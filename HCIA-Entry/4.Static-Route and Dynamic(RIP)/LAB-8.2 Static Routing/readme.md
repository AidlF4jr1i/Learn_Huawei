# 8.2 Static Routing

## Memahami Static Routing

Routing adalah proses yang memungkinkan perangkat di jaringan yang berbeda untuk dapat saling berkomunikasi. Proses ini ibarat memilih jalan terbaik bagi paket data untuk sampai ke tujuannya.

Terdapat dua pendekatan utama untuk routing:
- **Dynamic Routing (otomatis)**
- **Static Routing (manual)**

### Apa itu Static Routing?
Static Routing adalah metode routing di mana administrator jaringan **menambahkan rute secara manual** ke dalam tabel routing sebuah router.

Contohnya:
> Untuk mencapai jaringan X, kirimkan paket melalui router tetangga Y.

Perintah dasar di perangkat Huawei:

```bash
system-view
ip route-static <network-tujuan> <subnet-mask> <ip-gateway-tetangga>
```

---

## Kapan Static Route Kurang Efisien?
Jika router tetangga terhubung ke banyak jaringan, maka setiap jaringan harus dimasukkan satu per satu secara manual, yang tentu **tidak efisien**.

Solusinya adalah dengan **Default Static Route**, yaitu rute pamungkas yang mengarahkan semua paket yang tujuannya tidak diketahui ke satu gateway.

```bash
ip route-static 0.0.0.0 0.0.0.0 <ip-gateway-tetangga>
```

Angka `0.0.0.0 0.0.0.0` mewakili **semua jaringan**.

> Penting: Komunikasi dua arah hanya bisa berjalan jika router tetangga juga memiliki rute balik (return route).

---

## LAB 8.2: Konfigurasi Static Route Dasar

Tujuan: Menghubungkan dua jaringan berbeda menggunakan static route agar PC dari kedua sisi bisa saling berkomunikasi.

🔗 [Topologi Jaringan](https://drive.google.com/open?id=1Q5gmhx_UrzL-_dlGlIfJYhA00oy9CR-_&usp=drive_fs)

---

### Konfigurasi Router 1 (R1)
```bash
system-view
interface GigabitEthernet0/0/1
ip address 192.168.1.1 24
quit

interface GigabitEthernet0/0/0
ip address 10.0.0.1 24
quit

ip route-static 172.16.1.0 255.255.255.0 192.168.1.2
# ip route-static 0.0.0.0 0.0.0.0 192.168.1.2  (opsional)

display ip routing-table
```

**Output Routing Table R1:**
```
Destination/Mask     Proto   Pre  Cost  Flags  NextHop        Interface
0.0.0.0/0            Static  60   0     RD     192.168.1.2    GE0/0/1
10.0.0.0/24          Direct  0    0      D     10.0.0.1       GE0/0/0
172.16.1.0/24        Static  60   0     RD     192.168.1.2    GE0/0/1
192.168.1.0/24       Direct  0    0      D     192.168.1.1    GE0/0/1
```

---

### Konfigurasi Router 2 (R2)
```bash
system-view
interface GigabitEthernet0/0/1
ip address 192.168.1.2 24
quit

interface GigabitEthernet0/0/0
ip address 172.16.1.1 24
quit

ip route-static 10.0.0.0 255.255.255.0 192.168.1.1
display ip routing-table
```

**Output Routing Table R2:**
```
Destination/Mask     Proto   Pre  Cost  Flags  NextHop        Interface
0.0.0.0/0            Static  60   0     RD     192.168.1.1    GE0/0/1
10.0.0.0/24          Static  60   0     RD     192.168.1.1    GE0/0/1
172.16.1.0/24        Direct  0    0      D     172.16.1.1     GE0/0/0
192.168.1.0/24       Direct  0    0      D     192.168.1.2    GE0/0/1
```

---

### Pengujian
Lakukan `ping` dari PC2 ke PC1.

🔗 [Hasil Pengujian](https://drive.google.com/open?id=1gaXIeqANauqS0wyuHViin9LVgvQXNJ4D&usp=drive_fs)

---

## LAB 8.3: Floating Static Route untuk Failover

### Konsep Floating Static Route
Floating Static Route digunakan untuk menyediakan **jalur cadangan (backup)** jika jalur utama gagal.

- **Jalur Utama** → preference default = 60  
- **Jalur Cadangan (Floating)** → preference lebih tinggi (misalnya 70)

Perintah:
```bash
# Jalur utama
ip route-static <network-tujuan> <subnet-mask> <ip-gateway-utama>

# Jalur cadangan
ip route-static <network-tujuan> <subnet-mask> <ip-gateway-cadangan> preference <nilai-preference>
```

🔗 [Topologi Jaringan](https://drive.google.com/open?id=1q0U11Q_SdVIcrvKI0QejFXlzNOrIcKxv&usp=drive_fs)

---

### Konfigurasi Lengkap

#### Router 1 (R1)
```bash
system-view
interface g2/0/0
ip address 10.0.0.1 24
quit

interface g0/0/0
ip address 192.168.12.1 24
quit

interface g0/0/1
ip address 192.168.21.1 24
quit

ip route-static 172.16.0.0 255.255.255.0 192.168.12.2
ip route-static 172.16.0.0 255.255.255.0 192.168.21.2 preference 70
```

#### Router 2 (R2)
```bash
system-view
interface g2/0/0
ip address 172.16.0.1 24
quit

interface g0/0/0
ip address 192.168.12.2 24
quit

interface g0/0/1
ip address 192.168.21.2 24
quit

ip route-static 10.0.0.0 255.255.255.0 192.168.12.1
ip route-static 10.0.0.0 255.255.255.0 192.168.21.1 preference 70
```

---

### Pengujian Failover

1. **Verifikasi Jalur Utama**
   - Jalankan `ping` dan `tracert` dari PC1 ke PC2.
   - Gateway utama: `192.168.12.2`  
     🔗 [Gambar 1(Ping)](https://drive.google.com/open?id=1-j5V2OOzcjK8aJXXKo_buPbehcZ7mduI&usp=drive_fs)
     🔗 [Gambar 2(Tracert)](https://drive.google.com/open?id=13Cl-vNhzsPdThSd_ZX62sVGGFhn3vpP-&usp=drive_fs)

2. **Simulasi Kegagalan Jalur Utama**
   ```bash
   interface GigabitEthernet0/0/0
   shutdown
   ```
   🔗 [Cara mematikan interface](https://drive.google.com/open?id=1KcF-BbXgGlB8Kpfk5yd4WibwgL8Hd6q4&usp=drive_fs)

3. **Verifikasi Jalur Cadangan**
   Jalankan kembali `ping` dan `tracert`.
   - Gateway cadangan: `192.168.21.2`
   - 🔗 [Gambar 3(Ping)](https://drive.google.com/open?id=1MWvZ9e3DfPzVNapGSKDWMQTFh7lVbPPm&usp=drive_fs)
   - 🔗 [Gambar 4(Tracert)](https://drive.google.com/open?id=1otgdYLecgbb1aPy6pflvfpdZ-QlkH-3B&usp=drive_fs)

   Untuk mengaktifkan kembali interface:
   ```bash
   interface GigabitEthernet0/0/0
   undo shutdown
   ```

---

## Ringkasan Static Routing

**Definisi Inti:**  
Static Routing adalah metode konfigurasi rute secara manual tanpa pertukaran informasi antar router.

**Kelebihan:**
- Keamanan tinggi (tidak ada pertukaran rute otomatis)
- Beban CPU ringan
- Kontrol penuh oleh administrator

**Kekurangan:**
- Tidak skalabel untuk jaringan besar
- Tidak adaptif terhadap perubahan topologi

**Jenis-jenis Static Route:**
- **Specific Static Route:** Untuk satu jaringan tertentu.
- **Default Static Route:** Untuk semua jaringan (`0.0.0.0/0`).
- **Floating Static Route:** Jalur backup (failover).
