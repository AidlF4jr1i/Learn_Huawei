# Basic connection in Huawei

sebelum melangkah ke bagian koinfgurasi perangkat huawei kita akan terlebih dahulu membuat Lab sederhana menggunakan ENSp dimana kita akan melakukan koneksi antar perangkat komputer yang tersdeia di ENSP, untuk Lab nya bisa dilihat pada gambar berikut :''

dimana yang akan kita alakukan :

- masuk ke ENSp
- drag and drop 2 PC end device yang tersedia
- sambungkan ke-2 PC dengan cable(pilih yang chopper)
- masukkan IP, PC1(192.168.1.2/24), PC2(192.168.1.3/24), untuk melakukannya kamu bisa klik PC yang kamu masukkan lanlau klik 'settings'
- coba klik kanan pada cable lalu 'capture'
-  setelah menu wireshark terbuka coba jalankan ping ke salah satu PC dari PC lainnya disini coba lakukan dari PC2 ke PC1 dan lihat apakah wireshark dapat menangkaptraffic yang lewat(untuk melakukannya ucukup buka 'settings di PC2 lalu pilih 'command' dan jalankan 'ping 192.168.1.2'

output: ''

padaLAb ini kita memeplajari bagaimana Cara untuk meakukan setting IP pad aperangkat end device secara manual dimana kalau dilihat, interface nya sangat mirip dengan seperti yang kita lakukan di Cisco Packet Tracer, selain itu kita bsia melakukan analisis lalu litas data langsung menggunakan wireshark yang sudah kompatibel dengan ENSp,Kepp going now kita akan lanjut ke LAB selanjutnya!