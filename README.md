# Jarkom-Modul-3-B18-2023

## Kelompok B18

|          Nama          |    NRP     |
| :--------------------: | :--------: |
|  Aryan Shafa Wardana   | 5025211031 |
| Shazia Ingeyla Naveeda | 5025211203 |

## Nomor 0
### Soal
Setelah mengalahkan Demon King, perjalanan berlanjut. Kali ini, kalian diminta untuk melakukan register domain berupa riegel.canyon.yyy.com untuk worker Laravel dan granz.channel.yyy.com untuk worker PHP mengarah pada worker yang memiliki IP [prefix IP].x.1.

##### Penyelesaian
Pada node `DNS Server` lakukan konfigurasi zone untuk domain dengan menggunakan bind9. Pada `/etc/bind/named.conf.local` akan tampak sebagai berikut
![img]()

Lalu pada directory `/etc/bind/hero` akan terdapat dua file yaitu

- `/etc/bind/hero/canyon.b18.com`
![img]()
- `/etc/bind/hero/channel.b18.com`
![img]()

## Nomor 1
### Soal
Lakukan konfigurasi sesuai dengan peta yang sudah diberikan.

Kemudian, karena masih banyak spell yang harus dikumpulkan, bantulah para petualang untuk memenuhi kriteria berikut.:
1. Semua CLIENT harus menggunakan konfigurasi dari DHCP Server.

#### Penyelesaian
Berikut gambar topologi
![img]()
Lalu pada setiap client dilakukan konfigurasi pada network settingnya dengan setiap client berisikan
```sh
auto eth0
iface eth0 inet dhcp
```

## Nomor 2 3 5
### Soal
2. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.16 - [prefix IP].3.32 dan [prefix IP].3.64 - [prefix IP].3.80
3. Client yang melalui Switch4 mendapatkan range IP dari [prefix IP].4.12 - [prefix IP].4.20 dan [prefix IP].4.160 - [prefix IP].4.168
5. Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch3 selama 3 menit sedangkan pada client yang melalui Switch4 selama 12 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 96 menit

#### Penyelesaian
Karena DHCP Server tidak terhubung dengan client secara langsung maka lakukan juga konfigurasi untuk DHCP Relay yaitu pada router. Pada DHCP relay lakukan konfigurasi pada `/etc/default/isc-dhcp-relay` dan akan tampak sebagai
![img]()
dan pada `/etc/sysctl.conf`, untuk comment pada
```sh
net.ipv4.ip_forward=1
```
Lakukan konfigurasi pada file `/etc/default/isc-dhcp-server` dan akan tampak seperti
![img]()

Lakukan konfigurasi pada file `/etc/dhcp/dhcpd.conf` dan akan tampak seperti
![img]()

## Nomor 4
### Soal
4. Client mendapatkan DNS dari Heiter dan dapat terhubung dengan internet melalui DNS tersebut

#### Penyelesaian
Lakukan konfigurasi pada DNS Master pada file `/etc/bind/named.conf.options` untuk melakukan forwarder sehingga dapat connect ke internet
![img]()

## Nomor 6
### Soal
Pada masing-masing worker PHP, lakukan konfigurasi virtual host untuk website berikut dengan menggunakan php 7.3.

##### Penyelesaian
Pada setiap php worker, lakukan instalasi dengan script berikut
```sh
apt-get install php -y
apt-get install php php-fpm -y
apt-get install nginx -y
pt-get install wget -y
apt-get install unzip -y
wget -O '/var/www/granz.channel.b18.com' 'https://drive.google.com/u/0/uc?id=1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1&export=download'
unzip -o /var/www/granz.channel.b18.com -d /var/www/
rm /var/www/granz.channel.b18.com
mv /var/www/modul-3 /var/www/granz.channel.b18.com
```
Lalu lakukan konfigurasi pada `/etc/nginx/sites-available/granz.channel.b18.com` dan tampak seperti ini
![img]()

### Nomor 7
Kepala suku dari Bredt Region memberikan resource server sebagai berikut:
- Lawine, 4GB, 2vCPU, dan 80 GB SSD.
- Linie, 2GB, 2vCPU, dan 50 GB SSD.
- Lugner, 1GB, 1vCPU, dan 25 GB SSD.

aturlah agar Eisen dapat bekerja dengan maksimal, lalu lakukan testing dengan 1000 request dan 100 request/second.

#### Penyelesaian
Menggunakan Weighted Round Robin maka server yang memiliki weight paling besar akan dijadikan prioritas ketika menerima request dari client. Pada node Eisen jalankan perintah berikut 
```sh
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt install nginx php php-fpm -y

nano /etc/nginx/sites-available/lb-eisen

(((setelah mengedit konfigurasi)))
ln -s /etc/nginx/sites-available/lb-eisen /etc/nginx/sites-enabled
rm -r /etc/nginx/sites-enabled/default
service nginx start
```
Tambahkan konfigurasi berikut
```
upstream myweb {
    server 192.187.3.1 weight=1;
    server 192.187.3.2 weight=2;
    server 192.187.3.3 weight=4;
}

server {
    listen 80;
    server_name 192.187.2.2;
    location / {
        proxy_pass http://myweb;
        }
```

Lalu pada node Heiter lakukan konfigurasi pada ``/etc/bind/jarkom/granz.channel.b17.com`` dan arahklan IP ke Load Balancer ``10.17.2.2`` dan restart bind9
```sh
service bind9 restart
```

**Lakukan testing pada salah satu client**
```sh
apt-get install apache2
service apache2 start
ab -n 1000 -c 100 192.187.2.2/ 
```
foto hasil

## Nomor 8
### Soal
Karena diminta untuk menuliskan grimoire, buatlah analisis hasil testing dengan 200 request dan 10 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:
- Nama Algoritma Load Balancer
- Report hasil testing pada Apache Benchmark
- Grafik request per second untuk masing masing algoritma. 
- Analisis

#### Penyelesaian
**Lakukan testing pada salah satu client**
```sh
ab -n 200 -c 10 192.187.2.2/ 
```

- Algo Round Robin ( 691.12 [#/sec] (mean) )
foto

- Algo Least Connection (  635.47 [#/sec] (mean) )
foto

- Algo IP Hash (  684.04 [#/sec] (mean) )
foto

- Algo Generic Hash ( 638.46 [#/sec] (mean) )
foto

**Grafik request per second**
foto

## Nomor 9
### Soal
Dengan menggunakan algoritma Round Robin, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 100 request dengan 10 request/second, kemudian tambahkan grafiknya pada grimoire.

#### Penyelesaian
**Lakukan testing pada salah satu client**
```sh
ab -n 100 -c 10 192.187.2.2/ 
```

- 3 Worker
foto
- 2 Worker
foto
- 1 Worker
foto

**Grafik**
foto

## Nomor 10
### Soal
Selanjutnya coba tambahkan konfigurasi autentikasi di LB dengan dengan kombinasi username: “netics” dan password: “ajkyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/rahasisakita/

#### Penyelesaian

Lakukan perintah berikut di node Eisen
```sh
mkdir /etc/nginx/rahasisakita
htpasswd -c /etc/nginx/rahasisakita/htpasswd netics
```

Lalu, masukkan passwordnya ``ajkb18``

Jika sudah memasukkan password dan re-type password. Sekarang bisa dicoba dengan menambahkan command berikut pada setup nginx

```sh
auth_basic "Administrator";
auth_basic_user_file /etc/nginx/rahasisakita/ .htpasswd;
```

**Lakukan testing pada salah satu client**
```sh
lynx 10 192.187.2.2
```
