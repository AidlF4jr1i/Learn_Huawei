# 9. Dynamic Routing

## Mengenal Dynamic Routing di Perangkat Huawei

Setelah sebelumnya kita belajar mengenai cara mengkonfigurasi **Static Routing**, sekarang kita akan masuk ke pembahasan **Dynamic Routing**.  
Sesuai namanya, *Dynamic* berarti **Dinamis** atau **Otomatis**. Jadi, penggunaannya jelas: untuk mengkonfigurasi routing secara otomatis antar perangkat yang terhubung ke router.

Keunggulannya, metode ini lebih cepat dan efisien untuk jaringan skala besar — baik di perusahaan maupun di kantor.  
Pada sesi ini, kita akan mempelajari bagaimana cara mengkonfigurasi Dynamic Routing menggunakan dua protokol populer: **RIP** dan **OSPF**.

---

## 1. RIP (Routing Information Protocol)

### Apa itu RIP?

**RIP** adalah protokol dynamic routing yang paling umum sekaligus salah satu yang tertua di dunia.  
Cara kerjanya adalah dengan *mengiklankan (advertise)* semua jaringan yang terhubung langsung dengannya ke router tetangga, sambil menghitung jarak (*metric*) untuk mencapai jaringan tersebut.  

Metric pada RIP dihitung berdasarkan jumlah lompatan (**hop count**).

> ⚠️ Kelemahan utama RIP adalah tidak memiliki gambaran utuh terhadap topologi jaringan.
> RIP hanya tahu "jarak terdekat" berdasarkan jumlah router yang dilewati, tanpa mempertimbangkan bandwidth atau kepadatan lalu lintas data.

#### Contoh Kasus

Bayangkan **Router A** ingin mengirim data ke **Router E**. Ada dua jalur:

1. Jalur 1: Melalui Router B → C → D dengan kecepatan 1 Gbps (**3 hop**).  
2. Jalur 2: Melalui Router F dengan kecepatan 100 Mbps (**1 hop**).

RIP akan memilih Jalur 2 karena hanya memiliki 1 lompatan, meskipun 10× lebih lambat.  
Jika data besar, ini bisa menyebabkan kemacetan (*bottleneck*). Karena itu, RIP lebih cocok untuk jaringan kecil atau sebagai sarana pembelajaran.

---

### Konfigurasi RIP

Topologi yang digunakan dapat diakses di sini:  
🔗 [Topology Link](https://drive.google.com/open?id=1NuX0ZfL4cvruvqCbuIaGN1Jt4UdfBOrM&usp=drive_fs)

#### Langkah 1: Konfigurasi Dasar (IP Address & DHCP)

##### Router 1 (R1)

```bash
system-view
int g0/0/0
 ip address 192.168.12.1 24
int g0/0/1
 ip address 10.10.10.1 24
quit

dhcp enable
int g0/0/1
 dhcp select interface
 dhcp server lease day 1
 dhcp server dns-list 1.1.1.1
 dhcp server excluded-ip-address 10.10.10.2
```

##### Router 2 (R2)

```bash
system-view
int g0/0/0
 ip address 192.168.12.2 24
int g0/0/1
 ip address 192.168.23.1 24
int g2/0/0
 ip address 20.20.20.1 24
quit

dhcp enable
int g2/0/0
 dhcp select interface
 dhcp server lease day 1
 dhcp server dns-list 1.1.1.1
 dhcp server excluded-ip-address 20.20.20.2
```

##### Router 3 (R3)

```bash
system-view
int g0/0/0
 ip address 192.168.23.2 24
int g0/0/1
 ip address 172.16.0.1 24
int g2/0/0
 ip address 172.16.1.1 24
int g2/0/1
 ip address 172.16.2.1 24
quit

dhcp enable
int g0/0/1
 dhcp select interface
 dhcp server excluded-ip-address 172.16.0.2
int g2/0/0
 dhcp select interface
 dhcp server excluded-ip-address 172.16.1.2
int g2/0/1
 dhcp select interface
 dhcp server excluded-ip-address 172.16.2.2
```

#### Langkah 2: Mengaktifkan RIP

##### Router 1 (R1)

```bash
system-view
rip 1
 version 2
 network 192.168.12.0
 network 10.0.0.0
display ip routing-table
```

##### Router 2 (R2)

```bash
system-view
rip 1
 version 2
 network 192.168.12.0
 network 192.168.23.0
 network 20.0.0.0
display ip routing-table
```

##### Router 3 (R3)

```bash
system-view
rip 1
 version 2
 network 192.168.23.0
 network 172.16.0.0
display ip routing-table
```

---

### Fitur Lanjutan RIP

#### A. Optimalisasi dengan Route Summarization

Pada R3, terdapat tiga jaringan serupa: `172.16.0.0/24`, `172.16.1.0/24`, dan `172.16.2.0/24`.  
Kita bisa meringkasnya menjadi satu alamat jaringan **172.16.0.0/22**.

```bash
system-view
int g0/0/0
 rip summary-address 172.16.0.0 255.255.252.0
```

#### B. Keamanan dengan Autentikasi

##### 1. Simple Authentication

```bash
int g0/0/0
 rip authentication-mode simple plain testing
```

##### 2. MD5 Authentication

```bash
int g0/0/0
 rip authentication-mode md5 cipher testing
```

> **plain:** password terlihat jelas di konfigurasi.  
> **cipher:** password dienkripsi di file konfigurasi.

---

## 2. OSPF (Open Shortest Path First)

### Apa itu OSPF?

**OSPF** adalah protokol dynamic routing *Link-State*, yang membangun peta topologi jaringan secara keseluruhan.  
Berbeda dengan RIP, OSPF lebih cerdas dan efisien.

#### Keunggulan OSPF dibanding RIP

1. **Metric Cerdas** — menggunakan *cost* berdasarkan bandwidth.  
2. **Konvergensi Cepat** — cepat mendeteksi perubahan jaringan.  
3. **Mendukung Jaringan Besar** — menggunakan konsep *area*.  
4. **Lebih Efisien** — hanya mengirim update saat ada perubahan topologi.

---

### Konfigurasi OSPF

Gunakan **wildcard mask**, kebalikan dari subnet mask.  
Contoh: `/24` → wildcard `0.0.0.255`.

#### Router 1 (R1)

```bash
system-view
ospf 1
 area 0
  network 192.168.12.0 0.0.0.255
  network 10.10.10.0 0.0.0.255
display ip routing-table
```

#### Router 2 (R2)

```bash
system-view
ospf 1
 area 0
  network 192.168.12.0 0.0.0.255
  network 192.168.23.0 0.0.0.255
  network 20.20.20.0 0.0.0.255
display ip routing-table
```

#### Router 3 (R3)

```bash
system-view
ospf 1
 area 0
  network 192.168.23.0 0.0.0.255
  network 172.16.0.0 0.0.0.255
  network 172.16.1.0 0.0.0.255
  network 172.16.2.0 0.0.0.255
display ip routing-table
```

---

### Ringkasan OSPF

- **ospf 1** → mengaktifkan proses OSPF ID 1.  
- **area 0** → backbone area default.  
- **network ...** → mengaktifkan OSPF pada interface dengan IP dalam rentang tersebut.

---

## Ringkasan Keseluruhan Materi

1. **Konfigurasi Dasar** — IP dan DHCP.  
2. **RIP** — sederhana tapi terbatas.  
3. **OSPF** — cerdas dan efisien untuk jaringan besar.

| Protokol | Kelebihan | Kekurangan | Kapan Digunakan |
|-----------|------------|-------------|-----------------|
| **RIP** | Mudah dikonfigurasi | Tidak efisien, terbatas hop 15 | Jaringan kecil, pembelajaran |
| **OSPF** | Cepat, scalable, efisien | Konfigurasi kompleks | Jaringan besar dan perusahaan |

---

### Struktur Materi

- Mengenal Dynamic Routing di Perangkat Huawei
- 1. RIP (Routing Information Protocol)
  - Apa itu RIP?
  - Konfigurasi RIP
  - Fitur Lanjutan RIP
    - Route Summarization
    - Autentikasi
- 2. OSPF (Open Shortest Path First)
  - Apa itu OSPF?
  - Konfigurasi OSPF
- Ringkasan Keseluruhan Materi
