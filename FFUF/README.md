# 🎯 Ultimate FFUF Cheatsheet

Panduan praktis dan ringkasan lengkap penggunaan **FFUF** (*Fuzz Faster U Fool*) untuk pemindaian direktori, *subdomain enumeration*, *parameter fuzzing*, dan pengujian penetrasi aplikasi web.

---

## 📋 Daftar Isi
1. [Sintaks Dasar & Konsep Keyword](#1-sintaks-dasar--konsep-keyword)
2. [Fuzzing Direktori & File (Directory Brute Forcing)](#2-fuzzing-direktori--file-directory-brute-forcing)
3. [Fuzzing Subdomain & VHOST](#3-fuzzing-subdomain--vhost)
4. [Fuzzing Parameter (GET & POST)](#4-fuzzing-parameter-get--post)
5. [Filtering & Matching (Menyaring Hasil)](#5-filtering--matching-menyaring-hasil)
6. [Opsi HTTP Header, Cookie, & Autentikasi](#6-opsi-http-header-cookie--autentikasi)
7. [Pengaturan Performa & Proxy](#7-pengaturan-performa--proxy)
8. [Opsi Output & Penyimpanan](#8-opsi-output--penyimpanan)
9. [Tabel Referensi Flag Populer](#9-tabel-referensi-flag-populer)

---

## 1. Sintaks Dasar & Konsep Keyword

Format umum penggunaan FFUF:

```bash
ffuf -u <URL> -w <WORDLIST> [opsi/flag]
```

> ⚠️ **PENTING:** FFUF membutuhkan penanda posisi berupa kata kunci **`FUZZ`** pada lokasi yang ingin Anda uji (*fuzz*). Secara *default*, FFUF akan mengganti string `FUZZ` dengan entri dari wordlist yang Anda tentukan.

### Contoh Dasar:
```bash
ffuf -u https://example.com/FUZZ -w /path/to/wordlist.txt
```

---

## 2. Fuzzing Direktori & File (Directory Brute Forcing)

### 🔹 Pemindaian Direktori Standar
```bash
ffuf -u https://example.com/FUZZ -w /usr/share/wordlists/dirb/common.txt
```
* **`-u`**: Menentukan URL target.
* **`-w`**: Menentukan lokasi berkas *wordlist*.

### 🔹 Pemindaian File dengan Ekstensi Khusus (`-e`)
```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt -e .php,.html,.txt
```
* **`-e`**: Menambahkan daftar ekstensi file pada setiap entri di wordlist (misal: `admin` -> `admin.php`, `admin.html`).

### 🔹 Pemindaian Direktori Bertingkat (Recursion) (`-recursion`)
```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt -recursion -recursion-depth 2
```
* **`-recursion`**: Menginstruksikan FFUF untuk otomatis memindai ulang direktori baru yang ditemukan (*recursive scan*).
* **`-recursion-depth`**: Membatasi kedalaman level rekursi.

---

## 3. Fuzzing Subdomain & VHOST

### 🔹 Subdomain Enumeration (DNS-based)
```bash
ffuf -u https://FUZZ.example.com/ -w subdomains.txt
```
* **Penjelasan:** Mengganti bagian *subdomain* dengan daftar entri wordlist.

### 🔹 Virtual Host (VHOST) Enumeration
```bash
ffuf -u https://192.168.1.10 -H "Host: FUZZ.example.com" -w subdomains.txt
```
* **`-H "Host: FUZZ.example.com"`**: Digunakan jika Anda ingin menemukan VHOST tersembunyi yang berada di balik satu IP server yang sama.

---

## 4. Fuzzing Parameter (GET & POST)

### 🔹 Menguji Nama Parameter GET
```bash
ffuf -u "https://example.com/page.php?FUZZ=test" -w param_names.txt
```
* **Penjelasan:** Mencari nama parameter URL (*query parameter*) yang valid pada halaman target.

### 🔹 Menguji Nilai Parameter GET / Data Brute Force
```bash
ffuf -u "https://example.com/login.php?user=admin&pass=FUZZ" -w passwords.txt
```

### 🔹 Fuzzing Body Data POST
```bash
ffuf -u https://example.com/login -X POST   -H "Content-Type: application/x-www-form-urlencoded"   -d "username=admin&password=FUZZ"   -w passwords.txt
```
* **`-X POST`**: Menentukan metode HTTP POST.
* **`-d`**: Mengirimkan data *payload* pada *request body*.

---

## 5. Filtering & Matching (Menyaring Hasil)

Fitting & Matching adalah fitur paling krusial di FFUF untuk membuang *noise* / *false positive*.

### 🔹 Match Status Code (`-mc`)
```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt -mc 200,301,302
```
* **`-mc`**: Hanya menampilkan hasil jika kode status HTTP sesuai (misal: 200 OK, 301 Redirect).

### 🔹 Filter Status Code (`-fc`)
```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt -fc 404,403
```
* **`-fc`**: Menyembunyikan hasil yang mengembalikan kode status tertentu (misal: sembunyikan 404 Not Found).

### 🔹 Filter berdasarkan Ukuran Respon (`-fs`)
```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt -fs 4242
```
* **`-fs`**: Menyembunyikan respon yang memiliki ukuran (*size*) persis N bytes (sangat berguna untuk menyaring halaman *custom 404*).

### 🔹 Filter berdasarkan Jumlah Kata (`-fw`) & Baris (`-fl`)
```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt -fw 12 -fl 2
```
* **`-fw`**: Menyembunyikan respon berdasarkan jumlah kata (*word count*).
* **`-fl`**: Menyembunyikan respon berdasarkan jumlah baris (*line count*).

### 🔹 Filter dengan RegEx (`-fr`)
```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt -fr "Access Denied"
```
* **`-fr`**: Menyembunyikan respon jika teks/pola tertentu ditemukan di dalam *response body*.

---

## 6. Opsi HTTP Header, Cookie, & Autentikasi

### 🔹 Menambahkan Custom Header (`-H`)
```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt   -H "User-Agent: MyCustomScanner"   -H "Authorization: Bearer SECRET_TOKEN"
```

### 🔹 Penggunaan Cookie / Session (`-b`)
```bash
ffuf -u https://example.com/admin/FUZZ -w wordlist.txt   -b "PHPSESSID=abc123xyz456; security=low"
```
* **`-b`**: Mengirimkan *cookie* pada setiap permintaan.

---

## 7. Pengaturan Performa & Proxy

### 🔹 Mengatur Jumlah Thread (`-t`)
```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt -t 100
```
* **`-t`**: Menentukan jumlah *threads* bersamaan (default: 40). Semakin tinggi nilai, semakin cepat pemindaian.

### 🔹 Mengatur Rate Limiting / Delay (`-p`)
```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt -p 0.2
```
* **`-p`**: Memberikan jeda (*delay*) dalam detik antar-permintaan (misal `0.2` detik) untuk menghindari *rate limit* atau pemblokiran WAF.

### 🔹 Menggunakan Proxy / Burp Suite (`-x`)
```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt -x http://127.0.0.1:8080
```
* **`-x`**: Meneruskan seluruh lalu lintas FFUF melalui *proxy* (seperti Burp Suite atau OWASP ZAP) untuk analisis lebih rinci.

### 🔹 Mengabaikan Sertifikat SSL / TLS (`-k`)
```bash
ffuf -u https://192.168.1.1/FUZZ -w wordlist.txt -k
```
* **`-k`**: Mengabaikan validasi sertifikat SSL (*insecure HTTPS*).

---

## 8. Opsi Output & Penyimpanan

### 🔹 Menyimpan Output ke File (`-o` & `-of`)
```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt   -o hasil_scan.json -of json
```
* **`-o <file>`**: Menentukan nama berkas output.
* **`-of <format>`**: Menentukan format file output (`json`, `ejson`, `html`, `md`, `csv`, `all`).

---

## 9. Tabel Referensi Flag Populer

| Flag Singkat | Flag Panjang | Fungsi / Deskripsi |
| :--- | :--- | :--- |
| `-u` | `--url` | Menentukan URL target (wajib menyertakan kata kunci `FUZZ`) |
| `-w` | `--wordlist` | Menentukan lokasi file wordlist |
| `-X` | `--request-method` | Menentukan metode HTTP (`GET`, `POST`, `PUT`, `DELETE`, dll) |
| `-d` | `--data` | Mengirim data *POST body* |
| `-H` | `--header` | Menambahkan *header* kustom pada permintaan |
| `-b` | `--cookie` | Menambahkan *cookie* permintaan |
| `-e` | `--extensions` | Menambahkan daftar ekstensi file (contoh: `.php,.html`) |
| `-recursion` | `--recursion` | Mengaktifkan pemindaian rekursif pada direktori yang ditemukan |
| `-mc` | `--match-code` | Filter cocok berdasarkan kode status HTTP |
| `-fc` | `--filter-code` | Menyembunyikan respon berdasarkan kode status HTTP |
| `-fs` | `--filter-size` | Menyembunyikan respon berdasarkan ukuran berkas (*size*) |
| `-fw` | `--filter-words` | Menyembunyikan respon berdasarkan jumlah kata |
| `-fl` | `--filter-lines` | Menyembunyikan respon berdasarkan jumlah baris |
| `-t` | `--threads` | Mengatur jumlah thread bersamaan (default: 40) |
| `-p` | `--rate` | Jeda waktu (*delay*) antar permintaan dalam detik |
| `-x` | `--proxy` | Meneruskan permintaan melalui server proxy (contoh: Burp Suite) |
| `-k` | `--insecure` | Mengabaikan verifikasi sertifikat SSL/TLS |
| `-o` | `--output` | Menyimpan hasil ke dalam berkas |
| `-of` | `--output-format` | Menentukan format output (`json`, `csv`, `html`, `md`, `all`) |
| `-s` | `--silent` | Mode senyap (hanya menampilkan data hasil tanpa banner/header) |

---