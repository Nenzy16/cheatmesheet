# 🦈 Ultimate Wireshark & TShark Cheatsheet

Panduan praktis dan ringkasan lengkap penggunaan **Wireshark** (GUI) dan **TShark** (CLI) untuk analisis paket jaringan, *packet capture*, *troubleshooting*, dan investigasi keamanan siber.

---

## 📋 Daftar Isi
1. [Pengenalan Wireshark & TShark](#1-pengenalan-wireshark--tshark)
2. [Sintaks Dasar TShark (CLI Wireshark)](#2-sintaks-dasar-tshark-cli-wireshark)
3. [Capture Filters (BPF Syntax)](#3-capture-filters-bpf-syntax)
4. [Display Filters (Wireshark GUI & CLI Syntax)](#4-display-filters-wireshark-gui--cli-syntax)
5. [Operator Logika & Perbandingan](#5-operator-logika--perbandingan)
6. [Analisis Protokol Populer](#6-analisis-protokol-populer)
7. [Menganalisis Kinerja & Statistik Jaringan](#7-menganalisis-kinerja--statistik-jaringan)
8. [Eksportasi Data & Ekstraksi File](#8-eksportasi-data--ekstraksi-file)
9. [Tabel Referensi Flag TShark Populer](#9-tabel-referensi-flag-tshark-populer)

---

## 1. Pengenalan Wireshark & TShark

* **Wireshark**: Alat penganalisis protokol jaringan (*network protocol analyzer*) berbasis antarmuka grafis (GUI).
* **TShark**: Versi antarmuka baris perintah (*command-line interface*) dari Wireshark yang sangat berguna untuk otomatisasi skrip, pemindaian di server *headless*, dan analisis lalu lintas jaringan dalam skala besar.

---

## 2. Sintaks Dasar TShark (CLI Wireshark)

Format umum penggunaan `tshark`:

```bash
tshark [opsi/flag] [filter]
```

### 🔹 Menampilkan Daftar Interface Jaringan (`-D`)
```bash
tshark -D
```
* **`-D`**: Menampilkan seluruh kartu jaringan (*network interface*) yang tersedia beserta nomor indeksnya.

### 🔹 Menangkap Paket pada Interface Spesifik (`-i`)
```bash
tshark -i eth0
```
* **`-i <interface>`**: Memilih interface jaringan yang akan ditangkap lalu lintasnya (misal: `eth0`, `wlan0`, atau nomor indeks dari `-D`).

### 🔹 Membatasi Jumlah Paket yang Ditangkap (`-c`)
```bash
tshark -i eth0 -c 100
```
* **`-c <jumlah>`**: Otomatis menghentikan proses *capture* setelah menangkap N paket.

---

## 3. Capture Filters (BPF Syntax)

*Capture Filters* digunakan **sebelum** proses menangkap paket dimulai untuk mengurangi beban memori (menggunakan sintaks BPF / Berkeley Packet Filter).

### 🔹 Filter berdasarkan IP Target / Sumber
```bash
tshark -i eth0 -f "host 192.168.1.1"
```
* **`-f "host <IP>"`**: Hanya menangkap paket dari atau menuju IP tertentu.

```bash
tshark -i eth0 -f "src host 192.168.1.50"
tshark -i eth0 -f "dst host 192.168.1.1"
```
* **`src host`**: Hanya menangkap paket dari IP sumber.
* **`dst host`**: Hanya menangkap paket menuju IP tujuan.

### 🔹 Filter berdasarkan Port / Protokol
```bash
tshark -i eth0 -f "port 80"
tshark -i eth0 -f "tcp port 443"
tshark -i eth0 -f "udp port 53"
```
* **`port <nomor>`**: Hanya menangkap lalu lintas pada port spesifik.

### 🔹 Filter berdasarkan Subnet / Network
```bash
tshark -i eth0 -f "net 192.168.1.0/24"
```
* **`net <CIDR>`**: Menangkap lalu lintas dari seluruh subnet.

---

## 4. Display Filters (Wireshark GUI & CLI Syntax)

*Display Filters* digunakan **setelah** paket ditangkap untuk menyaring dan menganalisis paket spesifik. Format ini digunakan di bilah pencarian Wireshark GUI maupun dengan flag `-Y` pada TShark.

### 🔹 Memfilter Berdasarkan Protokol
```text
http
dns
tcp
udp
icmp
tls
```

### 🔹 Memfilter Berdasarkan Alamat IP
```text
ip.addr == 192.168.1.100
ip.src == 192.168.1.50
ip.dst == 10.0.0.1
```
* **`ip.addr`**: Mencocokkan IP sumber atau tujuan.
* **`ip.src`**: Mencocokkan IP sumber saja.
* **`ip.dst`**: Mencocokkan IP tujuan saja.

### 🔹 Memfilter Berdasarkan Port TCP / UDP
```text
tcp.port == 80
tcp.srcport == 443
udp.dstport == 53
```

---

## 5. Operator Logika & Perbandingan

| Operator | Sintaks Alternatif | Contoh Penggunaan | Fungsi |
| :--- | :--- | :--- | :--- |
| `==` | `eq` | `ip.src == 192.168.1.1` | Sama dengan |
| `!=` | `ne` | `ip.src != 192.168.1.1` | Tidak sama dengan |
| `>` | `gt` | `frame.len > 1000` | Lebih besar dari |
| `<` | `lt` | `tcp.port < 1024` | Lebih kecil dari |
| `>=` | `ge` | `frame.len >= 500` | Lebih besar dari atau sama dengan |
| `<=` | `le` | `frame.len <= 500` | Lebih kecil dari atau sama dengan |
| `and` | `&&` | `ip.src == 10.0.0.1 and tcp.port == 80` | Logika DAN |
| `or` | `\|\|` | `http or dns` | Logika ATAU |
| `not` | `!` | `not arp` | Logika BUKAN / Negasi |
| `contains` | - | `http.request.uri contains "admin"` | Mengandung substring tertentu |
| `matches` | `~` | `http.host matches ".*google.*"` | Mencocokkan Regular Expression (Regex) |

---

## 6. Analisis Protokol Populer

### 🔹 Analisis HTTP
```text
http.request.method == "POST"
http.response.code == 200
http.request.uri contains "login"
http.user_agent contains "sqlmap"
```

### 🔹 Analisis DNS
```text
dns.flags.response == 0         # Hanya query DNS (permintaan)
dns.flags.response == 1         # Hanya respon DNS
dns.qry.name contains "malware" # Pencarian nama domain tertentu
```

### 🔹 Analisis TCP & SSL/TLS
```text
tcp.flags.syn == 1 and tcp.flags.ack == 0  # TCP Handshake pertama (SYN)
tcp.analysis.retransmission                # Deteksi paket yang terkirim ulang (network issue)
tls.handshake.type == 1                    # Client Hello (Awal SSL/TLS handshake)
```

---

## 7. Menganalisis Kinerja & Statistik Jaringan

### 🔹 Membuat Hirarki Statistik Protokol (`-z io,phs`)
```bash
tshark -r capture.pcap -z io,phs
```
* **`-z io,phs`**: Menampilkan rangkuman persentase penggunaan protokol (*Protocol Hierarchy Statistics*) dalam file pcap.

### 🔹 Menampilkan Ringkasan Percakapan IP (`-z conv,ip`)
```bash
tshark -r capture.pcap -z conv,ip
```
* **`-z conv,ip`**: Menampilkan daftar statistik percakapan antar-IP (volume data, jumlah paket, dll).

### 🔹 Ekstraksi Field Tertentu ke Format Teks/CSV (`-T fields`)
```bash
tshark -r capture.pcap -T fields -e ip.src -e ip.dst -e tcp.dstport -E header=y -E separator=,
```
* **`-T fields`**: Mengubah format output ke field spesifik.
* **`-e <field>`**: Menentukan nama field yang ingin diekstrak.
* **`-E header=y`**: Menampilkan baris *header* nama kolom.
* **`-E separator=,`**: Menggunakan koma sebagai pemisah kolom (format CSV).

---

## 8. Eksportasi Data & Ekstraksi File

### 🔹 Menyimpan Hasil Capture ke File PCAP (`-w`)
```bash
tshark -i eth0 -w hasil_capture.pcap
```
* **`-w <file.pcap>`**: Menyimpan seluruh lalu lintas jaringan mentah ke dalam berkas format `.pcap` / `.pcapng`.

### 🔹 Membaca File PCAP yang Sudah Ada (`-r`)
```bash
tshark -r hasil_capture.pcap -Y "http.request"
```
* **`-r <file.pcap>`**: Membaca dan menganalisis file pcap dari penyimpanan lokal.
* **`-Y`**: Menerapkan *display filter* pada file pcap yang dibaca.

### 🔹 Ekstraksi File Tertentu dari PCAP (`--export-objects`)
```bash
tshark -r capture.pcap --export-objects "http,./extracted_files"
```
* **`--export-objects`**: Ekstraksi otomatis file yang ditransfer melalui protokol HTTP/SMB/DICOM/IMF dari pcap ke folder lokal.

---

## 9. Tabel Referensi Flag TShark Populer

| Flag Singkat | Flag Panjang | Fungsi / Deskripsi |
| :--- | :--- | :--- |
| `-i` | `--interface` | Menentukan interface jaringan yang digunakan |
| `-D` | `--list-interfaces` | Menampilkan daftar interface jaringan terdeteksi |
| `-f` | - | Menerapkan *Capture Filter* (sintaks BPF) sebelum capture dimulai |
| `-Y` | `--display-filter` | Menerapkan *Display Filter* (sintaks Wireshark) pada data capture |
| `-c` | - | Membatasi jumlah paket yang ditangkap |
| `-w` | - | Menyimpan hasil tangkapan paket ke file `.pcap` |
| `-r` | - | Membaca file berkas tangkapan `.pcap` lokal |
| `-V` | `--verbose` | Menampilkan detail rincian paket secara lengkap (*deep inspection*) |
| `-q` | - | Mode senyap (*quiet mode*), berguna saat membuat statistik dengan `-z` |
| `-z` | - | Menjalankan analisis statistik khusus (percakapan, hirarki protokol, dll) |
| `-T` | - | Menentukan format output teks (`fields`, `json`, `pdml`, `psml`) |
| `-e` | - | Menentukan nama field yang diekstrak saat menggunakan `-T fields` |
| `-n` | - | Mematikan konversi nama DNS (menampilkan IP mentah agar lebih cepat) |
| `-b` | - | Opsi *ring buffer* (memutar file pcap berdasarkan ukuran/waktu) |

---