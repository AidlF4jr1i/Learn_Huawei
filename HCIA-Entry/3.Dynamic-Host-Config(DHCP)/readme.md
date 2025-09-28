# DHCP

DHCP merupakan salah satu menu yang dimiliki oleh Router Huawei yang berfungsi sebagai penyedia layanan IP secara otomatis kepada perangkat lain dalam satu jaringan yang sama, konsepnya mirip dengan yang ada pada beberapa vendor seperti Mikrotik,dan Cisco jadi tidak begitu sulit untuk mengkonfigurasinya, sebelum lanjut kita pada section ini kita akan menggunakan Lab Topology berikut:

Topology: "https://drive.google.com/open?id=1h-l-IMjnWDCuHKgzsmdEHta7FyVRbmuH&usp=drive_fs"

next dari LAB tersebut kita akan mulai mengkonfigurasikan DHCP-Server berikut rincian commandnya:

- pada setiap router masuk ke 'system-view' dan jalankan 'dhcp enabled'
- setelah itu, pada R2 yang akan saya jadikan DHCP-Server, kita akan menjalankan:

    -) ip address 192.168.1.1 24 # (memasukkan IP address sebagai gateway)
    -) int <nama interface yang terhubung ke SW>
    -) dhcp select interface(memilih interface tersebut sebagai DHCP-Server)
    -) next kita akan menngkonfig dhcp server, ada banyak pilihan seperti yang saya tuliskan berikut : '
    
    [Huawei-GigabitEthernet0/0/0]dhcp server ?
  dns-list             Configure DNS servers
  domain-name          Configure domain name 
  excluded-ip-address  Mark disable IP addresses 
  import               Imports the following network configuration parameters   
                       from a central server into local ip pool database: domain
                       name, dns server and netbios server.
  lease                Configure the lease of the IP pool
  nbns-list            Configure the windows's netbios name servers 
  netbios-type         Netbios node type
  next-server          The address of the server to use in the next step of the 
                       client's bootstrap process.
  option               Configure the DHCP options
  option121            DHCP option 121 
  option184            DHCP option 184
  recycle              Recycle IP address
  static-bind          Static bind
    ', nah disini kita akan mengkonfigurasikan yang pertama adalah dns-list, lalu lease, dan juga excluded ip addr langsung saja ke konfigurasi:
    -) dhcp server dns-list 8.8.8.8
    -) dhcp server  lease day 1 hour 1 minute 30, # mengatur waktu reset ip menjadi selama 1 hari 1 jam 30 menit
    -) dhcp server excluded-ip-address 192.168.1.2 # mengexlude IP ini agar tidak masuk kedalam andtrian IP otomatis

selanjutnya pada masing-masing router kita tinggal menjalankan:
- int <nama interface yang terhubung ke SW>
- ip add dhcp server-alloc

dan jalankan 'disp ip int br' untuk melihat apakah ip address didapatkan, next di R2(DHCP-Server) kita akan pastikan juga apakah requestnya masuk atau tidak dengan menjalankan 'display dhcp server statistics ' dan output nya seperti ini : '
[Huawei]display dhcp server statistics 
 DHCP Server Statistics: 

 Client Request          : 8       
  Dhcp Discover          : 4       
  Dhcp Request           : 4       
  Dhcp Decline           : 0       
  Dhcp Release           : 0       
  Dhcp Inform            : 0       
 Server Reply            : 8       
  Dhcp Offer             : 4       
  Dhcp Ack               : 4       
  Dhcp Nak               : 0       
 Bad Messages            : 0   
', terakhir pada topologi kan kita ingin agar PC bisa dhcp an juga, caranya gimana? cukup mudah, cukup pergi ke menu settings PC lalu pilihan IP dari 'ststic' ganti ke 'dhcp' berikut tampilannya

Gambar:"https://drive.google.com/open?id=15cQj9jj-vkh9AKft--nuJYi8Rw1g6dN-&usp=drive_fs"
Gambar:"https://drive.google.com/open?id=1R1vo2lbZmgPZSlLCDAO6eoHpVdbkrb_L&usp=drive_fs"

# Ringkasan DHCP-Server

<masukkan ringkasan penjelasan sebelumnya disini>



# LAB 7 DHCP RELAY

DHCP Relay adalah mekanisme pada router atau perangkat Layer-3 yang berfungsi untuk meneruskan pesan DHCP (yang aslinya broadcast) ke DHCP server yang berada di jaringan lain. Hal ini diperlukan karena broadcast tidak bisa melewati router. Misalnya ada 3 router (R1, R2, R3) yang saling terhubung, dan R1 berfungsi sebagai DHCP Server sementara client berada di jaringan R3. Agar client di R3 bisa mendapatkan IP dari R1, maka router yang menghubungkan jaringan client (R3) perlu dikonfigurasi sebagai DHCP Relay, sehingga broadcast DHCP dari client bisa diteruskan ke DHCP server di R1!, langsung saja masuk ke topologinya,

Topologi: 'https://drive.google.com/open?id=1jKS0IVoOCm1pfFFu3-JK_xdTvMVs-tTC&usp=drive_fs'

okeh dari topologi tersebut kita akan terlebih dahulu mengkonfiurasi IP static untuk R1 dan R2 agar saling terhubung, lalu paad R2 kita akan menyiapkan 1 IP pada interface g0/0/1 yang hanya akan aktif bila ada request dari client(maksdunya mejadi gateway dari R3) dimana :

R1:
- system-view
- int g0/0/0
- ip add 192.168.12.1 24

R2:
- system-view
- int g0/0/0
- ip add 192.168.12.2 24
- int g0/0/1
- ip add 172.16.0.1 24

next sebelum lanut pada keseluruhan router jalankan :

- dhcp enabled

lalu di R1:

- ip pool <0-999>, disini saya akan apakai 'ip pool 0'
- network 172.16.0.0 mask 255.255.255.0 # setting IP dhcp untuk Client
- gateway-list <ip R2 yang tehrubung ke client>, disini: 'gateway-list 172.16.0.1', # Ip gateway yang akan menreuskan packet DHCP
- dns-list 8.8.8.8
- lease day 3 hour 10
- excluded-ip-address 172.16.0.254
- quit
- ip route-static 172.16.0.0 255.255.255.0 192.168.12.2 # dimana semua traffic yang berhubungan dengan ip 172.16.0.0(client) akan diserahkan ke R2 via IP 192.168.12.2
- int g0/0/0
- dhcp select global

R2:

- system-view
- int g0/0/1 # interface yang mengarah ke client
- dhcp select relay # memilih interface ini sebagai relay
- dhcp relay server-ip 192.168.12.1 # memasukkan informasi menegani IP server dimana akan dikirimkan request dari Client

okeh setelah selesai dari ini next pada r3 kita hanya perlu masuk ke interface nya dan jalankan 'ip add dhcp-alloc' dan lihat apakah ip sudah diapaatkan atau belum dengan disp ip int br hasilnya dapat dilihat dibawah:

gambar1(R3): "https://drive.google.com/open?id=1tWa_JjthGJIpeXR7nZ_GzmsjYHGg4bBP&usp=drive_fs"
gambar2(PC): "https://drive.google.com/open?id=1kvRZXQ3XJ8PkFpy6V52T4mWupzRy9-_-&usp=drive_fs"

# ringkasan Materi DHCP Relay
<masukkan penjelasan mengenari materi DHCP relay yang sudah ditulis sebelumnya!>