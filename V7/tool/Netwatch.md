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

# Sumber Referensi
- https://www.youtube.com/watch?v=qK0aUo4B5Tc
- https://help.mikrotik.com/docs/display/ROS/Netwatch
- https://citraweb.com/artikel_lihat.php?id=85
