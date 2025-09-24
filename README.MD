# Belajar Konfigurasi Router Huawei dengan eNSP

Pada section ini, kita akan mempelajari bagaimana cara melakukan konfigurasi pada perangkat **Router Huawei** menggunakan platform **eNSP**.  

👉 Bagi yang belum memiliki aplikasi **eNSP**, silakan download terlebih dahulu pada link resmi Huawei Enterprise.  

---

## Hal-hal yang Perlu Diperhatikan Saat Install eNSP

1. **Siapkan ruang untuk aplikasi pendukung** yang wajib diinstal bersama eNSP, yaitu:  
   - `VirtualBox-5.2.44-139111-Win`  
   - `vlc-3.0.21-win64.exe`  
   - `WinPcap_4_1_3`  
   - `Wireshark-win64-3.6.10`

   > Catatan: Versi di atas adalah versi yang sudah dites kompatibel dengan eNSP. Jika menggunakan versi lain, bisa saja terjadi error.

2. **Jika sebelumnya sudah menginstal aplikasi dengan nama yang sama**, lakukan uninstall terlebih dahulu, lalu instal kembali versi yang diminta oleh eNSP.  

3. **Uninstall Npcap** (jika ada), karena eNSP hanya mendukung **WinPcap** sebagai penghubung antara jaringan virtual dengan jaringan host.  

---

## Materi yang Akan Dipelajari

Pada bagian pembelajaran ini, kita akan membahas beberapa topik dasar hingga intermediate terkait konfigurasi di Huawei Router, antara lain:

- **Basic Huawei Configuration** (konfigurasi dasar perangkat Huawei)  
- **STP & RSTP** (Spanning Tree Protocol & Rapid Spanning Tree Protocol)  
- **DHCP (Dynamic Host Configuration Protocol)**  
- **Static Routing & Dynamic Routing (RIP)**  

---

## Metode Pembelajaran

Di section ini, kita akan lebih banyak **berfokus pada lab skenario** dan penjelasan dari hasil pengerjaan lab.  

Tujuannya adalah agar teman-teman bisa langsung mencoba:  
- Mengimpor lab yang sudah disediakan.  
- Melakukan konfigurasi step by step.  
- Menguji hasil konfigurasi secara langsung di ekosistem **eNSP**.  

Dengan begitu, pembelajaran akan lebih mudah dipahami karena langsung dipraktikkan.  

---
```