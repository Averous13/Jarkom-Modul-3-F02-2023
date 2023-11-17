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

Untuk membuat dns server kita perlu melakukan instalasi package bind9 pada DNS Server
` `



