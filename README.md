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
