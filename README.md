# Jarkom_Modul5_Lapres_T17 

<b> Laporan Resmi Praktikum Jaringan Komputer </b> <br>
Modul 5 - FIREWALL <br>
Kelompok T06
1. Millenia Ulwan Z                (05311840000020)
2. Bayu Trianayasa                 (05311840000038)

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## SOAL
<b>(A)</b> Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan Bibah seperti dibawah ini : <br>
![Topologi Modul 5](https://user-images.githubusercontent.com/61286109/102757895-8d583d80-43a4-11eb-971a-50f803ca820a.PNG) <br>
Keterangan : 
- <b>SURABAYA</b> diberikan <b>IP TUNTAP</b>
- <b>MALANG</b> merupakan <b>DNS Server</b> diberikan <b>IP DMZ</b>
- <b>MOJOKERTO</b> merupakan <b>DHCP Server</b> diberikan <b>IP DMZ</b>
- <b>MADIUN</b> dan <b>PROBOLINGGO</b> merupakan <b>WEB Server</b>
- <b>Setiap Server</b> diberikan memory sebesar <b>128M</b>
- <b>Client dan Router</b> diberikan memori sebesar <b>96M</b>
- Jumlah host pada subnet <b>SIDOARJO 200 Host</b>
- Jumlah host pada subnet <b>GRESIK 210 Host</b> 
<br>
<b>(B)</b> karena kalian telah mempelajari Subnetting dan Routing, Bibah meminta kalian untuk membuat topologi tersebut menggunakan teknik <b>CIDR</b> atau <b>VLSM</b>. Setelah melakukan subnetting, <b>(C)</b> kalian juga diharuskan melakukan routing agar setiap perangkat pada jaringan tersebut dapat terhubung. 
<br>
<br>
<b>(D)</b> Tugas berikutnya adalah memberikan ip pada subnet <b>SIDOARJO</b> dan <b>GRESIK</b> secara dinamis menggunakan bantuan DHCP SERVER (Selain subnet tersebut menggunakan ip static). Kemudian kalian mengingat bahwa kalian harus setting DHCP RELAY pada router yang menghubungkannya, seperti yang kalian telah pelajari di masa lalu. 
<br>
<br>
<b>(1)</b> Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi <b>SURABAYA</b> menggunakan iptables, namun Bibah tidak ingin kalian menggunakan MASQUERADE. 
<br>
<br>
<b>(2)</b> Kalian diminta untuk mendrop semua akses SSH dari luar Topologi (UML) Kalian pada server yang memiliki ip DMZ (DHCP dan DNS SERVER) pada <b>SURABAYA</b> demi menjaga keamanan. <br> <br>
<b>(3)</b> Karena tim kalian maksimal terdiri dari 3 orang, Bibah meminta kalian untuk membatasi DHCP dan DNS server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan yang berasal dari mana saja menggunakan <b>iptables pada masing masing server</b>, selebihnya akan di DROP. 
<br> <br>
kemudian kalian diminta untuk membatasi akses ke <b>MALANG</b> yang berasal dari SUBNET SIDOARJO dan SUBNET GRESIK dengan peraturan sebagai berikut: <br>
<b>(4)</b> Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin sampai Jumat. <br>
<b>(5)</b> Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap harinya. <br>
Selain itu paket akan di REJECT. 
<br> <br>
Karena kita memiliki 2 buah <b>WEB Server</b>, <b>(6)</b> Bibah ingin <b>SURABAYA</b> disetting sehingga setiap request dari client yang mengakses <b>DNS Server</b> akan didistribusikan <b>secara bergantian</b> pada <b>PROBOLINGGO</b> port 80 dan <b>MADIUN</b> port 80. 
<br> <br>
<b>(7)</b> Bibah ingin agar semua paket didrop oleh firewall (dalam topologi) tercatat dalam log pada setiap UML yang memiliki aturan drop. <br>
Bibah berterima kasih kepada kalian karena telah mau membantunya. Bibah juga mengingatkan agar semua aturan iptables harus disimpan pada sistem atau paling tidak kalian menyediakan script sebagai backup.

## PENYELESAIAN <br>
### Menggunakan Metode CIDR
#### Subnetting (Pembagian IP) <br>
- Menentukan subnet yang ada dalam topologi dan lakukan labelling netmask terhadap masing-masing subnet kemudian menggabungkan subnet-subnet sampai menjadi subnet besar. <br>
![SubnetTopologi Modul 5](https://user-images.githubusercontent.com/61286109/102895716-c7eed280-4497-11eb-9344-008e4d53366b.png) <br>
Hasil yang didapat adalah <b>Netmask /21</b> untuk subnet besar topologi diatas.
- Pembagian IP dengan pohon berdasarkan penggabungan subnet yang telah dilakukan.
![TreeCIDR Modul5](https://user-images.githubusercontent.com/61286109/102901541-58311580-44a0-11eb-9e8e-62e186f6eda4.png) <br>
- Dari pohon tersebut akan mendapat pembagian IP sebagai berikut. <br>
![Pembagian IP](https://user-images.githubusercontent.com/61286109/102895979-3fbcfd00-4498-11eb-8706-a60ed977048d.PNG) <br>

#### Konfigurasi Tiap UML
1. Pertama, membuat file `topologi.sh`.
![picture](https://cdn.discordapp.com/attachments/691272824765284362/793430599939129364/2.jpg) <br>

2. Pada semua UML Router, ke file `nano /etc/sysctl.conf` kemudian uncomment `net.ipv4.ip_forward=1` dan aktifkan dengan syntax `sysctl -p`. <br>

3. Lakukan setting interface pada semua UML di `nano /etc/network/interfaces` dan di restart dengan syntax `service networking restart`. <br>
- ROUTER SURABAYA <br>
- ROUTER KEDIRI <br>
- ROUTER BATU <br>
- SERVER MADIUN <br>
- SERVER PROBOLINGGO <br>
- SERVER MALANG <br>
- SERVER MOJOKERTO <br>
- KLIEN GRESIK <br>
- KLIEN SIDOARJO <br>
 ![picture](https://cdn.discordapp.com/attachments/691272824765284362/793430579609206794/1.png) <br>

4. Lakukan Routing terhadap Router Surabaya dengan membuat file dengan `nano routing.sh` untuk memudahkan kita karena pada saat setiap UML di restart maka routenya ke reset. Untuk menjalankannya ketik syntax `source routing.sh`. <br>

5. Install DHCP server, DHCP Relay, DNS server dan Web server
Jalankan perintah `apt-get update` sebelum menginstall pada UML

- MALANG (DNS Server) <br>
Gunakan command `apt-get install bind9 -y` untuk menginstall Bind9 <br>

- MOJOKERTO (DHCP Server) <br>
Gunakan command `apt-get install isc-dhcp-server` untuk menginstall DHCP Server <br>

- SURABAYA, BATU dan KEDIRI (DHCP Relay) <br>
Gunakan command `apt-get install isc-dhcp-relay` untuk menginstall DHCP Relay <br>

- MADIUN dan PROBOLINGGO (Web Server) <br>
Gunakan command `apt-get install apache2` untuk menginstall Web Server Apache <br>

6. Agar MOJOKERTO (DHCP Server) dapat berjalan dengan lancar, maka kita perlu melakukan deklarasi subnet yang terkoneksi pada MOJOKERTO yang diatur di `etc/dhcp/dhcpd.conf`. Terdapat subnet S2, A1 dan A4. <br>

![picture](https://cdn.discordapp.com/attachments/691272824765284362/793431014734823424/12.jpg) <br>

7. Agar DHCP Request dari Client SIDOARJO dan Client GRESIK dapat diteruskan ke DHCP Server MOJOKERTO, maka kita memerlukan DHCP Relay di ketiga router yaitu SURABAYA, KEDIRI dan BATU. Lakukan setting pada server dan interfaces dimana akan membantu DHCP Request agar dapat diteruskan dengan baik ke DHCP Server pada file `nano /etc/default/isc-dhcp-relay`<br>

DHCP Relay SURABAYA <br>
![picture](https://cdn.discordapp.com/attachments/691272824765284362/793430829145653288/8.jpg)

DHCP Relay BATU <br>
![picture](https://cdn.discordapp.com/attachments/691272824765284362/793430896572629012/10.jpg)

DHCP Relay KEDIRI <br>
![picture](https://cdn.discordapp.com/attachments/691272824765284362/793430856517025812/9.jpg)

8. DHCP Server
Interface dari server MOJOKERTO yang menuju ke client GRESIK dan SIDOARJO adalah <b>eth0</b>, maka kita akan memilih interface <b>eth0</b> untuk diberikan layanan DHCP.
Buka file konfigurasi interface dengan perintah `nano /etc/default/isc-dhcp-server`
```
INTERFACES="eth0"
```
9. Buat file `bye.sh`

#### NOMOR 1
Membuat file `nomor1.sh` UML SURABAYA yang berisikan script sebagai berikut: 
```
iptables -t nat -A POSTROUTING -s 192.168.0.0/21 -o eth0 -j SNAT --to-source 10.151.70.58
```
10.151.70.58 merupakan ip eth0 SURABAYA <br>
![picture](https://cdn.discordapp.com/attachments/691272824765284362/793430626161655808/3.jpg)

#### NOMOR 2
Membuat file `nomor2.sh` pada UML SURABAYA yang berisikan script sebagai berikut: 
```
iptables -A FORWARD -p tcp --dport 22 -d 10.151.71.112/29 -i eth0 -j DROP
```
10.151.71.112 merupakan IP DMZ <br>
![picture](https://cdn.discordapp.com/attachments/691272824765284362/793430710085484554/4.jpg)

#### NOMOR 3
Membatasi DHCP dan DNS server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan yang berasal dari mana saja. <br>
Membuat file `nomor3.sh` dan berikan script berikut:
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```
- UML Mojokerto <br>
![picture](https://cdn.discordapp.com/attachments/691272824765284362/793431052948471818/13.jpg)

- UML Malang <br>
![picture](https://cdn.discordapp.com/attachments/691272824765284362/793431119751675904/15.jpg)

#### NOMOR 4
Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin sampai Jumat, Selain itu di <b>reject</b>. <br>
Membuat file `nomor4.sh` yang berisikan script sebagai berikut: 
```
iptables -A INPUT -s 192.168.2.0/24 -m time --timestart 00:00 --timestop 06:59 --weekdays Mon,Tue,Wed,Thu,Fri -j REJECT
iptables -A INPUT -s 192.168.2.0/24 -m time --timestart 17:01 --timestop 23:59 --weekdays Mon,Tue,Wed,Thu,Fri -j REJECT
iptables -A INPUT -s 192.168.2.0/24 -m time --weekdays Sat,Sun -j REJECT
```
![picture](https://cdn.discordapp.com/attachments/691272824765284362/793431157852340234/16.jpg)

#### NOMOR 5
Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap harinya, Selain itu di <b>reject</b>.<br>
Membuat file `nomor5.sh` yang berisikan script sebagai berikut: 
```
iptables -A INPUT -s 192.168.0.0/24 -m time --timestart 07:01 --timestop 16:59 -j REJECT
```
![picture](https://cdn.discordapp.com/attachments/691272824765284362/793431186269536306/17.jpg)

#### NOMOR 6
Membuat file `nomor6.sh` pada UML SURABAYA yang berisikan script sebagai berikut: 
```
iptables -A PREROUTING -t nat -p tcp -d 10.151.71.114 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.168.1.2:80
iptables -A PREROUTING -t nat -p tcp -d 10.151.71.114 -j DNAT --to-destination 192.168.1.3:80
```
![picture](https://cdn.discordapp.com/attachments/691272824765284362/793430732218564618/5.jpg)

#### NOMOR 7
Membuat file `nomor7.sh` pada UML SURABAYA yang berisikan script LOGGING sebagai berikut:
```
iptables -N LOGGING
iptables -A FORWARD -d 10.151.71.112/29 -i eth0 -p tcp -m tcp --dport 22 -j LOGGING
iptables -A LOGGING -m limit --limit 2/min -j LOG --log-prefix "IPTables-Dropped: " --log-level 4
iptables -A LOGGING -j DROP
```
![picture](https://cdn.discordapp.com/attachments/691272824765284362/793430766184955918/6.jpg)

Membuat file `nomor7.sh` pada UML MALANG dan MOJOKERTO yang berisikan script LOGGING sebagai berikut:
```
iptables -N LOGGING
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j LOGGING
iptables -A LOGGING -m limit --limit 2/min -j LOG --log-prefix "IPTables-Dropped: " --log-level 4
iptables -A LOGGING -j DROP
```
![picture](https://cdn.discordapp.com/attachments/691272824765284362/793431237091131402/18.jpg) 

![picture](https://cdn.discordapp.com/attachments/691272824765284362/793431084578766848/14.jpg)
