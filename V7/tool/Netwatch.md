# Pembahasan
Fitur netwatch berfungsi untuk memonitor kondisi host apakah up atau down. Netwatch biasa diimplementasikan untuk memonitoring sebuah host yang memang sangat penting dan harus dimonitoring selama 24 Jam. Netwatch bekerja dengan cara mengirimkan packet ICMP ke host dan jika packet ICMP tersebut mendapatkan respon timeout, maka status host tersebut adalah down. 
<br>
<br>
Pada RouterOS v7.5 memperkenalkan fitur netwatch yang jauh lebih gila, karena di versi sebelumnya hanya bisa mengirimkan packet ICMP, tetapi pada versi 7.5 terdapat tipe tambahan atau yang disebut dengan probe yaitu HTTP-GET, TCP-CONN dan juga ICMP dengan attribute tambahannya. 

## Parameter
|    Parameter    | Deksripsi                                                                                                                                                                                                                                                                             |
|:---------------:|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     **Host**    | IP Address dengan format : <br>- IPv4<br>- IPv4@vrf<br>- IPv6<br>- IPv6@vrf<br>- IPv6-linklocal%interface                                                                                                                                                                             |
|     **type**    | Tipe Probe :<br>- ICMP, mengirimkan dan menerima hasil statistik seperti pada ping di terminal/cmd<br>- TCP-CONN, terkoneksi ke host dengan port tertentu<br>- HTTP-GET, menerima respon dari host berbasis HTTP<br>- Simple, seperti pada versi sebelumnya yaitu tidak ada statistik |
|   **interval**  | Waktu ketika berjalannya probe. (Default 10 detik)                                                                                                                                                                                                                                    |
|   **timeout**   | Waktu maksimal untuk menunggu ketika terjadi timeout. (Default 3 detik)                                                                                                                                                                                                               |
| **start-delay** | Waktu menunggu berjalanya netwatch pada saat ditambahkan, enable, atau reboot. (Default 3 detik)                                                                                                                                                                                      |
|  **up-script**  | Script yang akan dijalankan ketika status Up                                                                                                                                                                                                                                          |
| **down-script** | Script yang akan dijalankan ketika status Down                                                                                                                                                                                                                                        |
| **test-script** | Script yang akan dijalankan dalam situasi sesudah Up atau Down                                                                                                                                                                                                                        |

### Penjelasan Opsi Probe
#### ICMP 
|       Parameter      | Deksripsi                                                       |
|:--------------------:|-----------------------------------------------------------------|
| **packet-interval**  | Rentang waktu packet ICMP yang dikirimkan                       |
| **packet-count**     | Jumlah packet yang dikirimkan dalam satu pengujian (default 10) |
| **packet-size**      | Ukuran total packet IP ICMP                                     |
| **thr-rtt-max**      | Batasan maksimal latency                                        |
| **thr-rtt-avg**      | Batasan rata-rata latency                                       |
| **thr-rtt-stdev**    | Batasan standar deviasi latency                                 |
| **thr-rtt-jitter**   | Batasan jitter                                                  |
| **thr-loss-percent** | Batasan packet loss dalam percent                               |
| **thr-loss-count**   | Batasan jumlah packet loss                                      |

### TCP-CONN
|       Parameter       | Deksripsi                   |
|:---------------------:|-----------------------------|
| **port**              | Port TCP host               |
| **thr-tcp-conn-time** | Batas waktu koneksi TCP     |

### HTTP-GET
|     Parameter     | Deksripsi                                                 |
|:-----------------:|-----------------------------------------------------------|
| **thr-http-time** | Batas waktu response (default 10 detik)                   |
| **http-code-min** | Respon kode HTTP (default 100)                            |
| **http-code-max** | Respon kode dalam rentang [http-code-min, http-code-max]  |

### Contoh Implementasi
#### Monitoring Web BNET
```rsc
/tool netwatch
add comment="MONITOR WEB BNET" disabled=no down-script=":log info \"Web BNET Down\"" host=103.73.73.177 http-codes=100-499 interval=10s port=443 test-script=":log info \"Pengecekan Web Selesai\"" type=http-get up-script=":log info \"Web BNET Up\""
```
Netwatch akan mengecekan apakah Web BNET sedang berjalan normal atau tidak dengan range response code 100-499. Jika response lebih dari 499, maka disimpulkan bahwa web BNET sedang down.

#### Monitoring Rhome
```rsc
/tool netwatch
add comment="MONITORING RHOME" disabled=no down-script=":log info \"Rhome Berjalan Tidak Normal\"" host=103.73.72.222 http-codes="" interval=10s packet-count=20 test-script=":log info \"Pengecekan Rhome Selesai\"" thr-loss-percent=1% thr-max=10ms type=icmp up-script=":log info \"Rhome Berjalan Normal\""
```
Netwatch akan mengecekan rhome dengan aturan packet yang dikirim dan diterima harus 20 dan batas maksimal packet los hanya 1% dan batas latency tertinggi ialah 10ms. Jika sudah melanggar dari aturan tersebut, makan dikatakan Rhome sedang tidak normal atau down.

#### Monitoring Rhome Primitf
```rsc
/tool netwatch
add comment="PRIMITIF MONITORING RHOME" disabled=no down-script=":log info \"Rhome Down\"" host=103.73.72.222 http-codes="" interval=10s test-script="" type=simple up-script=":log info \"Rhome Up\""
```
Sama seperti netwatch diatas, tetapi ini versi primitif atau sebelum V7.5, yaitu hanya bisa melakukan pengiriman packet ICMP tetapi tidak ada ukuran statistikanya.

#### Cek Port SSH Rhome
```
/tool netwatch
add comment="CEK PORT SSH RHOME" disabled=no down-script=":log info \"Port SSH Rhome Down\"" host=103.73.72.222 http-codes="" port=22322 test-script=":log info \"Pencekan Port SSH Rhome Selesai\"" type=tcp-conn up-script=":log info \"Port SSH Rhome Up\""
```
Netwatch akan mengecekan apakah port SSH Rhome berada di port 22322 atau bukan. Jika bukan di 22322 dapat dipastikan port tersebut sudah diganti atau dikatakan down.

### Rekomendasi Implementasi
1. Dapat diimplementasikan pada host yang memiliki 2 Link (Main & Backup). Jika pada link main statusnya sudah tidak normal (bukan hanya down), maka langsung dipindahkan ke link backup dengan cara membuat perintah semacam failover. Dengan cara ini akan lebih efektif dibandingkan dengan netwatch versi sebelumnya yang cukup lama dalam melakukan pergantian Link.
2. Dapat diimplementasikan untuk pengecekan Port SSH jika ada yang melakukan perubahan dengan menggunakan type **tcp-conn**.

# Sumber Referensi
- https://www.youtube.com/watch?v=qK0aUo4B5Tc
- https://help.mikrotik.com/docs/display/ROS/Netwatch
- https://citraweb.com/artikel_lihat.php?id=85
