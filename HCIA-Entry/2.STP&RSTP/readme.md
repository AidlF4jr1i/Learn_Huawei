## Pendahuluan: Spanning Tree Protocol (STP)
Dalam dunia jaringan, redundansi link antar switch sangat penting untuk memastikan ketersediaan jaringan yang tinggi. Namun, redundansi ini dapat menciptakan masalah baru yang disebut *switching loop*. Ketika loop terjadi, frame Ethernet akan berputar tanpa henti di antara switch, menyebabkan *broadcast storm* yang dapat melumpuhkan seluruh jaringan karena penggunaan CPU dan bandwidth yang melonjak drastis.

Untuk mengatasi masalah ini, hadirlah **Spanning Tree Protocol (STP)**. STP adalah protokol Layer 2 yang bertugas untuk menciptakan topologi jaringan bebas loop (loop-free) secara logis dalam sebuah jaringan fisik yang memiliki link redundan. Cara kerjanya adalah dengan memblokir port-port tertentu secara cerdas untuk memastikan hanya ada satu jalur aktif antara dua titik di jaringan pada satu waktu. Jika jalur utama gagal, STP akan secara otomatis membuka port yang diblokir untuk menyediakan jalur alternatif, sehingga menjaga konektivitas jaringan tetap berjalan.

Konsep ini bersifat universal dan diimplementasikan oleh berbagai vendor, termasuk Huawei. Pada perangkat Huawei, STP (dan varian lanjutannya seperti RSTP dan MSTP) memastikan stabilitas dan keandalan jaringan Layer 2.

---

## LAB 4: Memahami STP Fundamental
Pada lab ini, kita akan mengeksplorasi cara kerja dasar STP menggunakan perangkat switch Huawei S5700 di eNSP.

### Topologi Jaringan
Gambar Topologi: `1.1Topology_STP.png`[Topology](https://drive.google.com/open?id=1SPaXhAg7YwdiIRVwsJirCLC7b3luqUME&usp=drive_fs) 

### Langkah-langkah Lab:
1. **Konfigurasi IP Address**  
   - PC1: `192.168.2.254/24`  
   - PC2: `192.168.2.253/24`

2. **Analisis Aktivitas STP**  
   Setelah semua kabel terhubung, gunakan Wireshark pada salah satu link antar-switch. Anda akan melihat frame STP (atau MSTP secara default di Huawei) yang saling dipertukarkan. Ini menunjukkan bahwa switch secara aktif berkomunikasi untuk membangun topologi bebas loop di belakang layar.  

   Gambar Aktivitas STP: `1.2 Capture Wireshark STP.png`
 
   - [File Wireshark Capture](https://drive.google.com/open?id=10ZSAUGV5T7l8_NAjWg2iyAXxhBHRQl0x&usp=drive_fs)

---

## Konsep Inti dalam STP
1. **Bridge ID (BID)**  
   Identitas unik setiap switch dalam proses STP. BID terdiri dari:  
   - *Bridge Priority* (default: 32768)  
   - *MAC Address switch*  

   Switch dengan BID terendah akan terpilih sebagai pusat topologi STP.  
   Formula: `Bridge ID = Bridge Priority + MAC Address`

2. **Root Bridge**  
   Switch dengan Bridge ID terendah di seluruh jaringan. Semua port pada Root Bridge adalah **Designated Port**.

3. **Designated Port (DP)**  
   Port yang bertanggung jawab untuk meneruskan traffic ke segmen jaringan tertentu.

4. **Root Port (RP)**  
   Port pada switch non-root dengan jalur terbaik ke Root Bridge.

5. **Alternate Port (AP) / Blocked Port**  
   Port yang diblokir oleh STP untuk mencegah loop.

---

## Konfigurasi dan Verifikasi STP di Huawei
Secara default, perangkat Huawei menggunakan MSTP. Untuk pembelajaran, ubah ke mode STP tradisional.

```bash
# Melihat mode STP aktif
display stp

# Melihat status port STP
display stp brief

# Ubah mode STP (jalankan di semua switch)
[Huawei] system-view
[Huawei] stp mode stp
```

---

## Modifikasi Pemilihan Root Bridge
1. **Menjadikan Switch sebagai Root Bridge Utama (Primary Root):**
```bash
[SW1] system-view
[SW1] stp root primary
[SW1] display stp brief
[SW1] undo stp root
```

2. **Mengubah Priority Secara Manual:**
```bash
[SW2] system-view
[SW2] stp priority 8192
[SW2] display stp brief
[SW2] undo stp priority
```

---

## Catatan Penting: Konvergensi STP
Proses status port:  
- Disabled / Blocking  
- Listening (15 detik)  
- Learning (15 detik)  
- Forwarding  
- Blocking (Alternate Port)  

Total waktu konvergensi: **30-50 detik**.

---

## Ringkasan STP
- Mencegah loop dengan blokir port redundan  
- Root Bridge dipilih berdasarkan BID terendah  
- Port berperan sebagai Root, Designated, atau Alternate  
- Kekurangan: **konvergensi lambat**

---

# LAB 5 RSTP

**Intro singkat:** RSTP (IEEE 802.1w) adalah evolusi STP yang dirancang untuk mengurangi waktu konvergensi secara drastis. Pada perangkat Huawei, RSTP adalah salah satu mode STP yang tersedia (selain STP klasik dan MSTP). RSTP cocok untuk jaringan modern yang memerlukan pemulihan cepat ketika terjadi perubahan topologi.

**Topologi LAB RSTP:** gunakan file topologi berikut:  
[Topology](https://drive.google.com/open?id=10ZSAUGV5T7l8_NAjWg2iyAXxhBHRQl0x&usp=drive_fs)

### Langkah awal — verifikasi STP/RSTP aktif dan pemilihan root
Sebelum perubahan, cek mode dan status STP/RSTP:

```bash
# Periksa mode dan informasi CIST/Root
display stp
display stp brief    # atau disp stp br pada beberapa platform
```

Perhatikan informasi pada menu CIST Bridge / CIST Root / CIST Regional Root (CIST RegRoot). Jika `disp stp br` menunjukkan seluruh port sebagai **Designated (DESI)**, itu berarti switch tersebut berperan sebagai Root Bridge untuk CIST/MST.

### Mengaktifkan mode RSTP
Jalankan pada setiap switch:

```bash
[Huawei] system-view
[Huawei] stp mode rstp
```

Verifikasi kembali dengan `display stp brief` untuk melihat peran port setelah mode berubah.

---

### Penyesuaian penting untuk lingkungan dengan end‑device (PC)
Pada lab ini kita akan melakukan beberapa penyesuaian agar end‑device (PC) tidak mempengaruhi topologi STP/RSTP:

#### 1. Konfigurasi Edge Port (stp edge-port)
Mengapa perlu? Port yang terhubung ke end‑device (PC, server) **tidak** termasuk dalam topologi redundansi — mereka seharusnya tidak ikut dalam pemilihan Root Port atau proses discarding/learning yang memperlambat konektivitas ketika terjadi perubahan topologi. Jika tidak dikonfigurasi, interface ke PC (terutama pada switch non‑root) bisa salah dikategorikan sebagai Root Port sehingga menjadi bagian dari proses konvergensi dan menyebabkan gangguan pada end‑device selama perubahan topologi.

**Perintah:**

```bash
[SW] system-view
[SW] interface <nama-interface>    # mis. GigabitEthernet0/0/1
[SW-GigabitEthernet0/0/1] stp edge-port enable
[SW-GigabitEthernet0/0/1] quit
```

Ulangi untuk setiap interface yang mengarah ke PC.

#### 2. Aktifkan BPDU Protection (stp bpdu-protection)
**Apa itu BPDU?** BPDU (Bridge Protocol Data Unit) adalah pesan yang dipertukarkan switch untuk membentuk topologi STP: informasi root, cost, dan lain‑lain. Kita ingin mencegah end‑device (atau perangkat yang salah) mengirim/menyebabkan BPDU masuk ke jaringan dan mengubah topologi secara tidak sengaja (misal: seseorang mencolokkan switch baru pada port PC).

Dengan mengaktifkan `stp bpdu-protection` secara global setelah edge port dikonfigurasi, apabila port edge menerima BPDU, port tersebut dapat dijadikan `error-down` atau dimatikan untuk mencegah perubahan topologi yang tidak diinginkan.

**Perintah:**

```bash
[SW] system-view
[SW] stp bpdu-protection
```

Pada tampilan `display stp` atau `display stp brief` Anda akan melihat kolom `Protection` menandakan `BPDU` pada port yang dilindungi.

**Contoh hasil verifikasi:**

- *Switch Root Bridge output (`disp stp br`)*
```
 MSTID  Port                    Role  STP State     Protection
   0    GigabitEthernet0/0/1    DESI  FORWARDING    BPDU
   0    GigabitEthernet0/0/2    DESI  FORWARDING    NONE
   0    GigabitEthernet0/0/3    DESI  FORWARDING    NONE
```

- *Switch Non‑Root Bridge output (`disp stp br`)*
```
 MSTID  Port                    Role  STP State     Protection
   0    GigabitEthernet0/0/1    DESI  FORWARDING    BPDU
   0    GigabitEthernet0/0/2    ROOT  FORWARDING    NONE
   0    GigabitEthernet0/0/3    ALTE  DISCARDING    NONE
```

---

### Loop Protection (stp loop-protection)
**Kenapa diperlukan?** Bayangkan 2 skenario masalah BPDU:

1. **Alternate port** tiba‑tiba berhenti menerima BPDU dari tetangga (mis. link unidirectional). Jika tidak ada proteksi, port ini bisa menganggap link putus dan beralih ke `FORWARDING` — yang dapat menyebabkan looping.  
2. **Root port** tiba‑tiba berhenti menerima BPDU. Switch non‑root bisa mengira dirinya terputus dari root, lalu mencoba menjadi Root Bridge baru, sehingga ada dua Root Bridge di jaringan — jelas ini menyebabkan kekacauan dan loop.

`stp loop-protection` mencegah kedua skenario di atas dengan membuat port tetap dalam status `DISCARDING` (atau minimal tidak langsung menjadi `DESIGNATED`) saat BPDU hilang sehingga loop tidak terjadi.

**Konfigurasi:**

```bash
[SW] system-view
[SW] interface <interface-root-or-alternate>
[SW-if] stp loop-protection
```

**Contoh verifikasi (`disp stp br`):**

```
 MSTID  Port                    Role  STP State     Protection
   0    GigabitEthernet0/0/1    DESI  FORWARDING    BPDU
   0    GigabitEthernet0/0/2    ROOT  FORWARDING    NONE
   0    GigabitEthernet0/0/3    ALTE  DISCARDING    NONE
```

---

### Perbandingan Kecepatan Konvergensi: STP vs RSTP (uji ping)
Untuk membuktikan klaim RSTP yang lebih cepat, lakukan pengujian real‑time dengan:

1. Jalankan `ping` terus‑menerus (endless ping) dari PC1 → PC2.  
2. Matikan interface yang berfungsi sebagai **Root Port** pada switch non‑root dan amati jumlah RTO / packet loss.

**Hasil contoh pengujian:**

- **STP**
```
Ping statistics for 192.168.2.253:
    Packets: Sent = 25, Received = 20, Lost = 5 (20% loss)
Approximate round trip times in milli-seconds:
    Minimum = 94ms, Maximum = 2201ms, Average = 220ms
```

- **RSTP**
```
--- 192.168.2.253 ping statistics ---
  69 packet(s) transmitted
  69 packet(s) received
  0.00% packet loss
  round-trip min/avg/max = 47/80/437 ms
```

**Interpretasi:** RSTP menunjukkan hampir tidak ada packet loss (0% pada contoh), atau paling banyak 1–2 RTO singkat. Sementara STP klasik menunjukkan packet loss yang signifikan (contoh: 20%), menggambarkan konvergensi lambat (puluhan detik). Ini membuktikan bahwa RSTP memulihkan jalur alternatif jauh lebih cepat.

---

## Ringkasan RSTP
- **Standar:** IEEE 802.1w.  
- **Waktu konvergensi:** Sangat cepat (beberapa detik atau kurang).  
- **Peran port:** Root, Designated, Alternate, Backup (lebih tegas dibanding STP).  
- **Fitur penting:** Edge Port (`stp edge-port enable`), BPDU Protection (`stp bpdu-protection`), Loop Protection (`stp loop-protection`).  
- **Keunggulan:** Meminimalkan packet loss saat terjadi perubahan topologi; ideal untuk jaringan yang membutuhkan ketersediaan tinggi.  
- **Rekomendasi konfigurasi di lab/produksi:**  
  - Aktifkan `stp mode rstp` pada semua switch.  
  - Tandai port end‑device sebagai `edge-port` untuk mencegah dampak konvergensi pada end‑device.  
  - Aktifkan `stp bpdu-protection` setelah edge‑port dikonfigurasi.  
  - Aktifkan `stp loop-protection` pada Root/Alternate port yang rentan.  

---

## Bonus: Mengenal MSTP (Multiple Spanning Tree Protocol)
**Apa itu MSTP?** MSTP (IEEE 802.1s) adalah pengembangan dari RSTP yang memungkinkan administrator membuat beberapa *instance* Spanning Tree di dalam satu jaringan fisik. Tiap *instance* (atau MST instance) dapat memiliki topologi sendiri dan Root Bridge sendiri — sehingga cocok untuk lingkungan multi‑VLAN.

**Masalah yang diselesaikan MSTP:**  
- STP/RSTP hanya menyediakan **satu** topologi Spanning Tree untuk seluruh jaringan (semua VLAN berbagi topologi yang sama). Akibatnya beberapa link bisa tidak terpakai untuk VLAN tertentu dan tidak ada load‑balancing.  
- MSTP memungkinkan beberapa topologi paralel: VLAN dikelompokkan ke dalam beberapa instance, masing‑masing instance dapat memilih Root Bridge berbeda, sehingga link redundan dapat dimanfaatkan secara lebih efisien (load balancing per‑VLAN).

**Konsep kunci:**  
- **Region & Instance Mapping:** Administrator mendefinisikan region MST dan peta VLAN→MST instance (contoh: instance 1 = VLAN 10–20, instance 2 = VLAN 30–40).  
- **Per‑instance Root Bridge:** Tiap instance dapat memiliki root yang berbeda.  
- **Kinerja:** Menggabungkan kecepatan RSTP (konvergensi cepat) dan kemampuan load‑balancing berbasis VLAN. Pada perangkat Huawei, **MSTP** biasanya adalah mode default.

**Contoh singkat penggunaan (konsep):**  
- Instance 1: VLAN 10–20 → Root Bridge: SW1  
- Instance 2: VLAN 30–40 → Root Bridge: SW2

Dengan konfigurasi tersebut, traffic untuk VLAN 10–20 mengambil jalur via SW1 sedangkan VLAN 30–40 via SW2 — link yang diblokir untuk instance tertentu bisa aktif untuk instance lain sehingga memaksimalkan pemakaian bandwidth.

**Catatan:** Konfigurasi MSTP memerlukan perencanaan VLAN → instance dan konsistensi konfigurasi region/instance di seluruh switch agar tidak terjadi ketidaksamaan pandangan topologi antar switch.

---

## Ringkasan Materi Keseluruhan
| Protokol | Standar | Waktu Konvergensi | Status Port | Peran Port | Load Balancing | Default Huawei |
|----------|---------|-------------------:|-------------|------------|----------------|----------------|
| STP      | IEEE 802.1D | 30–50 detik | Blocking, Listening, Learning, Forwarding | Root, Designated, Alternate | Tidak | Tidak |
| RSTP     | IEEE 802.1w | Beberapa detik atau kurang | Discarding, Learning, Forwarding | Root, Designated, Alternate, Backup | Tidak | Tidak |
| MSTP     | IEEE 802.1s | Cepat (sama seperti RSTP) | Discarding, Learning, Forwarding (per instance) | Sama seperti RSTP per‑instance | Ya (per VLAN instance) | Ya |

---

**Catatan akhir & best practice singkat:**  
- Untuk jaringan enterprise: gunakan **MSTP** jika ada banyak VLAN dan Anda butuh load balancing.  
- Untuk jaringan yang menuntut pemulihan cepat tapi sederhana: **RSTP** adalah pilihan yang baik.  
- Untuk lab pembelajaran/compatibility, pahami perbedaan masing‑masing mode dan praktikkan konfigurasi `edge‑port`, `bpdu‑protection`, dan `loop‑protection` seperti diberikan di atas.

---