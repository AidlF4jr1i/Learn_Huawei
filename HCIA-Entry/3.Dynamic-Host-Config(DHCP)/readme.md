# DHCP SERVER & RELAY

## Konfigurasi Dasar DHCP Server pada Router Huawei

### 1. Pengenalan DHCP
DHCP (Dynamic Host Configuration Protocol) adalah fitur pada perangkat jaringan, termasuk router Huawei, 
yang berfungsi untuk memberikan dan mengelola alokasi alamat IP secara otomatis kepada perangkat (klien) dalam satu jaringan. 
Konsep ini serupa dengan yang diterapkan oleh vendor lain seperti MikroTik dan Cisco, sehingga konfigurasinya tidak terlalu rumit 
jika Anda sudah terbiasa dengan konsep dasarnya.

Pada tutorial ini, kita akan melakukan konfigurasi DHCP Server menggunakan topologi laboratorium berikut:

- **Topologi:** Lihat Topologi Jaringan  

Berdasarkan topologi tersebut, kita akan mengkonfigurasi R2 sebagai DHCP Server.

---

### 2. Konfigurasi DHCP Server (di R2)
Langkah-langkah berikut dilakukan pada router yang akan dijadikan sebagai server DHCP (R2).

#### Langkah 2.1: Aktifkan Layanan DHCP
```bash
<Huawei> system-view
[Huawei] dhcp enable
```

#### Langkah 2.2: Konfigurasi Interface
Konfigurasikan interface yang terhubung ke switch (SW). Interface ini akan bertindak sebagai gateway untuk klien.

```bash
[Huawei] interface GigabitEthernet0/0/0
[Huawei-GigabitEthernet0/0/0] ip address 192.168.1.1 24
[Huawei-GigabitEthernet0/0/0] dhcp select interface
```

🔗 [Topologi DHCP Server](https://drive.google.com/open?id=1h-l-IMjnWDCuHKgzsmdEHta7FyVRbmuH&usp=drive_fs)

#### Langkah 2.3: Konfigurasi Opsi DHCP Server
Beberapa opsi yang dapat digunakan:
- **DNS**  
- **Lease Time**  
- **Excluded IP**  

```bash
[Huawei-GigabitEthernet0/0/0] dhcp server dns-list 8.8.8.8
[Huawei-GigabitEthernet0/0/0] dhcp server lease day 1 hour 1 minute 30
[Huawei-GigabitEthernet0/0/0] dhcp server excluded-ip-address 192.168.1.2
```

---

### 3. Konfigurasi DHCP Client (Router & PC)

#### Langkah 3.1: Konfigurasi Router Klien
```bash
<Huawei> system-view
[Huawei] interface GigabitEthernet0/0/0
[Huawei-GigabitEthernet0/0/0] ip address dhcp-alloc
```

#### Langkah 3.2: Konfigurasi PC Klien
Ubah pengaturan IP dari **Static** menjadi **DHCP**.


🔗 [Setting PC](https://drive.google.com/open?id=15cQj9jj-vkh9AKft--nuJYi8Rw1g6dN-&usp=drive_fs)  
🔗 [output PC](https://drive.google.com/open?id=1R1vo2lbZmgPZSlLCDAO6eoHpVdbkrb_L&usp=drive_fs)
---

### 4. Verifikasi Konfigurasi

#### Langkah 4.1: Verifikasi di Sisi Klien
```bash
[Huawei-Client] display ip interface brief
```

#### Langkah 4.2: Verifikasi di Sisi Server
```bash
[Huawei] display dhcp server statistics
```


---

### Ringkasan DHCP Server
1. Aktifkan DHCP (`dhcp enable`)  
2. Konfigurasi interface (`dhcp select interface`)  
3. Tambahkan parameter opsional (DNS, Lease, Excluded IP)  
4. Klien hanya perlu diset DHCP (`ip address dhcp-alloc`)  
5. Verifikasi dengan `display` command  

---

## LAB 7: DHCP RELAY

### 1. Pengenalan DHCP Relay
DHCP Relay adalah mekanisme untuk meneruskan pesan DHCP (broadcast) ke server DHCP di jaringan lain, 
karena broadcast tidak bisa melewati router.

Contoh:  
- **R1** sebagai DHCP Server  
- **R3** sebagai Client  
- **R2** sebagai DHCP Relay

🔗 [Topologi DHCP Relay](https://drive.google.com/open?id=1jKS0IVoOCm1pfFFu3-JK_xdTvMVs-tTC&usp=drive_fs)

---

### 2. Konfigurasi Awal (IP Address & DHCP Enable)

#### Langkah 2.1: Pengaturan IP Address
**Di R1:**
```bash
system-view
int g0/0/0
ip add 192.168.12.1 24
```

**Di R2:**
```bash
system-view
int g0/0/0
ip add 192.168.12.2 24
int g0/0/1
ip add 172.16.0.1 24
```

#### Langkah 2.2: Mengaktifkan DHCP
```bash
dhcp enabled
```

---

### 3. Konfigurasi DHCP Server (di R1)
```bash
system-view
ip pool 0
network 172.16.0.0 mask 255.255.255.0
gateway-list 172.16.0.1
dns-list 8.8.8.8
lease day 3 hour 10
excluded-ip-address 172.16.0.254
quit
ip route-static 172.16.0.0 255.255.255.0 192.168.12.2
int g0/0/0
dhcp select global
```

---

### 4. Konfigurasi DHCP Relay (di R2)
```bash
system-view
int g0/0/1
dhcp select relay
dhcp relay server-ip 192.168.12.1
```

---

### 5. Verifikasi di Sisi Client
- **R3** dan **PC** jalankan `ip add dhcp-alloc`  
- Cek dengan `display ip interface brief`  

🔗 [Hasil di R3](https://drive.google.com/open?id=1tWa_JjthGJIpeXR7nZ_GzmsjYHGg4bBP&usp=drive_fs)  
🔗 [Hasil di PC](https://drive.google.com/open?id=1kvRZXQ3XJ8PkFpy6V52T4mWupzRy9-_-&usp=drive_fs)

---

### Ringkasan DHCP Relay
- **R1 (Server):** Menyediakan IP pool untuk jaringan client + route-static  
- **R2 (Relay):** Interface client diset `dhcp select relay` dan diarahkan ke `dhcp relay server-ip`  
- **Client:** Hanya perlu set `dhcp-alloc` seperti biasa  

---
