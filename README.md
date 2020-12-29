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
![NanoTopologi](https://user-images.githubusercontent.com/61286109/102784082-73cbeb80-43ce-11eb-89e5-5bfb656220a9.PNG) <br>
2. Pada semua UML Router, ke file `nano /etc/sysctl.conf` kemudian uncomment `net.ipv4.ip_forward=1` dan aktifkan dengan syntax `sysctl -p`. <br>
3. Lakukan setting interface pada semua UML di `nano /etc/network/interfaces` dan di restart dengan syntax `service networking restart`. <br>
- ROUTER SURABAYA <br>
![iface surabaya](https://user-images.githubusercontent.com/61286109/102900032-55352580-449e-11eb-8844-8224bb6b9db2.PNG) <br>
- ROUTER KEDIRI <br>
![iface kediri](https://user-images.githubusercontent.com/61286109/102899925-3767c080-449e-11eb-8854-e8714623d1c4.PNG) <br>
- ROUTER BATU <br>
![iface batu](https://user-images.githubusercontent.com/61286109/102899633-d0e2a280-449d-11eb-95ed-e3ea0a41ca32.PNG) <br>
- SERVER MADIUN <br>
![iface madiun](https://user-images.githubusercontent.com/61286109/102902989-4badbc80-44a2-11eb-972d-58845f439e48.PNG) <br>
- SERVER PROBOLINGGO <br>
![iface probolinggo](https://user-images.githubusercontent.com/61286109/102902985-494b6280-44a2-11eb-9da6-de197f677452.PNG) <br>
- SERVER MALANG <br>
![iface malang](https://user-images.githubusercontent.com/61286109/102900739-4ef37900-449f-11eb-9809-13a3a6b25ac0.PNG) <br>
- SERVER MOJOKERTO <br>
![iface mojokerto](https://user-images.githubusercontent.com/61286109/102900727-4bf88880-449f-11eb-8bfb-4095fa168432.PNG) <br>
- KLIEN GRESIK <br>
![iface gresik](https://user-images.githubusercontent.com/61286109/102826852-0cd12580-4414-11eb-8710-1234c41f2c70.PNG) <br>
- KLIEN SIDOARJO <br>
![iface sidoarjo](https://user-images.githubusercontent.com/61286109/102826859-0fcc1600-4414-11eb-9b38-f57858a26aa6.PNG) <br>
4. Lakukan Routing terhadap Router Surabaya dengan membuat file dengan `nano routing.sh` untuk memudahkan kita karena pada saat setiap UML di restart maka routenya ke reset. Untuk menjalankannya ketik syntax `source routing.sh`.
- ROUTING SURABAYA <br>
![routing surabaya](https://user-images.githubusercontent.com/61286109/102912675-47889b80-44b0-11eb-9f89-5b2f4a1d4440.PNG) <br>
- ROUTING KEDIRI <br>
![routing kediri](https://user-images.githubusercontent.com/61286109/102912665-45bed800-44b0-11eb-9509-163626b42c1c.PNG) <br>

5. Install DHCP server, DHCP Relay, DNS server dan Web server
Jalankan perintah `apt-get update` sebelum menginstall pada UML
- MALANG (DNS Server) <br>
Gunakan command `apt-get install bind9 -y` untuk menginstall Bind9 <br>
<img width="364" alt="dns server" src="https://user-images.githubusercontent.com/26424136/102977306-63825080-4535-11eb-9e09-a4fd4849cc57.PNG"> <br>
- MOJOKERTO (DHCP Server) <br>
Gunakan command `apt-get install isc-dhcp-server` untuk menginstall DHCP Server <br>
<img width="369" alt="dhcp server" src="https://user-images.githubusercontent.com/26424136/102977301-61b88d00-4535-11eb-903e-588d5cc6c906.PNG"> <br>
- SURABAYA, BATU dan KEDIRI (DHCP Relay) <br>
Gunakan command `apt-get install isc-dhcp-relay` untuk menginstall DHCP Relay <br>
DHCP RELAY SURABAYA <br>
![relay surabaya](https://user-images.githubusercontent.com/61286109/103164621-4d7bd500-4840-11eb-9530-7a40f61e0276.PNG) <br>
DHCP RELAY BATU <br>
![relay batu](https://user-images.githubusercontent.com/61286109/103164623-4f459880-4840-11eb-9ee7-aa9a48f1528c.PNG) <br>
DHCP RELAY KEDIRI <br>
![relay kediri](https://user-images.githubusercontent.com/61286109/103164624-4fde2f00-4840-11eb-8df5-9f99771015b8.PNG) <br>
- MADIUN dan PROBOLINGGO (Web Server) <br>
Gunakan command `apt-get install apache2` untuk menginstall Web Server Apache <br>
WEB SERVER PROBOLINGGO <br>
![web server probolinggo](https://user-images.githubusercontent.com/61286109/103164757-bc0d6280-4841-11eb-9a3d-82902ef82032.PNG) <br>
WEB SERVER MADIUN <br>
![web server madiun](https://user-images.githubusercontent.com/61286109/103164756-ba439f00-4841-11eb-836b-1eb0b62ecc04.PNG) <br>

6. Agar MOJOKERTO (DHCP Server) dapat berjalan dengan lancar, maka kita perlu melakukan deklarasi subnet yang terkoneksi pada MOJOKERTO yang diatur di `etc/dhcp/dhcpd.conf`. Terdapat subnet S2, A1 dan A4. <br>
<img width="370" alt="dhcpconf1" src="https://user-images.githubusercontent.com/26424136/103148185-bce5bc00-478f-11eb-8a09-48acd27410da.PNG"> <br>
<img width="366" alt="dhcpconf2" src="https://user-images.githubusercontent.com/26424136/103148187-beaf7f80-478f-11eb-8f05-2114794e9e28.PNG"> <br>
7. Agar DHCP Request dari Client SIDOARJO dan Client GRESIK dapat diteruskan ke DHCP Server MOJOKERTO, maka kita memerlukan DHCP Relay di ketiga router yaitu SURABAYA, KEDIRI dan BATU. Lakukan setting pada server dan interfaces dimana akan membantu DHCP Request agar dapat diteruskan dengan baik ke DHCP Server pada file `nano /etc/default/isc-dhcp-relay`<br>
DHCP Relay SURABAYA <br>
<img width="371" alt="relaysby" src="https://user-images.githubusercontent.com/26424136/103148611-3089c800-4794-11eb-9081-c525527f5bfc.PNG"> <br>
DHCP Relay BATU <br>
<img width="370" alt="relaybatu" src="https://user-images.githubusercontent.com/26424136/103148606-2ec00480-4794-11eb-8c44-94f1047bf0f7.PNG"> <br>
DHCP Relay KEDIRI <br>
<img width="370" alt="relaykediri" src="https://user-images.githubusercontent.com/26424136/103148609-2ff13180-4794-11eb-90bf-509a83cea13d.PNG"> <br>
8. DHCP Server
Interface dari server MOJOKERTO yang menuju ke client GRESIK dan SIDOARJO adalah <b>eth0</b>, maka kita akan memilih interface <b>eth0</b> untuk diberikan layanan DHCP.
Buka file konfigurasi interface dengan perintah `nano /etc/default/isc-dhcp-server`
```
INTERFACES="eth0"
```
<img width="369" alt="dhcp server (2)" src="https://user-images.githubusercontent.com/26424136/103210086-3c61bf80-4937-11eb-950d-a785c001f0ef.PNG"> <br>
9. Buat file `bye.sh`
![bye](https://user-images.githubusercontent.com/26424136/103220026-ce76c180-4951-11eb-9c3b-dbf44da46a14.png)

#### NOMOR 1
Membuat file `nomor1.sh` UML SURABAYA yang berisikan script sebagai berikut: 
```
iptables -t nat -A POSTROUTING -s 192.168.0.0/21 -o eth0 -j SNAT --to-source 10.151.72.90
```
10.151.72.90 merupakan ip eth0 SURABAYA <br>
<img width="371" alt="no1" src="https://user-images.githubusercontent.com/26424136/103148188-bf481600-478f-11eb-9d6a-d25ea1d955c2.PNG">
##### Testing
Ping berhasil saat menggunakan iptables tersebut. <br>
![test1](https://user-images.githubusercontent.com/26424136/103148197-c53df700-478f-11eb-854c-385c32df1cc5.png)

#### NOMOR 2
Membuat file `nomor2.sh` pada UML SURABAYA yang berisikan script sebagai berikut: 
```
iptables -A FORWARD -p tcp --dport 22 -d 10.151.73.176/29 -i eth0 -j DROP
```
10.151.73.176 merupakan IP DMZ <br>
<img width="364" alt="no2" src="https://user-images.githubusercontent.com/26424136/103148189-bfe0ac80-478f-11eb-921f-55a4c5a04e03.PNG">
##### Testing
Hasil blok suatu kiriman text melalui port 22 (SSH) dari luar (putty) ke dalam topologi (MALANG dan MOJOKERTO) menggunakan netcat.
<img width="960" alt="test2" src="https://user-images.githubusercontent.com/26424136/103148200-c8d17e00-478f-11eb-9f2f-79dd1c1de807.png">

#### NOMOR 3
Membatasi DHCP dan DNS server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan yang berasal dari mana saja. <br>
Membuat file `nomor3.sh` dan berikan script berikut:
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```
- UML Mojokerto <br>
<img width="370" alt="no3" src="https://user-images.githubusercontent.com/26424136/103148190-c0794300-478f-11eb-987f-9c19051346a3.PNG"> <br>
- UML Malang <br>
<img width="370" alt="no3MLG" src="https://user-images.githubusercontent.com/26424136/103148191-c0794300-478f-11eb-9212-ab2028771ad6.PNG"> <br>
##### Testing
- Ping ke IP MOJOKERTO 10.151.73.179 <br>
Karena dibatasi hanya boleh menerima maksimal 3 koneksi, maka dapat dilihat bahwa yang <b>berhasil</b> melakukan ping yaitu <i>sidoarjo, gresik, kediri</i> dan yang <b>tidak berhasil</b> yaitu <i>probolinggo</i>.
<img width="960" alt="test3mojo" src="https://user-images.githubusercontent.com/26424136/103148202-cd963200-478f-11eb-9c04-8591c472aff1.png"> <br>
- Ping ke IP MALANG 10.151.73.178 <br>
Dapat dilihat bahwa yang <b>berhasil</b> melakukan ping yaitu <i>sidoarjo, gresik, probolinggo</i> dan yang <b>tidak berhasil</b> yaitu <i>kediri</i>.
<img width="960" alt="test3mlg" src="https://user-images.githubusercontent.com/26424136/103148201-cb33d800-478f-11eb-9bc0-578233a1a747.png"> <br>

#### NOMOR 4
Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin sampai Jumat, Selain itu di <b>reject</b>. <br>
Membuat file `nomor4.sh` yang berisikan script sebagai berikut: 
```
iptables -A INPUT -s 192.168.2.0/24 -m time --timestart 00:00 --timestop 06:59 --weekdays Mon,Tue,Wed,Thu,Fri -j REJECT
iptables -A INPUT -s 192.168.2.0/24 -m time --timestart 17:01 --timestop 23:59 --weekdays Mon,Tue,Wed,Thu,Fri -j REJECT
iptables -A INPUT -s 192.168.2.0/24 -m time --weekdays Sat,Sun -j REJECT
```
<img width="371" alt="no4" src="https://user-images.githubusercontent.com/26424136/103217880-4641ed80-494c-11eb-8f85-7ea0ffbde7aa.PNG"> <br>
##### Testing
- Melakukan test dengan netcat dari SIDOARJO ke MALANG pada waktu yang tidak diperbolehkan dan hasilnya gagal. <br>
<img width="729" alt="test4 (2)" src="https://user-images.githubusercontent.com/26424136/103148203-cf5ff580-478f-11eb-8087-3392ba0835c0.png"> <br>
- Melakukan ping dari SIDOARJO ke MALANG pada waktu yang tidak diperbolehkan dan hasilnya gagal. <br>
<img width="730" alt="test4 (3)" src="https://user-images.githubusercontent.com/26424136/103218179-f44d9780-494c-11eb-97cf-dd3da211885e.png">
<b>NB</b>: Gunakan perintah `date -s "TahunBulanTanggal Jam:Menit"` untuk mengganti tanggal pada UML.

#### NOMOR 5
Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap harinya, Selain itu di <b>reject</b>.<br>
Membuat file `nomor5.sh` yang berisikan script sebagai berikut: 
```
iptables -A INPUT -s 192.168.0.0/24 -m time --timestart 07:01 --timestop 16:59 -j REJECT
```
<img width="412" alt="no5" src="https://user-images.githubusercontent.com/26424136/103148193-c1aa7000-478f-11eb-8af6-3635e2022db4.PNG"> <br>
##### Testing
Melakukan test dengan netcat dari GRESIK ke MALANG pada waktu yang tidak diperbolehkan dan hasilnya gagal.
<img width="728" alt="test5 (2)" src="https://user-images.githubusercontent.com/26424136/103148205-d424a980-478f-11eb-8f4e-981d25c89f69.png">

#### NOMOR 6
Membuat file `nomor6.sh` pada UML SURABAYA yang berisikan script sebagai berikut: 
```
iptables -A PREROUTING -t nat -p tcp -d 10.151.73.178 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.168.1.2:80
iptables -A PREROUTING -t nat -p tcp -d 10.151.73.178 -j DNAT --to-destination 192.168.1.3:80
```
<img width="370" alt="no6" src="https://user-images.githubusercontent.com/26424136/103148194-c2430680-478f-11eb-8225-244475250142.PNG"> <br>
##### Testing
Mengirim suatu text melalui port 80 dari luar (putty) ke dalam topologi (Malang), dimana saat mengakses DNS Server akan didistribusikan secara bergantian ke PROBOLINGGO dan MADIUN.
![test6](https://user-images.githubusercontent.com/26424136/103219878-7049de80-4951-11eb-9937-dbfc24c3ea10.png)

#### NOMOR 7
Membuat file `nomor7.sh` pada UML SURABAYA yang berisikan script LOGGING sebagai berikut:
```
iptables -N LOGGING
iptables -A FORWARD -d 10.151.73.176/29 -i eth0 -p tcp -m tcp --dport 22 -j LOGGING
iptables -A LOGGING -m limit --limit 2/min -j LOG --log-prefix "IPTables-Dropped: " --log-level 4
iptables -A LOGGING -j DROP
```
<img width="367" alt="no7(1)" src="https://user-images.githubusercontent.com/26424136/103218919-e9940200-494e-11eb-8a85-b96ec04e4fd6.PNG"> <br>
Membuat file `nomor7.sh` pada UML MALANG dan MOJOKERTO yang berisikan script LOGGING sebagai berikut:
```
iptables -N LOGGING
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j LOGGING
iptables -A LOGGING -m limit --limit 2/min -j LOG --log-prefix "IPTables-Dropped: " --log-level 4
iptables -A LOGGING -j DROP
```
<img width="731" alt="no7(2)" src="https://user-images.githubusercontent.com/26424136/103218925-ebf65c00-494e-11eb-9d55-e5fd73846cd1.PNG"> <br>
##### Testing
- Hasil log saat menjalankan perintah nomor 3 pada UML MALANG
![test7mlg](https://user-images.githubusercontent.com/26424136/103219885-72ac3880-4951-11eb-9ed4-1c0296e6468d.png)
- Hasil log saat menjalankan perintah nomor 3 pada UML MOJOKERTO
![test7mojo](https://user-images.githubusercontent.com/26424136/103219889-7475fc00-4951-11eb-945f-b0a353b58f21.png)
- Hasil log saat menjalankan perintah nomor 2
![test7sby](https://user-images.githubusercontent.com/26424136/103219890-763fbf80-4951-11eb-9bdc-55249a1eab59.png)
