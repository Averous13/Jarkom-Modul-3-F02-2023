# Jarkom-Modul-3-F02-2023

|NAMA|NRP|
|:--:|:-:|
|Moh Adib Syambudi|5025211017|

#### Menyusun topologi seperti gambar dengan ketentuan-ketentuan berikut 

|NODE|KATEGORI|KONFIGURASI IP|
|:--:|:--:|:--:|
|Aura|Router (DHCP Relay)|Dynamic|
|Himmel|DHCP Server|Static|
|Heiter|DNS Server|Static|
|Denken|Database Server|Static|
|Eisen|Load Balancer|Static|
|Frieren|Laravel Worker|Static|
|Flamme|Laravel Worker|Static|
|Fern|Laravel Worker|Static|
|Lawine|PHP Worker|Static|
|Linie|PHP Worker|Static|
|Lugner|PHP Worker|Static|
|Revolte|Client|Dynamic|
|Richter|Client|Dynamic|
|Sein|Client|Dynamic|
|Stark|Client|Dynamic|

#### 1 Setelah mengalahkan Demon King, perjalanan berlanjut. Kali ini, kalian diminta untuk melakukan register domain berupa riegel.canyon.yyy.com untuk worker Laravel dan granz.channel.yyy.com untuk worker PHP (0) mengarah pada worker yang memiliki IP [prefix IP].x.1. dan melakukan konfigurasi sesuai peta

Untuk membuat dns server kita perlu melakukan instalasi package bind9 pada DNS Server dan pastikan bahwa server telah mengarah sesuai dengan yang ada di router

``` 
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
```
Kemudian edit konfigurasi pada `/etc/bind/named.conf.local` untuk mendaftarkan dns yang akan digunakan  menjadi
```

zone "riegel.canyon.f02.com" {
        type master;
        file "/etc/bind/praktikum3/riegel.canyon.f02.com";
};

zone "granz.channel.f02.com" {
        type master;
        file "/etc/bind/praktikum3/granz.channel.f02.com";
};

```

Edit konfigurasi tiap dns pada directory praktikum3 dan sesuaikan nama file konfigurasi dengan dns agar mudah untuk diingat `/etc/bind/praktikum3/`

```

;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     granz.channel.f02.com. root.granz.channel.f02.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      granz.channel.f02.com.
@       IN      A       192.222.2.3
www     IN      CNAME   granz.channel.f02.com.

```

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     granz.channel.f02.com. root.granz.channel.f02.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      granz.channel.f02.com.
@       IN      A       192.222.2.3
www     IN      CNAME   granz.channel.f02.com.
```

Untuk mengaktifkan dns yang telah dikonfigurasi lakukan command
```
service bind9 restart
```

Testing dari dns akan dilakukan setelah pemberian ip dynamic dari dhcp yang akan dibuat

#### 2. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.16 - [prefix IP].3.32 dan [prefix IP].3.64 - [prefix IP].3.80 & 3 Melakukan instalasi dhcp server packages dengan command Client yang melalui Switch4 mendapatkan range IP dari [prefix IP].4.12 - [prefix IP].4.20 dan [prefix IP].4.160 - [prefix IP].4.168
```
apt-get install isc-dhcp-server
```

edit pengaturan kepada interface yang akan diberikan ip dynamic pada `/etc/default/isc-dhcp-server` dengan menuju eth0 kemudian lakukan pengaturan pada tiap subnet yang akan diberikan ip
dengan pengaturan pada `/etc/dhcp/dhcpd.conf`


Untuk subnet yang tidak kita berikan diatur sebagai berikut
```
subnet 192.222.1.0 netmask 255.255.255.0 {
}

subnet 192.222.2.0 netmask 255.255.255.0 {
}
```
Untuk subnet yang akan kita beri atur sebagai berikut

```
subnet 192.222.3.0 netmask 255.255.255.0 {
    range 192.222.3.16 192.222.3.32;
    range 192.222.3.64 192.222.3.80;
    option routers 192.222.3.1;
    option broadcast-address 192.222.3.255;
    option domain-name-servers 192.222.1.3;
    default-lease-time 180;
    max-lease-time 5760;
}

subnet 192.222.4.0 netmask 255.255.255.0 {
    range 192.222.4.12 192.222.4.20;
    range 192.222.4.160 192.222.4.168;
    option routers 192.222.4.1;
    option broadcast-address 192.222.4.255;
    option domain-name-servers 192.222.1.3;
    default-lease-time 720;
    max-lease-time 5760;
}
```

