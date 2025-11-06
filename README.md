# Tugas Akhir 2 - Praktikum Jaringan Komputer
Repositori ini berisi file tugas akhir kedua untuk mata kuliah **Praktikum Jaringan Komputer**.  
File utama berisi konfigurasi jaringan menggunakan **Cisco Packet Tracer**, serta hasil pengujian konektivitas melalui perintah `ping`.

## File
- **Puan Akeyla Maharani M_2315061070_TA2_JKD.pkt** — File utama berisi konfigurasi jaringan menggunakan Cisco Packet Tracer.  
- **pingBelumBerhasil.png** — Hasil tangkapan layar ketika koneksi jaringan belum berhasil.  
- **pingSudahBerhasil.png** — Hasil tangkapan layar setelah koneksi berhasil dilakukan.  

## Tujuan
- Menghubungkan dua subnet menggunakan router.
- Mengonfigurasi switch dan router agar dapat saling berkomunikasi.
- Menguji konektivitas antar perangkat (PC-A ↔ PC-B).
- Menampilkan informasi jaringan dengan perintah `show`.

## Deskripsi 
Proyek ini membangun topologi jaringan dasar menggunakan:
- 1 Router **Cisco 4221**
- 1 Switch **Cisco 2960**
- 2 PC (PC-A & PC-B)

Konfigurasi meliputi IP address, pengamanan login, aktivasi interface, dan pengujian konektivitas IPv4 & IPv6.

## Peralatan
- Router Cisco 4321  
- Switch Cisco 2960  
- 2 PC  
- Kabel
- Cisco Packet Tracer

## Tabel Alamat

| Device | Interface | IPv4 Address | Subnet Mask | IPv6 Address | Default Gateway |
|--------|------------|--------------|--------------|---------------|-----------------|
| R1 | G0/0/0 | 192.168.0.1 | /24 | 2001:db8:acad::1/64 | — |
| R1 | G0/0/1 | 192.168.1.1 | /24 | 2001:db8:acad:1::1/64 | — |
| S1 | VLAN 1 | 192.168.1.2 | /24 | — | 192.168.1.1 |
| PC-A | NIC | 192.168.1.3 | /24 | 2001:db8:acad:1::3/64 | 192.168.1.1 |
| PC-B | NIC | 192.168.0.3 | /24 | 2001:db8:acad::3/64 | 192.168.0.1 |


##  Langkah-Langkah Konfigurasi

###  1. Inisialisasi & Koneksi
1. Hubungkan perangkat sesuai topologi:
   - PC-A → Switch → Router (G0/0/1)
   - PC-B → Router (G0/0/0)
2. Nyalakan semua perangkat.
3. Hapus konfigurasi lama:
   ```bash
   erase startup-config
   reload

###  2. Konfigurasi Router
Router> enable

Router# config terminal

Router(config)# hostname R1

R1(config)# no ip domain-lookup

R1(config)# enable secret class

R1(config)# service password-encryption

R1(config)# banner motd $ Authorized Users Only! $

! Console dan Telnet login

R1(config)# line console 0

R1(config-line)# password cisco

R1(config-line)# login

R1(config-line)# exit

R1(config)# line vty 0 4

R1(config-line)# password cisco

R1(config-line)# login

R1(config-line)# exit

! Interface G0/0/0 (ke PC-B)

R1(config)# interface g0/0/0

R1(config-if)# ip address 192.168.0.1 255.255.255.0

R1(config-if)# ipv6 address 2001:db8:acad::1/64

R1(config-if)# ipv6 address fe80::1 link-local

R1(config-if)# description Connected to PC-B

R1(config-if)# no shutdown

R1(config-if)# exit

! Interface G0/0/1 (ke Switch)

R1(config)# interface g0/0/1

R1(config-if)# ip address 192.168.1.1 255.255.255.0

R1(config-if)# ipv6 address 2001:db8:acad:1::1/64

R1(config-if)# ipv6 address fe80::1 link-local

R1(config-if)# description Connected to Switch S1

R1(config-if)# no shutdown

R1(config-if)# exit

! Aktifkan routing IPv6

R1(config)# ipv6 unicast-routing

R1(config)# exit

! Simpan konfigurasi & atur jam

R1# copy running-config startup-config

R1# clock set 15:30:00 27 Aug 2019

### Deskripsi Singkat Konfigurasi Router
Konfigurasi ini digunakan untuk menyiapkan router R1 agar dapat bekerja sebagai penghubung jaringan antara dua subnet, sekaligus mengamankan akses administrasi. Pertama, nama router diatur menjadi R1, kemudian fitur pencarian domain dinonaktifkan untuk mencegah kesalahan ketik menjadi proses DNS. Password enable dan password pada console serta VTY (Telnet) dikonfigurasi untuk memberikan keamanan saat administrator mengakses router, dan service password-encryption digunakan untuk mengenkripsi semua password.

Sebuah banner MOTD ditambahkan untuk menampilkan peringatan saat ada yang mengakses perangkat.
Selanjutnya, dua interface utama dikonfigurasi:
G0/0/0 terhubung ke PC-B dengan alamat IPv4 192.168.0.1/24 dan IPv6 2001:db8:acad::1/64.
G0/0/1 terhubung ke switch S1 dengan IPv4 192.168.1.1/24 dan IPv6 2001:db8:acad:1::1/64.

Masing-masing interface juga diberi alamat link-local IPv6 dan diaktifkan menggunakan perintah no shutdown. Agar IPv6 dapat dirutekan antar jaringan, fitur ipv6 unicast-routing diaktifkan. Terakhir, konfigurasi disimpan ke memori startup menggunakan copy running-config startup-config dan waktu sistem diatur menggunakan perintah clock set.

###  3. Konfigurasi Switch
Switch> enable

Switch# config terminal

Switch(config)# hostname S1

S1(config)# no ip domain-lookup

! IP VLAN untuk manajemen

S1(config)# interface vlan 1

S1(config-if)# ip address 192.168.1.2 255.255.255.0

S1(config-if)# no shutdown

S1(config-if)# exit

! Default gateway

S1(config)# ip default-gateway 192.168.1.1

S1(config)# exit

! Simpan konfigurasi

S1# copy running-config startup-config

### Deskripsi Singkat Konfigurasi Switch
Konfigurasi ini dilakukan untuk menyiapkan switch S1 agar dapat digunakan sebagai perangkat penghubung jaringan dan dapat diakses melalui jaringan menggunakan IP manajemen. Pertama, nama switch diubah menjadi S1, dan fitur ip domain-lookup dinonaktifkan untuk mencegah proses pencarian DNS yang tidak diperlukan saat terjadi salah ketik perintah. Untuk pengelolaan switch melalui jaringan, VLAN 1 dikonfigurasi dengan alamat IP 192.168.1.2/24, kemudian interface VLAN tersebut diaktifkan menggunakan no shutdown. Agar switch dapat berkomunikasi dengan perangkat di luar subnet-nya, gateway default diatur ke alamat router R1 yaitu 192.168.1.1. 

Terakhir, konfigurasi disimpan ke memori startup menggunakan perintah copy running-config startup-config agar tetap tersimpan setelah perangkat restart.

###  4. Konfigurasi PC
PC-A: 

IPv4: 192.168.1.3

Subnet Mask: 255.255.255.0

Gateway: 192.168.1.1

PC-B:

IPv4: 192.168.0.3

Subnet Mask: 255.255.255.0

Gateway: 192.168.0.1

### Deskripsi Singkat Konfigurasi PC
Pada PC-A dan PC-B dilakukan konfigurasi alamat IP agar kedua komputer dapat terhubung dalam jaringan melalui router.
PC-A dikonfigurasi berada pada jaringan 192.168.1.0/24 dengan alamat 192.168.1.3, subnet mask 255.255.255.0, dan gateway 192.168.1.1 (alamat interface router R1 pada jaringan tersebut). Konfigurasi ini memungkinkan PC-A mengirim data ke perangkat lain di dalam jaringan maupun keluar subnet melalui router.
PC-B berada pada jaringan berbeda, yaitu 192.168.0.0/24, menggunakan alamat 192.168.0.3, subnet mask 255.255.255.0, dan gateway 192.168.0.1 yang merupakan interface router R1 pada jaringan PC-B. Dengan pengaturan ini, PC-B dapat terhubung ke router dan berkomunikasi dengan jaringan lain melalui routing.

###  5. Pengujian Konektivitas
Dari Pc-A: ping 192.168.0.3

Dari Switch : ping 192.168.0.3

###  6. Perintah Verifikasi Penting
R1# show ip route

R1# show ipv6 route

R1# show ip interface brief

R1# show ipv6 interface brief

S1# show ip interface brief

## Hasil Pengujian
Sebelum koneksi berhasil:
![Ping Belum Berhasil](pingBelumBerhasil.png)

Mengapa ping dari PC-A ke PC-B tidak berhasil?

Ping tidak berhasil karena interface router (default gateway) pada setiap jaringan belum dikonfigurasi. Akibatnya, traffic Layer 3 (IP) tidak bisa dirutekan dari satu subnet ke subnet lainnya. Setiap PC berada di jaringan/subnet yang berbeda, sehingga untuk bisa saling berkomunikasi, mereka membutuhkan router yang sudah dikonfigurasi IP address pada masing-masing interface yang terhubung ke subnet tersebut.

Setelah koneksi berhasil:
![Ping Sudah Berhasil](pingSudahBerhasil.png)

Ping sudah berhasil!

Ping berhasil karena router sudah berfungsi dengan baik dalam meneruskan data antar dua subnet. Selain itu, switch 2960 secara otomatis mengaktifkan port yang terhubung ke perangkat, jadi koneksi antar perangkat bisa langsung berjalan tanpa perlu konfigurasi tambahan, ping pertama setelah router tersambung awalnya request timed out yang dilanjutkan dengan reply ini disebabkan oleh waktu milidetik yang dibutuhkan untuk tersambung.

## Penjelasan Lebih Lanjut
Penjelasan lebih lengkap mengenai isi dan konfigurasi jaringan dapat dilihat pada video berikut:  
🎥 [Klik di sini untuk menonton di YouTube](https://youtu.be/EF5bNltfH4g)
