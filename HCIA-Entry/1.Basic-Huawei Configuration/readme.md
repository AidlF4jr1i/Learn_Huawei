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

# Kesimpulan

Pada lab kedua ini kita belajar bagaimana melakukan konfigurasi dasar di router Huawei, mulai dari menampilkan informasi (`display`), masuk ke mode konfigurasi (`system-view`), mengatur IP address, hostname, banner, console access, hingga manajemen waktu.

Secara konsep, perintah Huawei mirip dengan Cisco, hanya berbeda di syntax. Jika terbiasa dengan Cisco, adaptasi ke Huawei cukup mudah.