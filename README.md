# Jarkom-Modul-1-2026-K-11

## 1. Mempersiapkan pembangunan The Wired
![[image.png]]

## 2. Konfigurasi Router Lain agar bisa terhubung ke internet
![[image-1.png]]

Konfigurasi Router Lain:
```
auto eth0
iface eth0 inet dhcp
```
![[image-2.png]]

Testing dengan `ping`:
![[image-3.png]]

## 3. Menghubungkan Client satu sama lain
Konfigurasi Alice:
```
auto eth0
iface eth0 inet static
address 10.10.1.2
netmask 255.255.255.0
gateway 10.10.1.1
```

Konfigurasi Mika:
```
auto eth0
iface eth0 inet static
address 10.10.1.3
netmask 255.255.255.0
gateway 10.10.1.1
```

Konfigurasi Chisa:
```
auto eth0
iface eth0 inet static
address 10.10.2.2
netmask 255.255.255.0
gateway 10.10.2.1
```

Konfigurasi Knight:
```
auto eth0
iface eth0 inet static
address 10.10.3.2
netmask 255.255.255.0
gateway 10.10.2.1
```

Konfigurasi Eiri:
```
auto eth0
iface eth0 inet static
address 10.10.3.3
netmask 255.255.255.0
gateway 10.10.3.1
```

Testing `ping` dari `Knight` ke `Eiri`:
![[image-5.png]]

## 4. Menghubungkan Clients ke Internet
### Konfigurasi Source NAT
Karena alamat Clients (yang terletak pada konfigurasi Clients) merupakan *Private IP*, router harus menyamarkan IP Address sumber paket menjadi milik eth0, saat paket keluar menuju internet.

Konfigurasi dilakukan dengan menambahkan konfigurasi Router Lain:
```
auto eth0
iface eth0 inet dhcp
	up sysctl -w net.ipv4.ip_forward=1
	up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
	up iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
	up iptables -A FORWARD -i eth2 -o eth0 -j ACCEPT
	up iptables -A FORWARD -i eth0 -m state --state ESTABLISHED,RELATED -j ACCEPT
  
auto eth1
iface eth1 inet static
		address 10.10.1.1
		netmask 255.255.255.0

auto eth2
iface eth2 inet static
		address 10.10.2.1
		netmask 255.255.255.0

auto eth3
iface eth3 inet static
		address 10.10.3.1
		netmask 255.255.255.0
```

### Konfigurasi DNS Resolver
Konfigurasi DNS Resolver dilakukan dengan run command melalui console Clients:
```sh
echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

Untuk memastikan konfigurasi DNS Resolver selalu benar, tambahkan konfigurasi baru ke dalam konfigurasi setiap Clients:

```
auto eth0
iface eth0 inet static
    address 10.10.1.2
    netmask 255.255.255.0
    gateway 10.10.1.1
    dns-nameservers 8.8.8.8 1.1.1.1 #---> konfigurasi DNS Resolver
```

Atau bisa juga seperti ini:
```
auto eth0
iface eth0 inet static
    address 10.10.1.2
    netmask 255.255.255.0
    gateway 10.10.1.1
	up echo "nameserver 8.8.8.8" >> /etc/resolv.conf #---> konfigurasi DNS Resolver
	up echo "nameserver 1.1.1.1" >> /etc/resolv.conf #---> konfigurasi DNS Resolver
```

`ping` ke google.com:
![[image-6.png]]

## 5. Script verifikasi (Lain)
[[cek_status.sh]]:
```sh
#!/bin/bash

ip -br a
iptables -t nat -L -v -n
```

buat script menjadi executable:
```sh
chmod +x cek_status.sh
```

jalankan script:
```sh
./cek_status.sh
```

Output:
![[image-4.png]]

## 6. Deteksi traffic
Jalankan [Generator traffic](https://drive.google.com/drive/folders/1ZjFvWIjvAQAjE9pPthm7V_bGyaSt93lY?usp=sharing):
```sh
chmod +x traffic_protocol7.sh && ./traffic_protocol7.sh
```

Output:
![[image-7.png]]

Packet sniffing menggunakan wireshark, dengan filter `dns or icmp` :
![[image-10.png]]
![[image-11.png]]

## 7. Setup FTP Server

## 8. Upload file ke FTP Server Chisa

## 9. Uji coba akses FTP Server

## 10. Uji ketahanan koneksi