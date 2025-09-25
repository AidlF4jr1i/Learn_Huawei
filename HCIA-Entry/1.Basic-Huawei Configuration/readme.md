# Basic Connection in Huawei (ENSP)

Sebelum masuk ke konfigurasi perangkat Huawei, kita akan terlebih dahulu membuat **Lab sederhana** menggunakan **eNSP**. Pada lab ini kita akan mencoba melakukan koneksi antar perangkat **PC End Device** yang tersedia di eNSP.  

Diagram lab dapat dilihat pada gambar berikut:  
👉 [Topologi Lab](https://drive.google.com/file/d/1xarkYhoZZwcEm-KtvwOFDjQ2CVgELRaV/view?usp=drive_link)

## Langkah-langkah:
1. Buka aplikasi **eNSP**.  
2. **Drag and drop** 2 perangkat **PC (End Device)** ke dalam workspace.  
3. Sambungkan kedua PC dengan **cable copper** (straight-through).  
4. Atur **IP Address** masing-masing PC:  
   - **PC1:** `192.168.1.2/24`  
   - **PC2:** `192.168.1.3/24`  
   Untuk melakukan konfigurasi IP, klik PC → pilih **Settings**.  
5. Klik kanan pada kabel → pilih **Capture** → maka akan otomatis membuka **Wireshark**.  
6. Dari **PC2**, lakukan ping ke **PC1**:  
   - Buka PC2 → **Settings** → pilih **Command**.  
   - Jalankan perintah:  
     ```
     ping 192.168.1.2
     ```
7. Amati pada Wireshark apakah lalu lintas **ICMP (ping)** dapat ditangkap.

## Dokumentasi:
- [Setting PC1](https://drive.google.com/file/d/1ouw8jEfk9mzP0oahKIUy4r4RbG-cQL_K/view?usp=drive_link)  
- [Setting PC2](https://drive.google.com/file/d/1SDFw8HCxNlVMVyW0S8JM286iJF7OzauA/view?usp=drive_link)  
- [Output Wireshark](https://drive.google.com/file/d/1rF7S1qj8bD5ZF_r7QG7MdtPXJsYayACF/view?usp=drive_link)  

## Kesimpulan:
Pada lab ini kita mempelajari bagaimana cara melakukan **manual IP setting pada end device** di Huawei eNSP. Interface dan prosesnya sangat mirip dengan yang ada di **Cisco Packet Tracer**. Bedanya, pada eNSP kita juga dapat langsung melakukan analisis lalu lintas data menggunakan **Wireshark** karena sudah kompatibel.  

---

# LAB 2 – Basic Configuration on Huawei Router

Pada lab ini kita akan mencoba beberapa konfigurasi dasar di **router Huawei**. Saya menggunakan 2 router tipe **AR120**, namun Anda bisa menggunakan tipe lain. Router akan dihubungkan, kemudian kita jalankan beberapa perintah konfigurasi umum.

---

## 1. Perintah **display**
Perintah ini mirip dengan `show` di Cisco, digunakan untuk menampilkan informasi konfigurasi atau status perangkat.  

Contoh:  
- `display version` → menampilkan versi OS yang digunakan.  
- `display ip interface brief` → menampilkan status IP pada interface.  
- `display current-configuration` → melihat konfigurasi yang sedang aktif.  
- `display saved-configuration` → melihat konfigurasi yang sudah tersimpan.  
- `display startup` → melihat file konfigurasi yang dijalankan saat booting.

---

## 2. Perintah **dir**
Digunakan untuk melihat lokasi penyimpanan data di router Huawei. Biasanya data tersimpan di **flash**, berisi file konfigurasi awal, file sistem, dll.  

Contoh:  
````

<Huawei> dir

```

---

## 3. Perintah **system-view**
Mirip dengan `conf t` di Cisco. Perintah ini digunakan untuk masuk ke **mode konfigurasi**.  

Setelah masuk system-view, prompt router akan berubah dari:  
```

<Huawei>
```
menjadi:  
```
[Huawei]
```

### Contoh command di system-view:

* Masuk ke interface dan set IP address:

  ```
  [Huawei] interface GigabitEthernet0/0/0
  [Huawei-GigabitEthernet0/0/0] ip address 192.168.1.1 24
  ```

  > Catatan: Pada Huawei kita bisa menggunakan **prefix length (/24)** atau **subnet mask lengkap (255.255.255.0)**.

* Menambahkan deskripsi pada interface:

  ```
  [Huawei-GigabitEthernet0/0/0] description Link to R2 - LAN Connection

  ```

* Mengubah nama host:

  ```
  [Huawei] sysname R1
  ```

* Membuat banner (header):

  ```
  [Huawei] header login information "Authorized Users Only!"
  ```

* Konfigurasi akses console dengan password:

  ```
  [Huawei] user-interface console 0
  [Huawei-ui-console0] authentication-mode password
  [Huawei-ui-console0] set authentication password cipher Huawei123
  [Huawei-ui-console0] idle-timeout 5 0
  ```

  > `idle-timeout` digunakan agar session otomatis logout setelah 5 menit idle.

---

## 4. Perintah tambahan

* **Mengatur jam & tanggal (clock):**

  ```
  <Huawei> clock datetime 15:30:00 2025-09-24
  ```

  > Command ini dijalankan di mode user (`<Huawei>`), bukan di system-view. Berguna agar log sesuai zona waktu.

* **Keluar dari mode konfigurasi:**

  ```
  [Huawei] quit
  ```

  > Untuk kembali ke mode sebelumnya. Jika ingin langsung kembali ke mode user, gunakan:

  ```
  [Huawei] return
  ```

  atau cukup `Ctrl+Z`.

---

# LAB 3 – Using Telnet to remote Huawei Router using HostOS

padalab kali ini kita akan mencoba melakukan remote pada router yang ada di ensp agar bisa dikonfigurasikan dari laptop kita, hal-hal yang perlu disiapkan:

1. setting loopback adapter di HostOS, disini karena kita menggunakan windows caranya cukup simpel:
- pada menu search windows tulis 'hdwwiz' lalu enter
- setelah masuk, pilih next lalu 'istall hardware manually'
- scroll lalu pilih bagian 'Network Adapter' lalu klik enter
- scroll lagi lalu pilih 'Microsofot', nah di menu sebelah kanan cari 'Loopback adapater atau kalau di laptop namanya'micorosoft KM-TEST loopback adapater'',klik next dua kali lalu oke, setelah itu lakukan restart pada komputer dan lihat apakah adapater sudah terpasang dengan menalankan 'Win + r' lalu 'ncpa.cpl', disana harusnya akan ada aadapter baru dengan nama Micorosoft Km-TEST Loopback, ubah namanya sesuai preferensi kamu, disinisaya akan menggunakan namaa'ENSP'

- setelah itu klik properties pada loopback adapter dan setting IP nya menjadi '192.168.2.254/24' laluu klik oke

2. okeh sekarag  kembali ke ENSp disini topologinya akan seperti pada gambar berikut: 'https://drive.google.com/file/d/15vaVup7X29Cu5nSvB2UHmKef4aZuJdlS/view?usp=drive_link'. disini kita akan menggunakan node 'cloud' dan 1 router AR1220 yang aan kita coba remote, sebelum itu kita lakukan settingterlebiih dahulu pada cloud dengan klik kanan lalu 'settings', nah di menu yang tampil lakukan setting seperti pada gambar berikut :

-gambar 1: https://drive.google.com/file/d/1ib7YetBnPaxxRz_iKs5XRRJ8OOgHHIo9/view?usp=drive_link
-gambar2: https://drive.google.com/file/d/18RXsPfTwV9lkr3R5GjMcVcJuaUccPU5A/view?usp=drive_link
-gambar3: https://drive.google.com/file/d/1uj4W9JDeoJbombzDEW1-7gGxW_UdXOlQ/view?usp=drive_link
, next setelah selesai padanode cloud pada ROuter masuk ke manu CLI dan mode konfig dengan menjalanakn:
- system-view, lalu masukkan IP address dengan subnet yang sama seperti pada komputer host
- interface Gigabitethernet 0/0/0
- ip add 192.168.2.1 24
- ping 192.168.2.254, kalau ping nya jalan artinya antar router dan PC sudah sa;ing terhubung(gambar:'https://drive.google.com/file/d/1Z62ljt7dfhq6gG5tqaJAlrwWeeJr1FIT/view?usp=drive_link'), next kita akan konfigurasikan telnet agar router huawei dapat kita remote dari komputer host kita, disini di mmode system-view kita akan jalankan :

- quit, keluar dari interface 
- user-interface vty 0 4, command vty ini berarti kita akan membuat sebuah room dimana orang-orang yang terhubung ke jaringan router dapat meremote router dengan maximal '0-4' atau kalau dijumlahkan sebanyak 5 user yang bisa berada pada sesi yang sama, kalau ada user ke-6 yang mecncoba remote maka aksesnya akan ditolak
- authentication-mode password , mesetting password untuk user yg akan mremote
- masukkan password, bebas mau masukkan apa saja, klik enter lalu selesai

3. selanutnya, pastika aps 'Putty' sudah terinstall di laptop kalian,KALAU BELUM SILAHKAN DWPWNLOAD DAN INSTALL FILE NYA DI SINI :'https://drive.google.com/file/d/11H4X2Xjvrn9Zc7bUmD35fj-bEe9Zc6tF/view?usp=drive_link', diisni kita akan meremote router ,enggunakan putty:
- masuk ke putty lalu masukka '192.168.2.1'(ip router) lalu centang menu 'telnet',gambar:'https://drive.google.com/file/d/17tqPixNrq1NxfdmCEJvtEExzvT9ECce7/view?usp=drive_link'
- klik enter dan masukkan passwrod yang sudah kita setting sebelumnya dan boom kita berhasil melakukan remote!, untuk memastikan coba jalankan command 'disp ip int br' dan lihat apakah kita bisa melihat konfigirasi yang ada di router, gambar: 'https://drive.google.com/file/d/1gssNewk2IHQYirb9YjLrriXX8RoRYU0Z/view?usp=drive_link'
- disini kita mendapat error unrecognized erro, mengapa? hal ini dikarenakan kita lupa melakukan setting hak akses apada user telent sehingga hak yang diapat sekarang hanya pada level 'visit' disini kita bbahakan tdak bisa menjalankan command 'display', solusi untuk amslaah ini, kita masuk ke interface vty :
- system-view
- user-interface vty 0 4
- user privilege level 15, dimana level 15 adalah level akses tertinggi yakni kita bisa mengkonfig router dengan hak akses admin!
- next coba remote ulang dan jalankan perintah sebelumnya
- tampilan akhirnya akan seperti gambar: 'https://drive.google.com/file/d/1Jc0Pp02zi8RQte767RwsuDey3FHrYRpk/view?usp=drive_link'


okeh sampai section ini kita sudah banyak melakukan beberapa hal mulai dari pengenalan jaringankomptuter di ENSp command-command basic pada HuaweiRouter, sampai acara meremote router Huawei ke komputer OS, jaga semangatmu karena kita akan amskuk kemateri yang lebih berat!!


# Ringkasan Materi