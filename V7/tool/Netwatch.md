# Pembahasan
Fitur netwatch berfungsi untuk memonitor kondisi host apakah up atau down. Netwatch biasa diimplementasikan untuk memonitoring sebuah host yang memang sangat penting dan harus dimonitoring selama 24 Jam. Netwatch bekerja dengan cara mengirimkan packet ICMP ke host dan jika packet ICMP tersebut mendapatkan respon timeout, maka status host tersebut adalah down. 
<br>
<br>
Pada RouterOS v7.5 memperkenalkan fitur netwatch yang jauh lebih gila, karena di versi sebelumnya hanya bisa mengirimkan packet ICMP, tetapi pada versi 7.5 terdapat tipe tambahan atau yang disebut dengan probe yaitu HTTP-GET, TCP-CONN dan juga ICMP dengan attribute tambahannya. 

## Parameter
| Parameter | Deksripsi                                                                                                 |
|-----------|-----------------------------------------------------------------------------------------------------------|
| Host      | IP Address dengan format : <br>- IPv4<br>- IPv4@vrf<br>- IPv6<br>- IPv6@vrf<br>- IPv6-linklocal%interface |
|           |                                                                                                           |
|           |                  

# Sumber Referensi
- https://www.youtube.com/watch?v=qK0aUo4B5Tc
- https://help.mikrotik.com/docs/display/ROS/Netwatch
- https://citraweb.com/artikel_lihat.php?id=85
