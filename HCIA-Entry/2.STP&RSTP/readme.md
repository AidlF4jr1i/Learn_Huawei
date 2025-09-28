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

RSTP merupakan salah satu dari 3 protokol STP yang ada di Huawei, bedanya dengan STP, RSTP menawarkan proses yang jauh lebih cepat bila terjadi skenario jaringan putus dimana di STP kita harus menunggu selama 50 detik maka RSTP akan memproses lebih cepat dari itu, untuk LAB RSTP kita akan menggunakan Topologi berikut: 'https://drive.google.com/open?id=1N5ImD0b-qh8o7nfWYpQMXYNQsK5Wn3I2&usp=drive_fs'

pada LAB ini seperti biasa kita akan melakukan cek dahulu stp nya dan melihat switch mana yang menjadi root bridge, hali ini ditunjukkan bila kita menggunakan 'disp stp' dan mac address pada menu CIST brdige  CIST root, dan CIST RegRoot sama, dan bila menggunakan 'disp stp br' maka seluruh port nya dalah Designated port! next kita akan menjalankan command untuk masing-masing switch :

- stp mode rstp

, next kita akan melakukan beberapa penyesuaian:

- kita akan mensetting mode edge-port enable untuk setiap interface yang mengarah ke PC dari Switch, mengapa? hal ini untuk menyatakan pada Switch bahwa PC tidak termasuk dalam bagian Topologi secara keseluruhan dan hanya bertugas sebagai end-device saja, kalau kita tidak mengkonfigurasi nya maka interface yang mengarah ke PC(terkhusus untuk Switch non root bridge) akan diklasifikasikan sebagai root port padahal harusnya tidak, hal ini akan menyebabkan masalah dimana ketika ada peruahan jaringan pada salah satu switch PC akan ikut dalam  proses discarding --> learning --> forwarding yang mana akan membuat galat jaringan selama 1 menit yang harusnya tidak dirasakan

- next kita akan mengkonfigurasi BPDU juga di interface yang sama, so apa itu BPDU?, BPDU(Bridge Protocol Data Unit) merupakan sebuah mekanisme pengiriman "pesan" yang digunakan oleh switch untuk berkomunikasi satu sama lain dalam proses STP. Pesan ini berisi informasi untuk memilih root bridge dan mencegah looping di jaringan. nah disini kita akan mensetting agar di jaringan end-device tidak menerima pesan BPDU apapun(walaupun sebenarnya memank tidak karena hanya dilakukan oleh switch), ini untuk meminimalisir bila mana dikemudian hari di interface yang tersambung PC tiba-tiba berganti menjadi Switch, apa yang terjadi? switch baru akan mengirimkan BPDU ke switch lain dan akan merubah keseluruhan topologi yang tentunya akan menjadi kekacauan apalagi kalau sw varu tsbt menjadi root bridge whuuhh 'mengerikann', okeh so next kita akan menjalankan :

- system-view
- int <nama interface>
- stp edge-port enabled
- quit
- stp bpdu-protection

di kedua switch, nantinya pada interface stp di masing-masing swicth akan menjadi:

- Switch Root bridge:
[Huawei]disp stp br
 MSTID  Port                        Role  STP State     Protection
   0    GigabitEthernet0/0/1        DESI  FORWARDING      BPDU
   0    GigabitEthernet0/0/2        DESI  FORWARDING      NONE
   0    GigabitEthernet0/0/3        DESI  FORWARDING      NONE

- Switch Root PortL
[Huawei]disp stp br
 MSTID  Port                        Role  STP State     Protection
   0    GigabitEthernet0/0/1        DESI  FORWARDING      BPDU
   0    GigabitEthernet0/0/2        ROOT  FORWARDING      NONE
   0    GigabitEthernet0/0/3        ALTE  DISCARDING      NONE

okeh, setelah ini, langkah terakhir adalah menjalankan command loop protection untuk alternate port dan juga root port, loh buat apa?, jadi gini bayangin ada skenario dimana SW B(non root bridge) di bagian alternate port nya tidak tiba-tiba tdak menerima BPDU apa yang terjadi?, yang terjadi adalah SW B akan menganggap bahwa alternate port tidak terhubung lagi ke Switch tetangganya(root bridge) sehingga akan mengembalikan statusnya menjadi 'forwarding' akibatnya skenario looping akan kembali terjadi, nah lalu skenario ke-2 dimana giliran root port yang tiba-tiba tidak menerima BPDU dari SW tetangga(root bridge) maka yang terjadi justru lebih parah yakni SW B akan 'mendeklarasikan' diirnya sebagai root bridge yang baru dan mengirimkan semua BPDU dari seluruh port yang tehrubung sehingga akan ada 2 SW yang menjadi Root bridge dimana sudah pasti ini akan menyebabkan proses looping juga karena tidak seharsunya ada 2 Root bridge dalam satu jaringan!, cara mengkonfigurasi loop protection juga cukup mudah, kita cukup jalankan :

- int <interface root port/alternate>
- stp loop-protection

dan hasilnya akan seperti ini : '
[Huawei]disp stp br
 MSTID  Port                        Role  STP State     Protection
   0    GigabitEthernet0/0/1        DESI  FORWARDING      BPDU
   0    GigabitEthernet0/0/2        ROOT  FORWARDING      NONE
   0    GigabitEthernet0/0/3        ALTE  DISCARDING      NONE
', okeh next kebagian paling penting adalah perbandingan antara STP dan juga RSTP, sebelumnya saya bilang bahwa STP lebih lambat dari RSTP tapi saya belum menunjukkan buktinya, disini kita akan menjalankan skenario dimana pada LAB STP & RSTP kita akan menjalankan ping endless dari PC1 ke PC2 lalu  mematikan root port pada switch dan lihat berapa lama RTO terjadi sampai jaringan kembali bisa terjalin. singkatnya ini dia hasilnya:

- STP:"

Ping statistics for 192.168.2.253:
    Packets: Sent = 25, Received = 20, Lost = 5 (20% loss),
Approximate round trip times in milli-seconds:
    Minimum = 94ms, Maximum = 2201ms, Average = 220ms
",

- RSTP:"
--- 192.168.2.253 ping statistics ---
  69 packet(s) transmitted
  69 packet(s) received
  0.00% packet loss
  round-trip min/avg/max = 47/80/437 ms
", lihat RSTP terbukti lebih cepat dalam menanani perubahan jaringan dibuktikan dengan tidak adanya RTO yang terjadi kalaupun ada maka itu akan terjadi di 1-2 RTO saja berbeda dengan STP

## Ringkasan RSTP
<masukkan ringkasan disini>

### Bonus

setelah membahas mengenai STP dan RSTP, lalu bagaiamna dengan MSTP?
<bantu jelaskan secara detail dan ringkas disini>

# Ringkasan Materi keseluruhan