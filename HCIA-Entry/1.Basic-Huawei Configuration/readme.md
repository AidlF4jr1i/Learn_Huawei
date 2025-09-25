# Huawei Networking Lab with eNSP  

Dokumentasi ini berisi langkah-langkah dasar dalam menggunakan **Huawei eNSP (Enterprise Network Simulation Platform)** untuk mempelajari konfigurasi perangkat jaringan Huawei.  
Kita akan memulai dari konfigurasi paling dasar (PC to PC connection), lalu ke konfigurasi dasar router, hingga melakukan remote menggunakan **Telnet** dari Host OS.  

---

# LAB 1 – Basic Connection in Huawei (eNSP)

Sebelum masuk ke konfigurasi perangkat **router Huawei**, kita akan terlebih dahulu membuat **Lab sederhana** menggunakan **eNSP**. Pada lab ini, kita akan mencoba melakukan koneksi antar perangkat **PC (End Device)** yang tersedia di eNSP.  

📌 Topologi Lab:  
👉 [Topologi Lab](https://drive.google.com/file/d/1xarkYhoZZwcEm-KtvwOFDjQ2CVgELRaV/view?usp=drive_link)

---

## Langkah-langkah:
1. Buka aplikasi **eNSP**.  
2. **Drag & Drop** 2 perangkat **PC (End Device)** ke dalam workspace.  
3. Sambungkan kedua PC dengan **cable copper straight-through**.  
4. Atur **IP Address** masing-masing PC:  
   - **PC1:** `192.168.1.2/24`  
   - **PC2:** `192.168.1.3/24`  
   📌 Konfigurasi IP dapat dilakukan dengan klik PC → pilih **Settings**.  
5. Klik kanan pada kabel → pilih **Capture** → maka otomatis membuka **Wireshark**.  
6. Dari **PC2**, lakukan ping ke **PC1**:  
   - Buka PC2 → **Settings** → pilih **Command**.  
   - Jalankan perintah:  
     ```
     ping 192.168.1.2
     ```
7. Amati pada Wireshark apakah lalu lintas **ICMP (ping)** berhasil ditangkap.

---

## Dokumentasi:
- [Setting PC1](https://drive.google.com/file/d/1ouw8jEfk9mzP0oahKIUy4r4RbG-cQL_K/view?usp=drive_link)  
- [Setting PC2](https://drive.google.com/file/d/1SDFw8HCxNlVMVyW0S8JM286iJF7OzauA/view?usp=drive_link)  
- [Output Wireshark](https://drive.google.com/file/d/1rF7S1qj8bD5ZF_r7QG7MdtPXJsYayACF/view?usp=drive_link)  

---

## Kesimpulan:
Pada lab ini kita belajar bagaimana melakukan **manual IP setting pada end device** di Huawei eNSP.  
Interface dan prosesnya sangat mirip dengan **Cisco Packet Tracer**, namun eNSP punya keunggulan karena dapat langsung melakukan analisis lalu lintas data menggunakan **Wireshark**.  

---

# LAB 2 – Basic Configuration on Huawei Router

Di lab ini kita akan mencoba beberapa konfigurasi dasar di **router Huawei**.  
Saya menggunakan 2 router tipe **AR120**, namun tipe lain juga bisa digunakan.  

📌 Router akan dihubungkan, lalu kita jalankan beberapa perintah konfigurasi umum.

---

## 1. Perintah **display**
Perintah ini mirip dengan `show` di Cisco. Digunakan untuk menampilkan informasi konfigurasi atau status perangkat.  

Contoh:
- `display version` → menampilkan versi OS yang digunakan.  
- `display ip interface brief` → menampilkan status IP pada interface.  
- `display current-configuration` → melihat konfigurasi aktif.  
- `display saved-configuration` → melihat konfigurasi yang sudah tersimpan.  
- `display startup` → melihat file konfigurasi yang dijalankan saat booting.  

---

## 2. Perintah **dir**
Digunakan untuk melihat lokasi penyimpanan data di router Huawei. Biasanya data tersimpan di **flash**, berisi file konfigurasi, file sistem, dll.  

Contoh:
```
<Huawei> dir
```

---

## 3. Perintah **system-view**
Mirip dengan `conf t` di Cisco. Perintah ini digunakan untuk masuk ke **mode konfigurasi**.  

Setelah masuk, prompt router berubah:  
```
<Huawei>
```
menjadi  
```
[Huawei]
```

---

### Contoh Command di system-view

* **Masuk ke interface dan set IP address:**
  ```
  [Huawei] interface GigabitEthernet0/0/0
  [Huawei-GigabitEthernet0/0/0] ip address 192.168.1.1 24
  ```
  > Catatan: Huawei bisa pakai **prefix length (/24)** atau **subnet mask (255.255.255.0)**.

* **Menambahkan deskripsi pada interface:**
  ```
  [Huawei-GigabitEthernet0/0/0] description Link to R2 - LAN Connection
  ```

* **Mengubah nama host:**
  ```
  [Huawei] sysname R1
  ```

* **Membuat banner (header):**
  ```
  [Huawei] header login information "Authorized Users Only!"
  ```

* **Konfigurasi akses console dengan password:**
  ```
  [Huawei] user-interface console 0
  [Huawei-ui-console0] authentication-mode password
  [Huawei-ui-console0] set authentication password cipher Huawei123
  [Huawei-ui-console0] idle-timeout 5 0
  ```
  > `idle-timeout` artinya session otomatis logout setelah 5 menit idle.  

---

## 4. Perintah tambahan

* **Mengatur jam & tanggal (clock):**
  ```
  <Huawei> clock datetime 15:30:00 2025-09-24
  ```

* **Keluar dari mode konfigurasi:**
  ```
  [Huawei] quit
  ```
  Untuk langsung kembali ke user mode:
  ```
  [Huawei] return
  ```
  atau cukup tekan `Ctrl+Z`.

---

# LAB 3 – Remote Huawei Router via Telnet from HostOS  

Di lab ini kita akan mencoba melakukan **remote ke router Huawei** dari laptop/PC menggunakan **Telnet**.  

---

## 1. Setting Loopback Adapter di Host OS (Windows)

1. Buka **Run** → ketik `hdwwiz`.  
2. Pilih **Next** → pilih **Install hardware manually**.  
3. Pilih **Network Adapter**.  
4. Pilih **Microsoft** → lalu pilih **Microsoft KM-TEST Loopback Adapter**.  
5. Klik **Next** hingga selesai → restart komputer.  
6. Cek adapter dengan **Win+R → ncpa.cpl**.  
   - Akan muncul adapter baru dengan nama **Microsoft KM-TEST Loopback Adapter**.  
   - Rename menjadi **ENSP**.  
7. Set IP Adapter menjadi:  
   ```
   192.168.2.254/24
   ```

---

## 2. Konfigurasi di eNSP  

📌 Topologi: [Lab Topologi](https://drive.google.com/file/d/15vaVup7X29Cu5nSvB2UHmKef4aZuJdlS/view?usp=drive_link)  

1. Tambahkan **Cloud Node** dan hubungkan dengan **Router AR1220**.  
2. Klik kanan **Cloud** → pilih **Settings**.  
   - Ikuti konfigurasi seperti pada gambar:  
     - [Gambar 1](https://drive.google.com/file/d/1ib7YetBnPaxxRz_iKs5XRRJ8OOgHHIo9/view?usp=drive_link)  
     - [Gambar 2](https://drive.google.com/file/d/18RXsPfTwV9lkr3R5GjMcVcJuaUccPU5A/view?usp=drive_link)  
     - [Gambar 3](https://drive.google.com/file/d/1uj4W9JDeoJbombzDEW1-7gGxW_UdXOlQ/view?usp=drive_link)  

3. Masuk ke CLI Router → konfigurasi IP di interface:  
   ```
   <Huawei> system-view
   [Huawei] interface GigabitEthernet0/0/0
   [Huawei-GigabitEthernet0/0/0] ip address 192.168.2.1 24
   ```
4. Uji konektivitas dengan ping:  
   ```
   <Huawei> ping 192.168.2.254
   ```
   Jika sukses → berarti Host OS dan Router sudah terhubung.  
   📷 [Contoh Hasil Ping](https://drive.google.com/file/d/1Z62ljt7dfhq6gG5tqaJAlrwWeeJr1FIT/view?usp=drive_link)  

---

## 3. Konfigurasi Telnet di Router

Masuk ke system-view:  
```
[Huawei] user-interface vty 0 4
[Huawei-ui-vty0-4] authentication-mode password
[Huawei-ui-vty0-4] set authentication password cipher Huawei123
```
📌 Artinya: maksimal 5 user (`0–4`) bisa Telnet ke router pada waktu yang sama.  

---

## 4. Remote Router via PuTTY

1. Install **PuTTY** → [Download PuTTY](https://drive.google.com/file/d/11H4X2Xjvrn9Zc7bUmD35fj-bEe9Zc6tF/view?usp=drive_link)  
2. Buka PuTTY → masukkan IP **192.168.2.1** → pilih **Telnet**.  
   📷 [Contoh PuTTY](https://drive.google.com/file/d/17tqPixNrq1NxfdmCEJvtEExzvT9ECce7/view?usp=drive_link)  
3. Login dengan password Telnet yang sudah dibuat.  
4. Jalankan perintah:  
   ```
   display ip interface brief
   ```
   📷 [Output Display](https://drive.google.com/file/d/1gssNewk2IHQYirb9YjLrriXX8RoRYU0Z/view?usp=drive_link)  

---

## ⚠️ Catatan: Masalah Privilege

Kadang muncul error *“unrecognized command”*.  
Hal ini karena level user default Telnet masih **Visit (Level 0)** → tidak bisa jalankan command `display`.  

### Solusi:
Masuk kembali ke konfigurasi VTY:  
```
[Huawei] user-interface vty 0 4
[Huawei-ui-vty0-4] user privilege level 15
```
📌 `level 15` = akses tertinggi (administrator).  

Coba remote ulang → sekarang Telnet dapat menjalankan semua command.  
📷 [Output Final](https://drive.google.com/file/d/1Jc0Pp02zi8RQte767RwsuDey3FHrYRpk/view?usp=drive_link)  

---

# Ringkasan Materi  

✅ **Lab 1:** Koneksi dasar antar PC (end device).  
✅ **Lab 2:** Konfigurasi dasar Router Huawei (`display`, `dir`, `system-view`, interface, banner, password).  
✅ **Lab 3:** Remote Router via Telnet dari Host OS menggunakan Loopback Adapter + PuTTY.  

Dengan 3 lab ini, kita sudah memahami dasar penggunaan **Huawei eNSP**, mulai dari konfigurasi IP, command dasar, hingga remote router.  
🚀 Selanjutnya kita bisa masuk ke materi **STP & RSTP** lebih lanjut.  