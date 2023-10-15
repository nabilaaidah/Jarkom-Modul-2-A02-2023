# Jarkom-Modul-2-A02-2023

<table>
    <tr>
        <th colspan=2> Kelompok A02 </th>
    </tr>
    <tr>
        <th>NRP</th>
        <th>Nama Anggota</th>
    </tr>
    <tr>
        <td>5025211032</td>
        <td>Nabila A'idah Diani</td>
    </tr>
</table>

# Nomor 1

Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut 

## Jawaban

Dikarenakan saya mendapat nomor topologi 6, maka topologi yang saya bentuk adalah sebagai berikut

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/dbf6fcf5-0017-4e28-a51f-75e35182dff0)

Konfigurasi ip dari masing - masing node adalah sebagai berikut:

#### Router

- Pandudewanata

```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.0.1.1
	netmask 255.255.255.0
	
auto eth2
iface eth2 inet static
	address 10.0.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.0.3.1
	netmask 255.255.255.0
```

#### DNS Master

-  Yudhistira

```
auto eth0
iface eth0 inet static
	address 10.0.1.4
	netmask 255.255.255.0
    gateway 10.0.1.1
```

#### DNS Slave

- Werkudara
  
```
auto eth0
iface eth0 inet static
	address 10.0.1.5
	netmask 255.255.255.0
	gateway 10.0.1.1
```

#### Client

- Nakula

```
auto eth0
iface eth0 inet static
	address 10.0.1.2
	netmask 255.255.255.0
	gateway 10.0.1.1
```

- Sadewa

```
auto eth0
iface eth0 inet static
	address 10.0.1.3
	netmask 255.255.255.0
	gateway 10.0.1.1
```

#### Load Balancer

- Arjuna

```
auto eth0
iface eth0 inet static
	address 10.0.2.2
	netmask 255.255.255.0
	gateway 10.0.2.1
```

#### Webserver

- Prabukusuma

```
auto eth0
iface eth0 inet static
	address 10.0.3.2
	netmask 255.255.255.0
	gateway 10.0.3.1
```

- Abimanyu

```
auto eth0
iface eth0 inet static
	address 10.0.3.3
	netmask 255.255.255.0
	gateway 10.0.3.1
```

- Wisanggeni

```
auto eth0
iface eth0 inet static
	address 10.0.3.4
	netmask 255.255.255.0
	gateway 10.0.3.1
``` 


# Nomor 2

Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

## Jawaban

Dikarenakan ini adalah soal pertama, maka perlu dilakukan update dan install bind9. Dalam melakukan update dan instalasi maka nameserver DNS Master dihubungkan dengan router, yaitu sebagai berikut

```
echo 'nameserver 192.168.122.1' > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
service bind9 start
```

Setelah itu, karena ingin membuat website utama, maka perlu dilakukan mendefinisikan zona DNS yang akan diatur pada file `named.conf.local`. Dikarenakan ingin membuat website utama, yaitu `arjuna.yyy.com`, maka dibuatlah zone dalam `/etc/bind/named.conf.local` dengan code sebagai berikut:

```
zone "arjuna.A02.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.A02.com";
};
```
Penjelasan:
- zone "arjuna.A02.com" adalah definisi zona DNS
- type master berarti server DNS ini adalah zona master untuk domain tersebut
- file "/etc/bind/jarkom/arjuna.A02.com" berarti lokasi di mana file zona DNS disimpan

Setelah itu, masukkan konfigurasi dari zona DNS tersebut ke dalam `/etc/bind/jarkom/arjuna.A02.com`.

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.A02.com. root.arjuna.A02.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      arjuna.A02.com.
@       IN      A       10.0.2.2
www     IN      CNAME   arjuna.A02.com.
```
Penjelasan:
- `$TTL    604800` berarti bahwa waktu yang menunjukkan berapa lama catatan DNS dapat disimpan dalam cache, dalam case ini adalah 604800 detik atau 7 hari
- SOA berarti bahwa pendefinisian otoritas dan parameter
- `@       IN      NS      arjuna.A02.com.` berarti bahwa nameserver ini bertanggung jawab untuk domain "arjuna.A02.com"
- `@ IN A 10.0.2.2` berarti bahwa catatan A (address) menghubungkan alamat IP 10.0.2.2 (Arjuna) ke nama domain "@" atau "arjuna.A02.com"
- `www     IN      CNAME   arjuna.A02.com.` berarti bahwa CMAME menghubungkan nama subdomain "www" ke domain "arjuna.A02.com" sehingga jika diakses "www.arjuna.A02.com", maka akan mengarah ke dalam "arjuna.A02.com"

Setelah itu lakukan:

```
service bind9 restart
```

Hasilnya dapat dilihat melalui client dengan melakukan setting resolv.conf terlebih dahulu ke arah IP DNS Master lalu untuk melakukan tes hasil, dapat diketikkan

```
ping arjuna.A02.com -c 5
```

atau

```
ping www.arjuna.A02.com -c 5
```

Hasil:

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/8a3f3771-18f8-4bc6-9b89-2913bdb41b35)


# Nomor 3

Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.

## Jawaban

Cara menjawab nomor 3 sama seperti nomor 2, namun perbedaannya hanya di domain yang akan diakses.

Definisikan zone ke dalam `/etc/bind/named.conf.local`

```
zone "abimanyu.A02.com" {
        type master;
        file "/etc/bind/jarkom/abimanyu.A02.com";
};
```

Setelah itu, masukkan konfigurasi domain tersebut ke dalam file `/etc/bind/jarkom/abimanyu.A02.com`

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.A02.com. root.abimanyu.A02.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.A02.com.
@       IN      A       10.0.3.3
www     IN      CNAME   abimanyu.A02.com.
```

Selanjutnya, jalankan command `service bind9 restart`

Lalu, untuk melihat hasil dapat dilakukan di node client dengan mengetiikan command sebagai berikut

```
ping abimanyu.A02.com -c 5
```

atau

```
ping www.abimanyu.A02.com -c 5
```

Hasilnya adalah sebagai berikut

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/b3b41b7c-879a-4997-b7d3-8922e6754004)


# Nomor 4

Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

## Jawaban

Untuk membenruk subdomain yang telah ada, maka kita perlu mengedit file `/etc/bind/jarkom/abimanyu.A02.com` dan menambahkan subdomain "parikesit" di dalamnya.

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.A02.com. root.abimanyu.A02.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@              IN      NS      abimanyu.A02.com.
@              IN      A       10.0.3.3
www            IN      CNAME   abimanyu.a07.com.
parikesit      IN      A       10.0.3.3
```
Penjelasan:
- Pada code ini, ditambahkan subdomain "parikesit"
- `parikesit      IN      A       10.0.3.3` berarti bahwa subdomain "parikesit" akan dihubungkan dengan IP 10.0.3.3

Lalu, jalankan command `service bind9 restart`

Setelah itu, dapat dilihat hasilnya dalam node client. Untuk command yang digunakan adalah
```
ping parikesit.abimanyu.A02.com -c 5
```

Hasilnya adalah sebagai berikut

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/b5af2085-d932-4874-89fa-e085b09b495f)



# Nomor 5

Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

## Jawaban

Reverse domain digunakan untuk menerjemahkan alamat IP menjadi nama domain. Untuk membentuk reverse domain, maka diperlukan adanya pendefinisian zone baru dalam `/etc/bin/named.conf.local`. Definisi zone baru tersebut adalah sebagai berikut:
```
zone "2.172.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.172.192.in-addr.arpa";
};
```
Penjelasan:
- Dalam zone ini, kita mengetahui bahwa terdapat ip domain yang dibalik, hal ini digunakan untuk memetakan alamat IP ke nama domain dalam struktur hierarkis DNS.
- Penulisan in-addr.arpa digunakan untuk reverse domain

Setelah itu, hal yang dilakukan adalah memuat konfigurasi ke dalam `/etc/bind/jarkom/3.0.10.in-addr.arpa.`. Konfigurasi yang akan dimuat adalah sebagai berikut
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.A02.com. root.arjuna.A02.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
2.0.10.in-addr.arpa.   IN      NS      arjuna.A02.com.
2                      IN      PTR     arjuna.A02.com.
```
Penjelasan:
- Pada code ini dapat diketahui bahwa IP 10.0.2.2 merujuk ke server "arjuna.A02.com"

Lalu, jalankan command `service bind9 restart`

Untuk melihat hasilnya di client, maka dapat dilakukan
```
echo 'nameserver 192.168.122.1 > /etc/resolv.conf'
apt-get update
apt-get install dnsutils
echo 'nameserver 10.0.1.4 > /etc/resolv.conf'
host -t PTR 10.0.3.3
```

Hasil yang didapatkan adalah sebagai berikut

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/2ec93fd9-c529-40b5-8f29-72ee04c8cad4)


# Nomor 6

Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

## Jawaban

Pertama, edit konfigurasi pada `/etc/bind/named.conf.local` utamanya pada zone arjuna dan abimanyu menjadi seperti dibawah ini:
```
zone "arjuna.A02.com" {
        type master;
        notify yes;
        also-notify { 10.0.1.5; }; // IP Werkudara
        allow-transfer { 10.0.1.1; }; // IP Werkudara
        file "/etc/bind/jarkom/arjuna.A02.com";
};

zone "abimanyu.A02.com" {
        type master;
        notify yes;
        also-notify { 10.0.1.5; }; // IP Werkudara
        allow-transfer { 10.0.1.5; }; // IP Werkudara
        file "/etc/bind/jarkom/abimanyu.A02.com";
};
```
Penjelasan:
- `notify yes` berfungsi untuk memberi tahu server lain yang berhubungan jika terjadi perubahan
- `also notify` berfungsi untuk memberi tahu server tertentu jika terjadi perubahan
- `allow-transfer` berfungsi untuk server tertentu diizinkan mengambil salinan zona ini

Lalu, jalankan command `service bind9 restart`

Selanjutnya, pergi ke DNS Slave. Pada DNS Slave, jalankan command di bawah ini:
```
echo 'nameserver 192.168.122.1` > /etc/resolv.conf

apt-get update
apt-get install bind9 -y
```

Setelah itu, isikan command di bawah ini ke dalam `/etc/bind/named.conf.local`:
```
zone "arjuna.A02.com" {
        type slave;
       	masters { 10.0.1.4; }; // IP Yudhistira
        file "/var/lib/bind/arjuna.A02.com";
};

zone "abimanyu.A02.com" {
        type slave;
       	masters { 10.0.1.4; }; // IP Yudhistira
         file "/var/lib/bind/abimanyu.A02.com";
};
```
Lalu, jalankan command `service bind9 restart`

Untuk melihat hasil runnya, maka jalankan command `service bind9 stop` di dalam DNS Master. Setelah itu, pergi ke Client, dan jalankan command berikut:
```
echo 'nameserver 10.0.1.5' > /etc/resolv.conf
```
Langkah berikutnya, adalah jalankan command berikut untuk melihat hasilnya
```
ping arjuna.A02.com -c 5
```
dan
```
ping www.arjuna.A02.com -c 5
```

Hasil yang didapatkan adalah sebagai berikut:

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/ec65f2da-2476-4eb3-a555-4b302c85ba2c)


Ps: Jangan lupa untuk menyalakan `service bind9 start` pada DNS Master dan `echo 'nameserver 10.0.1.4' > /etc/resolv.conf` pada Client


# Soal 7

Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

## Jawaban

Ubah konfigurasi `/etc/bind/jarkom/abimanyu.A02.com` pada DNS Master menjadi seperti berikut:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.A02.com. root.abimanyu.A02.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@              IN      NS      abimanyu.A02.com.
@              IN      A       10.0.3.3
www            IN      CNAME   abimanyu.A02.com.
parikesit      IN      A       10.0.3.3
ns1	       IN      A       10.0.1.5      
baratayuda     IN      NS      ns1
```
Penjelasan:
- `ns1	       IN      A       10.0.1.5` berarti bahwa subdomain ns1 berhubungan dengan alamat IP 10.0.1.5
- `baratayuda     IN      NS      ns1` berarti bahwa ns1 merupakan server DNS yang berwenang untuk subdomain "baratayuda"

Dikarenakan abimanyu akan didelegasikan ke DNS Slave, maka terdapat pengeditan konfigurasi di dalam `/etc/bind/named.conf.local` sebagai berikut:
```
zone "abimanyu.A02.com" {
        type master;
        allow-transfer { 10.0.1.5; }; // IP Werkudara
        file "/etc/bind/jarkom/abimanyu.A02.com";
};
```

Setelah itu, edit konfigurasi dari `/etc/bind/named.conf.options` menjadi seperti berikut:
1. Tambahkan `allow-query{any;};`
2. Comment `dnssec-validation auto;`

Setelah itu, lakukan `service bind9 restart`

Langkah berikutnya, adalah mengatur konfigurasi yang ada pada DNS Slave/Werkudara

Pertama, edit konfigurasi pada `/etc/bind/named.conf.options` enjadi seperti berikut:
1. Tambahkan `allow-query{any;};`
2. Comment `dnssec-validation auto;`

Setelah itu, edit konfigurasi yang ada pada `/etc/bind/Baratayuda/baratayuda.abimanyu.A02.com` menjadi seperti berikut:
```
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.A02.com. root.baratayuda.abimanyu.A02.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@                       IN      NS      baratayuda.abimanyu.A02.com.
@                       IN      A       10.0.3.3 ; IP Abimanyu
www                     IN      CNAME   baratayuda.abimanyu.A03.com.
```

Lalu, edit juga konfigurasi pada `/etc/bind/named.conf.local` seperti berikut:
```
zone "abimanyu.A02.com" {
        type master;
        file "/etc/bind/Baratayuda/baratayuda.abimanyu.A02.com";
};
```

Setelah itu, jalankan command `service bind9 restart`

Untuk melihat hasilnya, dapat dijalankan command berikut pada client:
```
ping baratayuda.abimanyu.A02.com -c 5
```
atau
```
ping www.baratayuda.abimanyu.A02.com -c 5
```

Hasilnya adalah sebagai berikut:

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/3f4e7081-f7d6-494b-8bb5-e9b724fc06f2)


# Nomor 8

Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

## Jawaban

Edit konfigurasi yang ada pada file `/etc/bind/Baratayuda/baratayuda.abimanyu.A02.com` di dalam DNS Slave/Werkudara sebagai berikut:
```
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.A02.com. root.baratayuda.abimanyu.A02.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@                       IN      NS      baratayuda.abimanyu.A02.com.
@                       IN      A       10.0.3.3 ; IP Abimanyu
www                     IN      CNAME   baratayuda.abimanyu.A02.com.
rjp                     IN      A       10.0.3.3 ; IP Abimanyu
www.rjp                 IN      CNAME   baratayuda.abimanyu.A02.com.
```

Setelah itu, jalankan `service bind9 restart`

Untuk melihat hasilnya, dapat dilakukan pada node client dengan menjalankan code sebagai berikut:
```
ping rjp.baratayuda.abimanyu.A02.com -c 5
```
atau
```
ping www.rjp.baratayuda.abimanyu.A02.com -c 5
```

Hasilnya adalah sebagai berikut:

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/1703858b-d116-4318-bc65-a363cd5c4661)


# Nomor 9

Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

## Jawaban

Pada nomor ini digunakan folder dari luar. Untuk menghubungkan folder tersebut dengan topologi yang ada di gns3, saya menggunakan github.

Pada node worker, dibuat konfigurasi seperti berikut:
```
echo nameserver 192.168.122.1 > /etc/resolv.conf

apt-get update && apt install nginx php php-fpm -y

php -v

mkdir /var/www/jarkom

apt-get install git -y

git -c http.sslVerify-false clone https://github.com/nabilaaidah/arjuna.yyy /var/www/jarkom

echo '
 server {
        listen 80;
        root /var/www/jarkom;
        index index.php index.html index.htm;
        server_name _;

        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }

        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        }

 location ~ /\.ht {
                        deny all;
        }

        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }
' > /etc/nginx/sites-available/jarkom

ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom

rm -rf /etc/nginx/sites-enabled/default

service php7.2-fpm start

service nginx reload

service nginx restart

nginx -t
```

Lalu, buat juga konfigurasi pada load balancer, sebagai berikut
```
echo nameserver 192.168.122.1 > /etc/resolv.conf

apt-get update
apt-get install nginx

echo ' # Default menggunakan Round Robin
 upstream myweb  {
  server 10.0.3.2; 
  server 10.0.3.3; 
  server 10.0.3.4;
 }

 server {
  listen 80;
  server_name arjuna.A02.com;

  location / {
  proxy_pass http://myweb;
  }
 }' > /etc/nginx/sites-available/lb-jarkom

ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled/lb-jarkom
```

Selanjutnya adalah melihat hasil pada client dengan lynx.
```
apt-get update
apt-get install lynx
```

Deploy index.php pada client dengan menjalankan command berikut
```
lynx [ip webserver]
```

Salah satu hasil pada node Prabukusuma:

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/fc648f4a-5178-4a9a-8e24-ed9c070ee675)


# Nomor 10

Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003

## Jawaban

Pertama, lakukan edit konfigurasi pada masing-masing webserver dengan menambahkan listen port. Contohnya adalah sebagai berikut pada webserver Abimanyu:
```
echo "nameserver 192.168.122.1" > /etc/resolv.conf

echo '
 server {
        listen 8002;
        root /var/www/jarkom;
        index index.php index.html index.htm;
        server_name _;

        location / {
                        try_files $uri $uri/ /index.php?$query_string;
        }

        # pass PHP scripts to FastCGI server
        location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        }

 location ~ /\.ht {
                        deny all;
        }

        error_log /var/log/nginx/jarkom_error.log;
        access_log /var/log/nginx/jarkom_access.log;
 }
' > /etc/nginx/sites-available/jarkom

service nginx restart
```

Setelah itu, edit juga konfigurasi yang ada pada load balancer menjadi seperti ini
```
echo ' # Default menggunakan Round Robin
 upstream myweb  {
  server 10.0.3.2:8001; 
  server 10.0.3.3:8002; 
  server 10.0.3.4:8003;
 }

 server {
  listen 80;
  server_name arjuna.A02.com;

  location / {
  proxy_pass http://myweb;
  }
 }' > /etc/nginx/sites-available/lb-jarkom
service nginx restart
```

Selanjutkan, lihat hasil melalui node client dengan mengetikkan command sebagai berikut untuk node Abimanyu:
```
lynx 10.0.3.3:8002
```

Dan hasilnya adalah sebagai berikut:

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/7dbbbef0-4e3c-4e55-870c-4bafafbba8f4)


# Soal 11

Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

## Jawaban

Edit konfigurasi pada node abimanyu sebagai berikut:
```
apt-get install apache2 -y

service apache2 start

apt-get install git -y

cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/abimanyu.A02.com.conf

echo '<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/abimanyu.A02
	ServerName abimanyu.A02.com
	ServerAlias www.abimanyu.A02.com

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/abimanyu.A02.com.conf

mkdir /var/www/abimanyu.A02

git -c http.sslVerify-false clone https://github.com/nabilaaidah/abimanyu.A02.git /var/www/abimanyu.A02

a2ensite abimanyu.A02.com

service apache2 restart
```

Setelah itu, dapat dilanjutkan dengan melihat hasil dengan menjalankan command berikut di node client:
```
lynx abimanyu.A02.com
```
dan
```
lynx www.abimanyu.A02.com
```

Hasilnya adalah sebagai berikut:

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/ad91fd4e-4eda-4891-b485-e427d47dd1fc)


# Nomor 12

Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.

## Jawaban

Edit konfigurasi yang ada pada webserver Abimanyu sebagai berikut:
```
a2enmod rewrite

service apache2 restart

echo 'RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule (.*) /index.php\/$1 [L]' > /var/www/abimanyu.A02./.htaccess
```
Penjelasan:
- `a2enmod rewrite` adalah perintah untuk mengaktifkan modul yang memungkinkan untuk URL rewriting dalam server web Apache
- bagian yang diecho adalah perintah untuk mengecek apakah permintaan file atau direktori yang ada, jika tidak mereka melakukan pemetaan ulang URL ke '/index.php/'


Konfigurasi berikut diberikan pada file `/etc/apache2/sites-available/abimanyu.A02.com.conf`
```
echo '<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/abimanyu.A02
	ServerName abimanyu.A02.com
	ServerAlias www.abimanyu.A02.com

	<Directory /var/www/abimanyu.A02>
		Options +FollowSymLinks -Multiviews
		AllowOverride All
	</Directory>

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/abimanyu.A02.com.conf
```
Penjelasan:
- `<Directory /var/www/abimanyu.A02>` merupakan blok konfigurasi yang dimulai dengan tag <Directory> dan menunjukkan direktori yang sedang dikonfigurasi, yaitu `/var/www/abimanyu.A02`
- `Options +FollowSymLinks -Multiviews` merupakan mengatur opsi Apache untuk mengizinkan tautan simbolik dan menonaktifkan pencarian alternatif file dengan nama serupa (multiviews) dalam direktori tersebut.
- `AllowOverride All` merupakan pengizinan pengaturan file '.htaccess' dalam direktori tersebut untuk mengganti konfigurasi server.

Lalu, untuk melihat hasil dapat menggunakan node client. Command yang dapat digunakan adalah seperti berikut
```
lynx www.abimanyu.A02.com/home
```
atau
```
curl www.abimanyu.A02.com/home
```

Berikut merupakan contoh dari penggunaan command dengan curl:

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/aeb15b55-25f3-49b3-949b-58cf56194297)


# Nomor 13

Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy

## Jawaban

Buat konfigurasi pada webserver Abimanyu pada file `/etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf`
```
echo '<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/parikesit.abimanyu.A02
	ServerName parikesit.abimanyu.A02.com
	ServerAlias www.parikesit.abimanyu.A02.com

	<Directory /var/www/abimanyu.A02>
		Options +FollowSymLinks -Multiviews
		AllowOverride All
	</Directory>

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf
```
Penjelasan:
- Pada konfigurasi ini, DocumentRoot diarahkan ke arah subdomain parikesit
- ServerName dan ServerAlias juga ditambahkan subdomain parikesit
- Dan juga code ini akan di-echo ke dalam file `/etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf`

Setelah itu jalankan command di bawah ini
```
a2ensite parikesit.abimanyu.A02
mkdir /var/www/parikesit.abimanyu.A02
git -c http.sslVerify-false clone https://github.com/nabilaaidah/parikesit.abimanyu.A02.git /var/www/parikesit.abimanyu.A02
service apache2 restart
```
Penjelasan:
- `a2ensite` digunakan untuk mengaktifkan konfigurasi situs web di server Apache.
- `mkdir` digunakan untuk membentuk direktori baru di dalam /var/www.
- `git -c http.sslVerify-false clone` digunakan untuk melakukan git clone repo ke dalam `/var/www/parikesit.abimanyu.A02`.
- `service apache2 restart` digunakan untuk melakukan restart apache2.

Lalu, lihat hasil run pada client, dengan menuliskan command sebagai berikut
```
lynx www.parikesit.abimanyu.A02.com
```

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/0fd636a4-68ec-4951-b543-96c057725c96)


# Nomor 14

Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).

## Jawaban

Lalu, edit konfigurasi yang telah dibuat pada file `/etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf` seperti berikut:
```
echo '<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/parikesit.abimanyu.A02
	ServerName parikesit.abimanyu.A02.com
	ServerAlias www.parikesit.abimanyu.A02.com

	<Directory /var/www/parikesit.abimanyu.A02/public>
		Options +Indexes
	</Directory>

	<Directory /var/www/parikesit.abimanyu.A02/secret>
		Options -Indexes
	</Directory>

	<Directory /var/www/abimanyu.A02>
		Options +FollowSymLinks -Multiviews
		AllowOverride All
	</Directory>

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf
```
Penjelasan:
```
<Directory /var/www/parikesit.abimanyu.A02/public>
	Options +Indexes
</Directory>
```
- Pada code ini, dapat diketahui bahwa folder public dapat dilihat dikarenakan opsi `Options +Indexes`
```
<Directory /var/www/parikesit.abimanyu.A02/secret>
	Options -Indexes
</Directory>
```
- Pada code ini, dapat diketahui bahwa folder secret ini tidak dapat dilihat dikarenakan opsi `Options -Indexes`


```
a2ensite parikesit.abimanyu.A02.com
servie apache2 restart
```
Penjelasan:
- `a2ensite` digunakan untuk mengaktifkan konfigurasi situs web di server Apache.
- `service apache2 restart` digunakan untuk melakukan restart apache2.

Hasilnya dapat dilihat dari node client dengan mengetikkan command:
```
lynx www.parikesit.abimanyu.A02.com
```

Hasilnya adalah sebagai berikut:
- Home dari webnya
  
![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/0b617247-311c-4bef-9919-2c1561b5fca5)

- Jika kita memilih folder yang dilarang
  
![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/8880cb19-6026-4d5f-93bd-7c1dfeab2405)


# Nomor 15

Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.

## Jawaban

Pada soal ini, diberikan perintah untuk memberikan kustom halaman error sehingga konfigurasi kode ada yang perlu diedit sebagai berikut:
```
echo '<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/parikesit.abimanyu.A02
	ServerName parikesit.abimanyu.A02.com
	ServerAlias www.parikesit.abimanyu.A02.com

	ErrorDocument 404 /error/404.html
	ErrorDocument 403 /error/403.html

	<Directory /var/www/parikesit.abimanyu.A02/public>
		Options +Indexes
	</Directory>

	<Directory /var/www/parikesit.abimanyu.A02/secret>
		Options -Indexes
	</Directory>

	<Directory /var/www/abimanyu.A02>
		Options +FollowSymLinks -Multiviews
		AllowOverride All
	</Directory>

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf
service bind9 apache
```
Penjelasan:
- Pada code ini, terdapat `ErrorDocument` yang akan mengarahkan pada error tertentu akan menampilkan file tertentu. Dalam code, error 404 akan menampilkan file 404.html yang terdapat pada folder error

Hasil dari code ini dapat dilihat dalam node client dengan menuliskan command sebagai berikut:
- Untuk menampilkan error 404, maka dapat menuliskan command `lynx www.parikesit.abimanyu.A02.com/hehehe`
  
![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/97e304bd-59f7-4047-891c-cdc85f000ae9)

- Untuk menampikan error 403, maka dapat menuliskan command 'lynx www.parikesit.abimanyu.A02.com/secret'
  
![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/dd49ae83-d81f-4c7b-9680-c49b8c7d1e44)


# Nomor 16

Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi 
www.parikesit.abimanyu.yyy.com/js

## Jawaban

Pada soal ini, diperintahkan untuk membuat alias dari `/public/js` menjadi hanya `/js`. Berikut adalah code yang digunakan:
```
echo '<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/parikesit.abimanyu.A02
	ServerName parikesit.abimanyu.A02.com
	ServerAlias www.parikesit.abimanyu.A02.com

	ErrorDocument 404 /error/404.html
	ErrorDocument 403 /error/403.html

	<Directory /var/www/parikesit.abimanyu.A02/public>
		Options +Indexes
	</Directory>

	<Directory /var/www/parikesit.abimanyu.A02/secret>
		Options -Indexes
	</Directory>

	<Directory /var/www/abimanyu.A02>
		Options +FollowSymLinks -Multiviews
		AllowOverride All
	</Directory>

	Alias "/js" "/var/www/parikesit.abimanyu.A02/public/js"

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf
service apache2 restart
```
Penjelasan:
- Untuk menjawab code ini, digunakan `Alias "/js" "/var/www/parikesit.abimanyu.A02/public/js"`, di mana akan memperpendek URL dari /public/js menjadi hanya /js
- Lakukan `service apache2 restart` untuk melakukan restart baru

Jalankan `lynx www.parikesit.abimanyu.A02.com/js` untuk melihat isi js pada URL tersebut. Command ini dijalankan pada node client. Berikut adalah hasil setelah dijalankan

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/679de04e-e5d0-47ed-a619-4ea6e0d68645)


# Nomor 17

Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.

## Jawaban

Pada soal ini, diperintahkan untuk membuat konfigurasi `www.rjp.baratayuda.abimanyu.yyy.com` hanya dapat diakses dengan port 14000 atau 14400 sehingga langkah pertama yang harus dilakukan adalah mengedit konfigurasi `/etc/apache2/sites-available/rjp.baratayuda.abimanyu.A02.com.conf` sebagai berikut:
```
echo '<VirtualHost *:14000>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/rjp.parikesit.abimanyu.A02
	ServerName rjp.parikesit.abimanyu.A02.com
	ServerAlias www.rjp.parikesit.abimanyu.A02.com

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf

echo '<VirtualHost *:14400>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/rjp.parikesit.abimanyu.A02
	ServerName rjp.parikesit.abimanyu.A02.com
	ServerAlias www.rjp.parikesit.abimanyu.A02.com

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' >> /etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf
```
Penjelasan:
- Pada code ini dituliskan terdapat dua VirtualHost dengan port yang berbeda, yaitu 14000 dan 14400 sesuai kebutuhan soal

Dan juga melakukan pengaturan Listen Port pada `/etc/apache2/ports.conf` dengan menambahkan kedua lines berikut ke dalamnya:
```
Listen 14000
Listen 14400
```

Selanjutnya, jalankan code di bawah ini
```
a2ensite rjp.baratayuda.abimanyu.A02.com
mkdir /var/www/rjp.baratayuda.abimanyu.A02
git -c http.sslVerify-false clone https://github.com/nabilaaidah/rjp.baratayuda.abimanyu.A02.git /var/www/rjp.baratayuda.abimanyu.A02
servie apache2 restart
```
Penjelasan:
- `a2ensite` digunakan untuk mengaktifkan konfigurasi situs web di server Apache.
- `mkdir` digunakan untuk membentuk direktori baru di dalam /var/www.
- `git -c http.sslVerify-false clone` digunakan untuk melakukan git clone repo ke dalam `/var/www/rjp.baratayuda.abimanyu.A02`.
- `service apache2 restart` digunakan untuk melakukan restart apache2.

Command untuk melihat hasilnya adalah
```
lynx www.rjp.baratayuda.abimanyu.A02.com:14000
```
dan
```
lynx www.rjp.baratayuda.abimanyu.A02.com:14400
```

Hasilnya adalah sebagai berikut:

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/69dc3081-19b8-408e-858d-d9b3c068f925)


# Nomor 18

Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.

## Jawaban

Untuk menjawab soal ini, maka diperlukan pengeditan konfigurasi pada `/etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf` sebagai berikut:
```
htpasswd -c -b /etc/apache2/.htpasswd Wayang baratayudayy
echo '<VirtualHost *:14000>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/rjp.parikesit.abimanyu.A02
	ServerName rjp.parikesit.abimanyu.A02.com
	ServerAlias www.rjp.parikesit.abimanyu.A02.com

	<Directory "/var/www/rjp.baratayuda.abimanyu.A02">
		AuthType Basic
		AuthName "Restricted Content"
		AuthUserFile /etc/aapache2/.htpasswd
		Rewuire valid-user
	</Directory>

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf

echo '<VirtualHost *:14400>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/rjp.parikesit.abimanyu.A02
	ServerName rjp.parikesit.abimanyu.A02.com
	ServerAlias www.rjp.parikesit.abimanyu.A02.com

	<Directory "/var/www/rjp.baratayuda.abimanyu.A02">
		AuthType Basic
		AuthName "Restricted Content"
		AuthUserFile /etc/aapache2/.htpasswd
		Rewuire valid-user
	</Directory>

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' >> /etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf

service apache2 restart
```
Penjelasan:
- `htpasswd -c -b /etc/apache2/.htpasswd Wayang baratayudayy` merupakan perintah htpasswd dengan opsi -c digunakan untuk membuat file htpasswd (yang digunakan untuk otentikasi HTTP) di lokasi /etc/apache2/.htpasswd dengan nama pengguna "Wayang" dan kata sandi "baratayudayy."
- AuthType Basic: Ini mengatur jenis otentikasi yang akan digunakan,
- AuthName "Restricted Content": Ini adalah pesan yang akan ditampilkan kepada pengguna ketika mereka diminta untuk memasukkan kredensial.
- AuthUserFile /etc/aapache2/.htpasswd: Ini mengacu pada lokasi file htpasswd yang digunakan untuk otentikasi.
- Require valid-user: Ini mengharuskan semua pengguna yang mencoba mengakses direktori ini
untuk memberikan kredensial yang valid.

Untuk melihat hasilnya, dapat diarahkan ke node client dan mengetikkan command `lynx www.rjp.baratayuda.abimanyu.A02.com`, dan hasilnya akan seperti berikut:

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/5b418a49-8a20-435b-a1ff-8bf2b5a74050)

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/f34c9cf6-c8e9-4881-b886-175c68cb9299)


# Nomor 19

Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)

## Jawaban

Untuk menjawab soal tersebut, maka kita perlu mengedit konfigurasi yang berada di file `/etc/apache2/sites-available/000-default.conf` dengan kode berikut:
```
echo '<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/html

	RewriteEngine On
	RewriteCond %{HTTP_HOST} !^abimanyu.A02.com$
	RewriteRule /.* http://abimanyu.A02.com/ [R]

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf
service apache2 restart
```
Penjelasan:
- RewriteEngine On: Ini mengaktifkan mod_rewrite, yang memungkinkan penggunaan aturan pemetaan ulang URL.
- RewriteCond %{HTTP_HOST} !^abimanyu.A02.com$: Ini adalah aturan kondisional yang memeriksa alamat host HTTP (HTTP_HOST). Jika alamat host tidak sama dengan "abimanyu.A02.com" (tanda "!" berarti "tidak sama dengan" atau "bukan"), maka aturan pemetaan ulang akan diterapkan.
- RewriteRule /.* http://abimanyu.A02.com/ [R]: Ini adalah aturan pemetaan ulang URL yang akan diterapkan jika kondisi sebelumnya terpenuhi.
1. /.* adalah pola yang cocok dengan semua alamat URL yang diminta.
2. http://abimanyu.A02.com/ adalah alamat URL tujuan yang akan diarahkan.
3. [R] menginstruksikan untuk melakukan pemetaan ulang dengan status kode HTTP 302 (Found). Ini akan mengarahkan permintaan ke "abimanyu.A02.com."

Hasil dari soal ini dapat dilihat dalam node client dengan mengetikan
```
curl 10.0.3.3
```
atau
```
lynx 10.0.3.3
```

Berikut adalah hasil curl-nya:

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/c8d731e0-9ead-431b-9369-785508cdb9bc)


# Nomor 20

Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.

## Jawaban

Untuk menjawab soal tersebut, maka perlu diaktifkan modul rewrite sebagai berikut:
```
echo 'RewriteEngine On
RewriteCond %{REQUEST_URI} ^/public/images/(.*)abimanyu(.*)
RewriteCond %{REQUEST_URI} !/public/images/abimanyu.png
RewriteRule /.* http://parikesit/abimanyu.A02.com/public/images/abimanyu.png [L]' > /var/www/parikesit.abimanyu.A02.com/.htaccess
```
Penjelasan:
- RewriteEngine On: Ini mengaktifkan mod_rewrite, yang memungkinkan penggunaan aturan pemetaan ulang URL.
- RewriteCond %{REQUEST_URI} ^/public/images/(.*)abimanyu(.*): Ini adalah aturan kondisional yang memeriksa URI permintaan (REQUEST_URI). Aturan ini memeriksa apakah URI permintaan mengandung kata "abimanyu." Jika ya, aturan pemetaan ulang akan diterapkan.
- RewriteCond %{REQUEST_URI} !/public/images/abimanyu.png: Ini adalah aturan kondisional kedua yang memeriksa apakah URI permintaan adalah "/public/images/abimanyu.png." Jika ya, aturan pemetaan ulang tidak akan diterapkan.
- RewriteRule /.* http://parikesit/abimanyu.A02.com/public/images/abimanyu.png [L]: Ini adalah aturan pemetaan ulang URL yang akan diterapkan jika kondisi sebelumnya terpenuhi.
1. /.* adalah pola yang cocok dengan semua alamat URL yang diminta.
2. http://parikesit/abimanyu.A02.com/public/images/abimanyu.png adalah alamat URL tujuan yang akan diarahkan jika kedua kondisi sebelumnya terpenuhi.
3. [L] menandakan bahwa pemetaan ulang ini adalah yang terakhir yang akan diterapkan jika kondisi terpenuhi.

Selain itu, juga terdapat edit konfigurasi pada file `/etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf` sebagai berikut:
```
echo '<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/parikesit.abimanyu.A02
	ServerName parikesit.abimanyu.A02.com
	ServerAlias www.parikesit.abimanyu.A02.com

	ErrorDocument 404 /error/404.html
	ErrorDocument 403 /error/403.html

	<Directory /var/www/parikesit.abimanyu.A02/public>
		Options +Indexes
	</Directory>

	<Directory /var/www/parikesit.abimanyu.A02/secret>
		Options -Indexes
	</Directory>

	<Directory /var/www/parikesit.abimanyu.A02>
		Options +FollowSymLinks -Multiviews
		AllowOverride All
	</Directory>

	Alias "/js" "/var/www/parikesit.abimanyu.A02/public/js"

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.A02.com.conf
service apache2 restart
```

Hasil dari code ini dapat dilihat dalam client dengan mengetikkan `lynx www.parikesit.abimanyu.A02.com/abimanyugantenk`

![image](https://github.com/nabilaaidah/Jarkom-Modul-2-A02-2023/assets/110476969/1862cfef-1c5f-4f16-8dfb-716c285e4aa8)


# Kendala
- Terminal GNS3 dan telnet yang tidak memadai untuk copy sehingga script tidak bisa disimpan di local
- Baru menyadari adanya folder .zip di hampir akhir praktikum sehingga harus mengulang dari awal
