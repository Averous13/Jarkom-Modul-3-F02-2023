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
Install package dhcp relay pada router dengan command 
```
apt-get install isc-dhcp-relay
```

Konfigurasi pada default menuju untuk server di isikan dengan ip dns server dan interfaces diisi dengan interface tujuan
```
SERVER="192.222.1.3"
INTERFACES="eth1 eth2 eth3 eth4"
```
Terakhir tambahkan pada  `/etc/sysctl.conf`

```
net.ipv4.ip_forward=1
```

dan kemudian restart dhcp relay untuk mengaktifkan konfigurasi yang telah dibuat

#### 4. Client mendapatkan DNS dari Heiter dan dapat terhubung dengan internet melalui DNS tersebut
Testing dicek pada client dengan melihat `etc/resolv.conf` dan ip a 

#### 5. Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch3 selama 3 menit sedangkan pada client yang melalui Switch4 selama 12 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 96 menit

Untuk pengaturan lama dari masa kadaluarsa dari ip telah kita atur pada dhcp server pada bagian pengaturan subnet. baris default-lease-time untuk peminjaman dan max-lease-time waktu maksimal

#### 6. Pada masing-masing worker PHP, lakukan konfigurasi virtual host untuk website berikut dengan menggunakan php 7.3.
Pengerjaan pembuatan worker PHP dengan melakukan langkah-langkah berikut:
- Melakukan instalasi nginx pada worker php

```
apt-get update
apt-get install nginx -y
apt-get install php -y
apt-get install php-fpm -y

```
- Setelah semua keperluan siap, unduh file zip yang telah disediakan soal dengan wget dan extract hasil unduhan.
- Hapus file yang telah diekstrak untuk menghemat penyimpanan dan ganti nama file hasil ekstrak dengan arjuna.f02.com
- Buat file konfigurasi pada `/etc/nginx/sites-available` dengan nama arjuna yang berisi seperti berikut:
```
'server {

	listen 800x;

	root /var/www/granz.channel.f02.com;

	index index.php index.html index.htm;
	server_name _;

	location / {
			try_files $uri $uri/ /index.php?$query_string;
	}

	# pass PHP scripts to FastCGI server
	location ~ \.php$ {
	include snippets/fastcgi-php.conf;
	fastcgi_pass unix:/run/php/php7.3-fpm.sock;
	}

location ~ /\.ht {
			deny all;
	}

	error_log /var/log/nginx/arjuna_error.log;
	access_log /var/log/nginx/arjuna_access.log;
}
```
- Port yang digunakan urut dari 8001-8003
- Nyalakan service dari php-fpm dengan `service php7.2-fpm start`
- buat symlink dengan command `ln -s /etc/nginx/sites-available/arjuna /etc/nginx/sites-enabled`
- restart nginx dan jalankan dengan command
```
service nginx restart
nginx -t
```

#### 7 Kepala suku dari Bredt Region memberikan resource server sebagai berikut: Lawine, 4GB, 2vCPU, dan 80 GB SSD.Linie, 2GB, 2vCPU, dan 50 GB SSD.Lugner 1GB, 1vCPU, dan 25 GB SSD. aturlah agar Eisen dapat bekerja dengan maksimal, lalu lakukan testing dengan 1000 request dan 100 request/second.

Pengujian menggunakan apache benchmark dengan instalasi apache2-utlis
``` 
apt-get install apache2-utils
```
Uji coba menggunakan command berikut
```
ab -n 1000 -c 100 http://www.granz.channel.d22.com/
```
#### 8 Karena diminta untuk menuliskan grimoire, buatlah analisis hasil testing dengan 200 request dan 10 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:
a. Nama Algoritma Load Balancer
b. Report hasil testing pada Apache Benchmark
c. Grafik request per second untuk masing masing algoritma.
d. Analisis

Round Robin
```
upstream myweb  {
        server 192.222.3.4:8001; #IP Lawine
        server 192.222.3.5:8002; #IP Linie
        server 192.222.3.6:8003; #IP Lugner
}

```


Lease Connection
```
#least connection
upstream myweb {
       least_conn;
       server 192.222.3.4:8001;
       server 192.222.3.5:8002;
       server 192.222.3.6:8003;
}
```
IP Hash
```
#upstream myweb {
       ip_hash;
       server 192.222.3.4:8001;
       server 192.222.3.5:8002;
        server 192.222.3.6:8003;
}
```

Generic Hash
```
#generichash
upstream myweb {
       hash $request_uri consistent;
       server 192.202.3.4:8001;
       server 192.202.3.5:8002;
       server 192.202.3.6:8003;
}
```
Uji coba menggunakan command berikut
```
ab -n 200 -c 10 http://www.granz.channel.d22.com/
```

#### 9 Dengan menggunakan algoritma Round Robin, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 100 request dengan 10 request/second, kemudian tambahkan grafiknya pada grimoire

Uji coba menggunakan command berikut
```
ab -n 100 -c 10 http://www.granz.channel.d22.com/
```
Dengan mengatur setiap uji coba dengan memberikan comment pada pada beberapa worker untuk penggunaan 3 worker, 2worker dan 1 worker

#### 10 Selanjutnya coba tambahkan konfigurasi autentikasi di LB dengan dengan kombinasi username: “netics” dan password: “ajkyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/rahasisakita/

Membuat folder untuk menyimpan user dan password
```
mkdir /etc/nginx/rahasisakita
apt-get install apache2-utils
htpasswd -c -b /etc/nginx/rahasisakita/htpasswd netics ajkf02
```
Tambahkan pada pengaturan load balancer 

```
location / {
 	    proxy_pass http://backend;
      auth_basic \"Autitenikasi\";
      auth_basic_user_file /etc/nginx/rahasisakita/htpasswd;
}
```

#### 11 Lalu buat untuk setiap request yang mengandung /its akan di proxy passing menuju halaman https://www.its.ac.id

Tambahkan konfigurasi berikut pada `etc/nginx/sites-available/granz`

```
location ~ /its {
    proxy_pass https://www.its.ac.id;
}
```

Testing menggunakan
```
lynx granz.channel.f02.com
```

#### 12 Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].3.69, [Prefix IP].3.70, [Prefix IP].4.167, dan [Prefix IP].4.168

Tambahkan konfigurasi berikut pada `etc/nginx/sites-available/granz`

```
location / {
        allow 192.202.3.69;
        allow 192.202.3.70;
        allow 192.202.4.167;
        allow 192.202.4.168;
        deny all;
	 	    proxy_pass http://backend;
        auth_basic \"Autitentikasi\";
        auth_basic_user_file /etc/nginx/rahasisakita/htpasswd;
```

 	}
