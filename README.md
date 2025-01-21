# Implementasi Intrusion Detection System (IDS) Menggunakan Snort

## Pendahuluan
Dalam era digital saat ini, keamanan jaringan menjadi aspek yang sangat penting untuk diperhatikan. Ancaman siber seperti peretasan, malware, dan akses tidak sah terus berkembang, sehingga perlindungan terhadap jaringan menjadi prioritas utama bagi individu maupun organisasi.

Salah satu solusi yang banyak digunakan untuk mendeteksi ancaman-ancaman tersebut adalah **Intrusion Detection System (IDS)**. IDS berfungsi sebagai alat untuk mendeteksi ancaman atau aktivitas mencurigakan dalam jaringan, seperti upaya peretasan, malware, atau akses tidak sah. 

Salah satu IDS yang populer digunakan adalah **Snort**, yang memiliki kemampuan untuk menganalisis lalu lintas jaringan secara real-time, mendeteksi berbagai pola serangan, dan memberikan peringatan kepada administrator jaringan.

## Instalasi Snort
Berikut adalah langkah-langkah untuk menginstal dan mengonfigurasi Snort di sistem berbasis Linux.

### 1. Update Sistem
```bash
sudo apt-get update
```

### 2. Instalasi Snort
```bash
sudo apt-get install snort
```

## Konfigurasi Snort

### 3. Konfigurasi IP Home Network
Buka file konfigurasi Snort untuk mendefinisikan alamat jaringan lokal.
```bash
sudo nano /etc/snort/snort.conf
```
Cari bagian **var HOME_NET** dan ubah menjadi:
```bash
var HOME_NET 10.10.10.0/24
```

### 4. Menentukan Rules
Rules digunakan untuk mengatur tindakan yang dilakukan Snort saat mendeteksi ancaman.

#### Masuk ke direktori rules Snort:
```bash
cd /etc/snort/rules
sudo nano local.rules
```

#### Tambahkan rule untuk mendeteksi serangan ICMP Flood:
```bash
alert icmp any any -> $HOME_NET any (msg:"Serangan ICMP Flood Terdeteksi"; sid:100001; rev:1; classtype:icmp-event; detection_filter:track by_dst, count 500, seconds 3;)
```

#### Tambahkan rule untuk mendeteksi serangan DoS SYN Flood:
```bash
alert tcp any any -> $HOME_NET 80 (flags: S; msg:"Serangan DoS Terdeteksi : SYN flood"; flow:stateless; sid:3; detection_filter:track by_dst, count 20, seconds 10;)
```

## Verifikasi Rules
Setelah menambahkan rules, pastikan konfigurasi dapat terbaca dengan perintah berikut:
```bash
sudo snort -T -c /etc/snort/snort.conf -i eth0
```
Jika tidak ada error yang ditampilkan, maka konfigurasi telah berhasil.

## Menjalankan Snort
Untuk mulai menjalankan Snort dan memantau jaringan secara real-time:
```bash
sudo snort -A console -c /etc/snort/snort.conf -i eth0
```

## Pengujian IDS

### 1. Simulasi Serangan ICMP Flood
Gunakan perintah berikut untuk menguji apakah aturan ICMP Flood bekerja:
```bash
hping3 -1 --flood -a 192.168.1.1 192.168.1.2
```
Jika konfigurasi berhasil, maka akan muncul pesan:
```bash
Serangan ICMP Flood Terdeteksi
```

### 2. Simulasi Serangan DoS SYN Flood
Gunakan perintah berikut untuk menguji apakah aturan SYN Flood bekerja:
```bash
hping3 -c 1000 -d 120 -S -w 64 -p 80 --flood --rand-source 192.168.1.2
```
Jika konfigurasi berhasil, maka akan muncul pesan:
```bash
Serangan DoS Terdeteksi : SYN flood
```

## Kesimpulan
Dengan mengikuti langkah-langkah di atas, kita telah berhasil mengimplementasikan IDS menggunakan Snort untuk mendeteksi ancaman seperti **ICMP Flood** dan **SYN Flood**. Pada artikel selanjutnya, kita akan membahas tentang bagaimana menggunakan **Intrusion Prevention System (IPS)** untuk mencegah serangan lebih lanjut.

## Referensi
- [Snort Official Documentation](https://www.snort.org/)
- [Linux Command Reference](https://linux.die.net/)

## Lisensi
Proyek ini dilisensikan di bawah lisensi MIT.