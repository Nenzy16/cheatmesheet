# 🎯 Ultimate Nmap Cheatsheet

Panduan praktis dan ringkasan lengkap penggunaan **Nmap** (*Network Mapper*) untuk pemindaian jaringan, audit keamanan, *port scanning*, dan *penetration testing*.

---

## 📋 Daftar Isi
1. [Sintaks Dasar](#1-sintaks-dasar)
2. [Target Specification (Penentuan Target)](#2-target-specification-penentuan-target)
3. [Teknik Pemindaian Port (Scan Techniques)](#3-teknik-pemindaian-port-scan-techniques)
4. [Penentuan Port (Port Specification)](#4-penentuan-port-port-specification)
5. [Deteksi Layanan & Versi (Service & Version Detection)](#5-deteksi-layanan--versi-service--version-detection)
6. [Deteksi Sistem Operasi (OS Detection)](#6-deteksi-sistem-operasi-os-detection)
7. [Nmap Script Engine (NSE)](#7-nmap-script-engine-nse)
8. [Pengaturan Waktu & Performa (Timing & Performance)](#8-pengaturan-waktu--performa-timing--performance)
9. [Evasion & Spoofing (Menembus Firewall/IDS)](#9-evasion--spoofing-menembus-firewallids)
10. [Opsi Output & Penyimpanan](#10-opsi-output--penyimpanan)
11. [Tabel Referensi Flag Populer](#11-tabel-referensi-flag-populer)

---

## 1. Sintaks Dasar

Format umum penggunaan Nmap:

```bash
nmap [Tipe Scan] [Opsi/Flag] [Target]
```

### Contoh Dasar (Pemindaian IP Tunggal):
```bash
nmap 192.168.1.1
```
> **Penjelasan:** Secara bawaan (*default*), Nmap akan memindai 1.000 port TCP yang paling umum digunakan pada alamat IP target.

---

## 2. Target Specification (Penentuan Target)

Nmap mendukung berbagai format penentuan target, mulai dari IP tunggal, domain, CIDR, hingga daftar dari file.

### 🔹 Memindai Nama Domain / Hostname
```bash
nmap scanme.nmap.org
```
* **Penjelasan:** Menerjemahkan nama domain ke IP terlebih dahulu sebelum melakukan pemindaian.

### 🔹 Memindai Beberapa IP Terpisah
```bash
nmap 192.168.1.1 192.168.1.15 192.168.1.50
```

### 🔹 Memindai Rentang Alamat IP (Range)
```bash
nmap 192.168.1.1-50
```
* **Penjelasan:** Memindai IP dari `192.168.1.1` sampai `192.168.1.50`.

### 🔹 Memindai Seluruh Subnet (Notasi CIDR)
```bash
nmap 192.168.1.0/24
```
* **Penjelasan:** Memindai seluruh 256 alamat IP dalam subnet `/24` (dari `.0` hingga `.255`).

### 🔹 Memindai Target dari File Teks (`-iL`)
```bash
nmap -iL daftar_target.txt
```
* **`-iL <file>`**: Membaca daftar target dari file teks (satu IP/domain per baris).

### 🔹 Mengecualikan Target Tertentu (`--exclude`)
```bash
nmap 192.168.1.0/24 --exclude 192.168.1.100,192.168.1.101
```
* **`--exclude`**: Memindai seluruh subnet kecuali IP yang ditentukan.

---

## 3. Teknik Pemindaian Port (Scan Techniques)

Nmap menyediakan berbagai teknik pemindaian tergantung pada hak akses (*privilege*) dan tipe protokol.

### 🔹 SYN Stealth Scan (`-sS`) — *Default (Root)*
```bash
sudo nmap -sS 192.168.1.1
```
* **`-sS`**: Mengirim paket SYN dan tidak menyelesaikan *3-way handshake* (Half-open scan). Sangat cepat dan tidak mudah tercatat oleh log aplikasi sederhana.

### 🔹 TCP Connect Scan (`-sT`) — *Default (Non-Root)*
```bash
nmap -sT 192.168.1.1
```
* **`-sT`**: Menyelesaikan seluruh proses *3-way handshake* TCP. Digunakan secara otomatis jika pengguna tidak memiliki hak akses *root/sudo*.

### 🔹 UDP Scan (`-sU`)
```bash
sudo nmap -sU 192.168.1.1
```
* **`-sU`**: Memindai port berbasis protokol UDP (seperti DNS, DHCP, SNMP). Biasanya membutuhkan waktu lebih lama dibanding TCP scan.

### 🔹 FIN, NULL, & Xmas Scans (`-sF`, `-sN`, `-sX`)
```bash
sudo nmap -sN 192.168.1.1
sudo nmap -sF 192.168.1.1
sudo nmap -sX 192.168.1.1
```
* **`-sN`**: NULL scan (tidak ada flag TCP yang diaktifkan).
* **`-sF`**: FIN scan (hanya flag FIN yang aktif).
* **`-sX`**: Xmas scan (flag FIN, PSH, dan URG aktif bersamaan).
* **Penjelasan:** Digunakan untuk memanipulasi aturan *firewall* atau *stateless filter*.

### 🔹 Ping Scan / Host Discovery (`-sn`)
```bash
nmap -sn 192.168.1.0/24
```
* **`-sn`**: Memeriksa host mana saja yang aktif (*live host*) tanpa melakukan *port scanning* (dikenal juga sebagai Ping Sweep).

---

## 4. Penentuan Port (Port Specification)

Secara *default*, Nmap memindai 1.000 port terpopuler. Anda dapat menyesuaikan port yang ingin dipindai.

### 🔹 Memindai Port Spesifik (`-p`)
```bash
nmap -p 80,443,8080 192.168.1.1
```
* **`-p <port>`**: Hanya memindai port yang disebutkan.

### 🔹 Memindai Rentang Port (`-p`)
```bash
nmap -p 1-1000 192.168.1.1
```

### 🔹 Memindai Seluruh Port TCP (`-p-`)
```bash
nmap -p- 192.168.1.1
```
* **`-p-`**: Memindai seluruh 65.535 port TCP.

### 🔹 Memindai Port Berdasarkan Protokol
```bash
sudo nmap -p U:53,111,T:80,443 192.168.1.1
```
* **`U:` / `T:`**: Memindai port UDP dan TCP secara bersamaan dalam satu perintah.

### 🔹 Fast Scan (`-F`)
```bash
nmap -F 192.168.1.1
```
* **`-F`**: Hanya memindai 100 port terpopuler (lebih cepat dari pemindaian standar).

---

## 5. Deteksi Layanan & Versi (Service & Version Detection)

### 🔹 Deteksi Versi Layanan (`-sV`)
```bash
nmap -sV 192.168.1.1
```
* **`-sV`**: Memeriksa port terbuka untuk menentukan layanan (*service*) dan versi aplikasi yang sedang berjalan (contoh: Apache 2.4.41, OpenSSH 8.2p1).

### 🔹 Mengatur Intensitas Deteksi Versi (`--version-intensity`)
```bash
nmap -sV --version-intensity 5 192.168.1.1
```
* **`--version-intensity <0-9>`**: Mengatur tingkat agresivitas pengujian versi (0 = ringan/cepat, 9 = paling akurat/lambat).

---

## 6. Deteksi Sistem Operasi (OS Detection)

### 🔹 Deteksi Sistem Operasi Target (`-O`)
```bash
sudo nmap -O 192.168.1.1
```
* **`-O`**: Menggunakan teknik *TCP/IP stack fingerprinting* untuk menebak jenis dan versi Sistem Operasi target.

### 🔹 Mode Agresif / Komprehensif (`-A`)
```bash
sudo nmap -A 192.168.1.1
```
* **`-A`**: Mengaktifkan gabungan fitur: Deteksi OS (`-O`), Deteksi Versi (`-sV`), Script Scanning (`-sC`), dan Traceroute (`--traceroute`).

---

## 7. Nmap Script Engine (NSE)

NSE memungkinkan Nmap menjalankan skrip Lua untuk otomatisasi pengujian kerentanan, deteksi malware, dan eksploitasi dasar.

### 🔹 Memindai dengan Skrip Standar (`-sC`)
```bash
nmap -sC 192.168.1.1
```
* **`-sC`**: Jalankan sekumpulan skrip kategori *default* yang aman. (Sama dengan `--script=default`).

### 🔹 Jalankan Skrip Spesifik (`--script`)
```bash
nmap --script http-title 192.168.1.1
```
* **`--script <nama_skrip>`**: Menjalankan satu skrip spesifik (contoh: megambil judul halaman HTTP).

### 🔹 Pemindaian Kerentanan (Vulnerability Scan)
```bash
nmap --script vuln 192.168.1.1
```
* **`--script vuln`**: Menjalankan kategori skrip untuk memeriksa berbagai kerentanan keamanan populer (*CVEs*).

### 🔹 Mengirim Argumen ke Skrip (`--script-args`)
```bash
nmap --script http-brute --script-args userdb=users.txt,passdb=pass.txt 192.168.1.1
```
* **`--script-args`**: Mengirim parameter kustom ke skrip NSE yang sedang dijalankan.

---

## 8. Pengaturan Waktu & Performa (Timing & Performance)

Nmap menyediakan template waktu dari `T0` (paling lambat/tersembunyi) hingga `T5` (paling cepat/agresif).

```bash
nmap -T4 192.168.1.1
```

* **`-T0` (Paranoid):** Sangat lambat, digunakan untuk menghindari IDS (*Intrusion Detection System*).
* **`-T1` (Sneaky):** Cukup lambat untuk meminimalkan deteksi log.
* **`-T2` (Polite):** Mengurangi kecepatan pemindaian untuk menghemat bandwidth target.
* **`-T3` (Normal):** Pengaturan *default* Nmap.
* **`-T4` (Aggressive):** Diutamakan untuk jaringan lokal/stabil dan cepat.
* **`-T5` (Insane):** Sangat cepat, namun dapat kehilangan akurasi pada jaringan lambat.

---

## 9. Evasion & Spoofing (Menembus Firewall/IDS)

### 🔹 Fragmentasi Paket (`-f`)
```bash
sudo nmap -f 192.168.1.1
```
* **`-f`**: Memecah paket header TCP menjadi beberapa potongan kecil untuk mengelabui filter *firewall*.

### 🔹 Mengubah Ukuran MTU (`--mtu`)
```bash
sudo nmap --mtu 24 192.168.1.1
```
* **`--mtu <ukuran>`**: Menentukan ukuran Maximum Transmission Unit kustom (harus kelipatan 8).

### 🔹 Menggunakan Decoy IP (`-D`)
```bash
sudo nmap -D RND:10 192.168.1.1
```
* **`-D`**: Mengirimkan paket dari IP acak (palsu) bersamaan dengan IP asli Anda untuk membingungkan analis log.

### 🔹 Pemalsuan Alamat MAC (`--spoof-mac`)
```bash
sudo nmap --spoof-mac Apple 192.168.1.1
```
* **`--spoof-mac <vendor/MAC>`**: Memalsukan alamat MAC perangkat Anda (misal: berpura-pura menjadi perangkat Apple atau vendor tertentu).

### 🔹 Kustomisasi Port Sumber (`--source-port`)
```bash
nmap --source-port 53 192.168.1.1
```
* **`--source-port <port>`** (atau `-g`): Memaksa Nmap menggunakan port sumber tertentu (misal port 53 DNS yang sering kali diizinkan oleh firewall).

---

## 10. Opsi Output & Penyimpanan

### 🔹 Output Format Teks Standar (`-oN`)
```bash
nmap -oN hasil_scan.txt 192.168.1.1
```
* **`-oN <file>`**: Menyimpan hasil pemindaian ke file teks biasa.

### 🔹 Output Format XML (`-oX`)
```bash
nmap -oX hasil_scan.xml 192.168.1.1
```
* **`-oX <file>`**: Menyimpan output dalam format XML (cocok diimpor ke Metasploit atau parser otomatis).

### 🔹 Output Format Grepable (`-oG`)
```bash
nmap -oG hasil_scan.gnmap 192.168.1.1
```
* **`-oG <file>`**: Format sederhana satu baris per host yang mudah di-filter menggunakan perintah `grep` atau `awk`.

### 🔹 Menyimpan Semua Format Sekaligus (`-oA`)
```bash
nmap -oA hasil_scan 192.168.1.1
```
* **`-oA <nama_dasar>`**: Menyimpan hasil secara bersamaan ke dalam format `.nmap`, `.xml`, dan `.gnmap`.

---

## 11. Tabel Referensi Flag Populer

| Flag Singkat | Flag Panjang | Fungsi / Deskripsi |
| :--- | :--- | :--- |
| `-sS` | `--syn` | Pemindaian TCP SYN (Half-open, kencang & stealthy) |
| `-sT` | `--connect` | Pemindaian TCP Connect penuh (*default non-root*) |
| `-sU` | `--udp` | Pemindaian port berbasis protokol UDP |
| `-sn` | `--ping` | Ping sweep / Host discovery tanpa scan port |
| `-p` | `-p` | Menentukan port atau rentang port tertentu |
| `-F` | `--fast` | Pemindaian cepat (100 port terpopuler) |
| `-sV` | `--service-version` | Menentukan versi layanan/aplikasi pada port terbuka |
| `-O` | `--osscan-guess` | Mendeteksi Sistem Operasi target |
| `-A` | `--all` | Mode agresif (Gabungan OS, Versi, NSE, & Traceroute) |
| `-sC` | `--script` | Jalankan set skrip NSE standar/default |
| `-T<0-5>`| `--timing` | Mengatur kecepatan/template waktu pemindaian |
| `-iL` | `--input-list` | Membaca daftar target IP/domain dari file teks |
| `-oN` | `--output-normal` | Menyimpan hasil scan ke file teks standar |
| `-oX` | `--output-xml` | Menyimpan hasil scan ke file format XML |
| `-oA` | `--output-all` | Menyimpan hasil scan ke semua format file sekaligus |
| `-Pn` | `--no-ping` | Menganggap semua host aktif (abaikan cek ping) |
| `-v` | `--verbose` | Menampilkan log alur pemindaian secara *real-time* |

---