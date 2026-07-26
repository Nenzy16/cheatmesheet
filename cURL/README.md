# 🚀 Ultimate cURL Cheatsheet

Panduan praktis dan ringkasan lengkap penggunaan **cURL** (*Client URL*) untuk menguji API, mengunduh file, *debugging* jaringan, dan otomasi skrip.

---

## 📋 Daftar Isi
1. [Sintaks Dasar](#1-sintaks-dasar)
2. [Metode HTTP (HTTP Verbs)](#2-metode-http-http-verbs)
3. [Mengirim Data & Content-Type](#3-mengirim-data--content-type)
4. [Kustomisasi Header & Autentikasi](#4-kustomisasi-header--autentikasi)
5. [Manajemen File & Unduhan](#5-manajemen-file--unduhan)
6. [Debugging, Log, & Output](#6-debugging-log--output)
7. [Penanganan Cookie & Session](#7-penanganan-cookie--session)
8. [Tabel Referensi Flag Populer](#8-tabel-referensi-flag-populer)

---

## 1. Sintaks Dasar

Format umum penggunaan cURL:

```bash
curl [opsi/flag] [URL]
```

### Contoh Dasar (GET Request):
```bash
curl https://api.example.com/data
```
> **Penjelasan:** Secara bawaan (*default*), cURL melakukan *request* dengan metode `GET` dan mencetak isi *response body* langsung ke terminal.

---

## 2. Metode HTTP (HTTP Verbs)

Gunakan flag `-X` (atau `--request`) untuk menentukan metode HTTP yang ingin digunakan.

### 🔹 GET (Mengambil Data)
```bash
curl -X GET https://api.example.com/users
```
* **`-X GET`**: Menentukan metode HTTP GET.

### 🔹 POST (Mengirim / Membuat Data Baru)
```bash
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{"nama": "Budi", "peran": "developer"}'
```
* **`-X POST`**: Menentukan metode HTTP POST.
* **`-d`**: Mengirimkan data *payload* di dalam *request body*.

### 🔹 PUT (Memperbarui Seluruh Data)
```bash
curl -X PUT https://api.example.com/users/123 \
  -H "Content-Type: application/json" \
  -d '{"nama": "Budi Santoso", "peran": "lead developer"}'
```
* **`-X PUT`**: Menentukan metode HTTP PUT untuk mengganti seluruh data pada *resource* terkait.

### 🔹 PATCH (Memperbarui Sebagian Data)
```bash
curl -X PATCH https://api.example.com/users/123 \
  -H "Content-Type: application/json" \
  -d '{"peran": "tech lead"}'
```
* **`-X PATCH`**: Menentukan metode HTTP PATCH untuk memperbarui sebagian properti saja.

### 🔹 DELETE (Menghapus Data)
```bash
curl -X DELETE https://api.example.com/users/123
```
* **`-X DELETE`**: Menentukan metode HTTP DELETE untuk menghapus *resource*.

---

## 3. Mengirim Data & Content-Type

### 🔹 Mengirim JSON (`application/json`)
```bash
curl -X POST https://api.example.com/products \
  -H "Content-Type: application/json" \
  -d '{"nama_produk": "Kopi", "harga": 15000}'
```
* **`-H "Content-Type: application/json"`**: Memberitahu server bahwa data yang dikirimkan berformat JSON.

### 🔹 Mengirim Form Biasa (`application/x-www-form-urlencoded`)
```bash
curl -X POST https://example.com/login \
  -d "username=budi" \
  -d "password=rahasia"
```
* **`-d`**: Mengirim parameter form. Jika `-H` tidak ditentukan, cURL otomatis menggunakan `application/x-www-form-urlencoded`.

### 🔹 Mengunggah File (`multipart/form-data`)
```bash
curl -X POST https://api.example.com/upload \
  -F "file=@/path/to/gambar.png" \
  -F "keterangan=Foto Profil"
```
* **`-F`**: Mengirim data menggunakan format `multipart/form-data`.
* **`@`**: Simbol `@` memberi tahu cURL untuk mengunggah berkas fisik dari direktori lokal.

---

## 4. Kustomisasi Header & Autentikasi

### 🔹 Menambahkan Custom Header
```bash
curl -H "Accept: application/json" \
  -H "User-Agent: MyApp/1.0" \
  https://api.example.com/data
```
* **`-H`**: Menambahkan baris *header* HTTP secara kustom.

### 🔹 Autentikasi Bearer Token (JWT / API Key)
```bash
curl -H "Authorization: Bearer YOUR_SECRET_TOKEN" \
  https://api.example.com/protected
```

### 🔹 Basic Authentication (Username & Password)
```bash
curl -u "admin:secret123" https://api.example.com/admin
```
* **`-u`** atau **`--user`**: Otomatis mengenkode `username:password` menjadi header *Basic Auth* Base64.

---

## 5. Manajemen File & Unduhan

### 🔹 Menyimpan Output ke File (`-o` vs `-O`)

1. **Mengubah Nama File Output (`-o`):**
   ```bash
   curl -o hasil_download.zip https://example.com/files/archive.zip
   ```
   * **`-o <nama_file>`**: Menyimpan hasil unduhan dengan nama file kustom yang ditentukan.

2. **Menggunakan Nama File Asli Server (`-O`):**
   ```bash
   curl -O https://example.com/files/archive.zip
   ```
   * **`-O`**: Otomatis menyimpan file dengan nama asli dari URL (`archive.zip`).

### 🔹 Meneruskan Redirection (`-L`)
```bash
curl -L -O https://example.com/redirect-link
```
* **`-L`**: Menginstruksikan cURL untuk mengikuti *redirect* jika server mengembalikan status code 301/302.

### 🔹 Melanjutkan Unduhan yang Terputus (`-C -`)
```bash
curl -C - -O https://example.com/file_besar.iso
```
* **`-C -`**: Melanjutkan (*resume*) proses unduhan file yang sempat terhenti dari titik terakhir.

---

## 6. Debugging, Log, & Output

### 🔹 Menampilkan Response Header (`-i`)
```bash
curl -i https://api.example.com/users
```
* **`-i`**: Menampilkan informasi *header* respon HTTP bersamaan dengan *body* respon.

### 🔹 Menampilkan Hanya Header (`-I`)
```bash
curl -I https://example.com
```
* **`-I`**: Melakukan permintaan `HEAD` untuk melihat *header* respon saja tanpa mengambil *body*.

### 🔹 Mode Verbose / Laporan Lengkap (`-v`)
```bash
curl -v https://api.example.com/users
```
* **`-v`**: Menampilkan seluruh rincian proses koneksi (handshake SSL, header terkirim, header diterima, dll) untuk keperluan *debugging*.

### 🔹 Mode Senyap (`-s`)
```bash
curl -s https://api.example.com/data
```
* **`-s`**: Mengabaikan indikator *progress bar* dan pesan kesalahan (*silent mode*).

---

## 7. Penanganan Cookie & Session

### 🔹 Menyimpan Cookie ke Dalam File (`-c`)
```bash
curl -c cookies.txt -d "user=budi&pass=123" https://example.com/login
```
* **`-c <file>`**: Menyimpan seluruh *cookie* yang dikembalikan server ke dalam berkas teks.

### 🔹 Mengirimkan Cookie dari File (`-b`)
```bash
curl -b cookies.txt https://example.com/dashboard
```
* **`-b <file>`**: Membaca dan mengirimkan *cookie* dari berkas teks ke server untuk mempertahankan status sesi (*session*).

---

## 8. Tabel Referensi Flag Populer

| Flag Singkat | Flag Panjang | Fungsi / Deskripsi |
| :--- | :--- | :--- |
| `-X` | `--request` | Menentukan metode/verb HTTP (`GET`, `POST`, `PUT`, `DELETE`, dll) |
| `-H` | `--header` | Menambahkan *header* khusus pada *request* |
| `-d` | `--data` | Mengirim data *payload* dalam *request body* |
| `-F` | `--form` | Mengirim data formulir `multipart/form-data` (biasanya untuk unggah file) |
| `-u` | `--user` | Menentukan kredensial untuk *Basic Authentication* (`user:pass`) |
| `-o` | `--output` | Menyimpan output respon ke dalam berkas lokal dengan nama tertentu |
| `-O` | `--remote-name` | Menyimpan output menggunakan nama berkas asli dari server |
| `-L` | `--location` | Mengikuti arahan *redirect* HTTP (301/302) |
| `-i` | `--include` | Menampilkan *header* respon HTTP pada output |
| `-I` | `--head` | Mengambil data *header* respon saja (permintaan `HEAD`) |
| `-v` | `--verbose` | Menampilkan log alur komunikasi jaringan secara rinci |
| `-s` | `--silent` | Mematikan tampilan *progress bar* dan pesan log (*silent mode*) |
| `-k` | `--insecure` | Mengabaikan pemeriksaan sertifikat SSL/TLS (berguna untuk lingkungan dev/self-signed) |
| `-C` | `--continue-at` | Melanjutkan proses unduhan yang terputus |
| `-c` | `--cookie-jar` | Menyimpan *cookie* dari respon ke dalam berkas |
| `-b` | `--cookie` | Mengirimkan *cookie* dari berkas/string ke server |

---
