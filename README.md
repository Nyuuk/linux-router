# Linux Router
menjadikan linux sebagai router tanpa dhcp server
## Topologi
- LAN (eth0) - mengkonfigurasi interface denga IP statis dalam subnet yang akan kita gunakan untuk jaringan lokal
- WAN (wwan0) - interface ini saya menggunakan modem usb stick Sierra 320U.
### IP Address
- LAN (eth0) - `192.168.123.10/24`
- WAN (wwan0) - DHCP CLIENT dari server operator GSM, menggunakan network manager `nmcli`
## Konfigurasi
### Step #1 - Menghidupkan ip forawrding di kernel
1. Buka linux kernel konfigurasi ( kamu harus login sebagai user root )
```sh
root@host:$ vim /etc/sysctl.conf
```
2. Modif/Tambah `net.ipv4.ip_forward=1` jika ada pagar di awal kata tersebut, maka hapus saja, jika tidak di temukan kata tersebut, maka tambah
### Step #2 - set IP Address
```sh
root@host:$ vim /etc/network/interfaces
```
```vim
source /etc/network/interfaces.d/*
auto lo
iface lo inet loopback
auto eth0
iface eth0 inet static
	address 192.168.123.10
	netmask 255.255.255.0
	network 192.168.123.0
	broadcast 192.168.123.255
```
### Step #3 - Menambahkan DNS
```sh
root@host:$ vim /etc/resolv.conf
```
```vim
nameserver 8.8.8.8
```
### Step #4 - Set IP Forwarding dan Masquerading (untuk menggunakan sebagai router), 
```sh
root@host:$ iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
root@host:$ iptables -A FORWARD -i eth0 -j ACCEPT
```
### Step #5 - Restart Network
```sh
root@host:$ /etc/init.d/networking restart
# atau
root@host:$ service networking restart
````
