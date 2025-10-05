# 8.1 FTP Server

## Tutorial Konfigurasi FTP & Telnet Server pada Router Huawei

### Bagian 1: Konfigurasi dan Pengujian FTP Server

#### Pengenalan FTP Server
FTP (File Transfer Protocol) adalah server yang menyediakan layanan transfer file dalam jaringan, baik LAN maupun Internet.  
FTP bekerja dengan arsitektur client-server — ada perangkat yang bertindak sebagai **server (penyedia layanan)** dan **client (peminta layanan)**.

Bayangkan FTP Server seperti **perpustakaan digital**, di mana pengguna dapat **mengambil (download)** atau **menitipkan (upload)** file menggunakan **username dan password**.

#### Skenario Laboratorium
- **R2** sebagai FTP Server  
- **R1** sebagai FTP Client  
- Tujuan: R1 dapat mengakses direktori `flash:` pada R2 dan mengunduh file konfigurasi.

🔗 [Topologi Awal](https://drive.google.com/open?id=19mWH3orA_2m6AYOuC8SwafpISS8i0U_u&usp=drive_fs)

---

### Langkah-Langkah Konfigurasi

#### 1. Konfigurasi Dasar Alamat IP

**Pada R1 (Client):**
```bash
<Huawei> system-view
[Huawei] interface gigabitethernet 0/0/0
[Huawei-GigabitEthernet0/0/0] ip address 192.168.1.1 24
```

**Pada R2 (Server):**
```bash
<Huawei> system-view
[Huawei] interface gigabitethernet 0/0/0
[Huawei-GigabitEthernet0/0/0] ip address 192.168.1.2 24
```

---

#### 2. Konfigurasi FTP Server (R2)
Aktifkan dan konfigurasi layanan FTP pada R2.

```bash
[Huawei] ftp server enable
[Huawei] set default ftp-directory flash:
[Huawei] aaa
[Huawei-aaa] local-user aidil password cipher aidil
[Huawei-aaa] local-user aidil service-type ftp
[Huawei-aaa] local-user aidil privilege level 15
[Huawei-aaa] local-user aidil idle-timeout 0 0
[Huawei-aaa] quit
```

---

#### 3. Verifikasi dan Persiapan File di R2
```bash
<Huawei> save
<Huawei> dir
<Huawei> rename vrpcfg.zip aidil.zip
```

🔗 [Hasil Direktori R2](https://drive.google.com/open?id=19mWH3orA_2m6AYOuC8SwafpISS8i0U_u&usp=drive_fs)

---

#### 4. Pengujian FTP dari R1
```bash
<R1> ftp 192.168.1.2
Username: aidil
Password: aidil
[ftp] get aidil.zip
[ftp] bye
<R1> dir
```

🔗 [Hasil Transfer File di R1](https://drive.google.com/open?id=1ygHpHWsmLn-xh7bTkB82dy84HkIo_Fq2&usp=drive_fs)

---

## Bagian 2: Menambahkan Akses Remote via Telnet

Setelah konfigurasi FTP, kita menambahkan kemampuan agar R2 bisa diakses dari **router lain** dan **komputer Host (PC)** menggunakan **Telnet**.

🔗 [Topologi Baru](https://drive.google.com/open?id=1yohjZMX75xPMoJO5BkSSfVklzRXc_ayL&usp=drive_fs)

---

### Langkah-Langkah Konfigurasi Telnet

#### 1. Uji Konektivitas Host ke Router
Pastikan Host dapat terhubung ke R2 dengan `ping` ke `192.168.1.2`.

🔗 [Hasil Ping](https://drive.google.com/open?id=1kEM6zUyAX2pvaD6Yl0GyJUenjAF6Uj3j&usp=drive_fs)

---

#### 2. Konfigurasi Telnet di R2
```bash
[Huawei] system-view
[Huawei] user-interface vty 0 4
[Huawei-ui-vty0-4] authentication-mode aaa
[Huawei-ui-vty0-4] quit
[Huawei] aaa
[Huawei-aaa] local-user aidil service-type telnet ftp
[Huawei-aaa] display this
```

Contoh hasil konfigurasi AAA:
```bash
[V200R003C00]
#
aaa
 authentication-scheme default
 authorization-scheme default
 accounting-scheme default
 domain default
 domain default_admin
 local-user admin password cipher %$%$K8m.Nt84DZ}e#<0`8bmE3Uw}%$%$
 local-user admin service-type http
 local-user aidil password cipher %$%$)XhHM6B[kPN4n$~+[Es0V8=>%$%$
 local-user aidil idle-timeout 0 0
 local-user aidil privilege level 15
 local-user aidil service-type telnet ftp
#
return
```

🔗 [Konfigurasi AAA Screenshot 1](https://drive.google.com/open?id=1ZDnXBJSfs5maF3SLWIvN0fRtQW5WKenq&usp=drive_fs)

---

#### 3. Pengujian Telnet dari Host PC
Gunakan aplikasi **PuTTY** atau terminal lain dari PC Host:
1. Masukkan IP: `192.168.1.2`
2. Pilih koneksi: **Telnet**
3. Login dengan:  
   - Username: `aidil`  
   - Password: `aidil`

🔗 [Hasil Login Telnet](https://drive.google.com/open?id=1jDmTcX_sPkvPzPykAMcsPdFxDnLZYn3O&usp=drive_fs)

---

## Ringkasan FTP
Dalam praktikum ini:
1. R2 dikonfigurasi sebagai **FTP Server**, mengaktifkan layanan FTP, menentukan direktori `flash:` dan membuat user **aidil** melalui AAA.  
2. R1 sebagai **FTP Client** berhasil mengunduh file dari R2.  
3. Ditambahkan juga **Telnet access** agar router dapat dikelola secara remote melalui Host PC.  
4. AAA digunakan sebagai **pusat otentikasi dan kontrol akses** untuk FTP dan Telnet.

---

### Daftar Isi
- Bagian 1: Konfigurasi dan Pengujian FTP Server  
  - Pengenalan FTP Server  
  - Skenario Laboratorium  
  - Langkah-Langkah Konfigurasi  
- Bagian 2: Menambahkan Akses Remote via Telnet  
  - Uji Konektivitas Host ke Router  
  - Konfigurasi Telnet di R2  
  - Pengujian Telnet dari Host PC  
- Ringkasan FTP  
