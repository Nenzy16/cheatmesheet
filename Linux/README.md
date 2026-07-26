# 🐧 Ultimate Linux Commands Cheatsheet

Panduan praktis dan ringkasan lengkap penggunaan **perintah dasar Linux** (*Linux Command Line / Bash*) untuk navigasi sistem, manajemen berkas, pengelolaan pengguna, pemantauan proses, dan jaringan.

---

## 📋 Daftar Isi
1. [Navigasi & Direktori](#1-navigasi--direktori)
2. [Manajemen Berkas & File Operations](#2-manajemen-berkas--file-operations)
3. [Membaca & Memproses Teks](#3-membaca--memproses-teks)
4. [Hak Akses & Pengguna (Permissions & Users)](#4-hak-akses--pengguna-permissions--users)
5. [Manajemen Proses & Sistem](#5-manajemen-proses--sistem)
6. [Jaringan & Transfer File](#6-jaringan--transfer-file)
7. [Pencarian Berkas (Finding Files)](#7-pencarian-berkas-finding-files)
8. [Tabel Referensi Perintah Populer](#8-tabel-referensi-perintah-populer)

---

## 1. Navigasi & Direktori

### 🔹 `ls` (List Directory Contents)
Menampilkan daftar berkas dan folder di dalam direktori.

```bash
ls -la /var/log
```
* **`-l`**: Menampilkan format panjang (*long format*) lengkap dengan hak akses, pemilik, ukuran, dan tanggal modifikasi.
* **`-a`**: Menampilkan seluruh berkas, termasuk berkas tersembunyi (*hidden files* yang diawali titik `.`).
* **`-h`**: Menampilkan ukuran berkas dalam format yang mudah dibaca manusia (*Human-readable*, misal: KB, MB, GB).

### 🔹 `cd` (Change Directory)
Pindah ke direktori lain.

```bash
cd /var/www/html
cd ..
cd ~
```
* **`..`**: Pindah ke satu tingkat direktori di atasnya.
* **`~`**: Pindah langsung ke direktori rumah pengguna (*Home Directory*).

### 🔹 `pwd` (Print Working Directory)
Menampilkan jalur direktori aktif saat ini.

```bash
pwd
```

---

## 2. Manajemen Berkas & File Operations

### 🔹 `mkdir` (Make Directory)
Membuat direktori baru.

```bash
mkdir -p project/src/components
```
* **`-p`**: Membuat direktori induk (*parent directory*) secara otomatis jika belum ada.

### 🔹 `cp` (Copy Files/Directories)
Menyalin berkas atau folder.

```bash
cp -rv /path/asal /path/tujuan
```
* **`-r`**: Menyalin direktori secara rekursi beserta seluruh isinya.
* **`-v`**: Mode *verbose*, menampilkan rincian berkas yang sedang disalin.

### 🔹 `mv` (Move / Rename)
Memindahkan atau mengubah nama berkas/direktori.

```bash
mv -i file_lama.txt /tmp/file_baru.txt
```
* **`-i`**: Mode interaktif, meminta konfirmasi sebelum menimpa file yang sudah ada.

### 🔹 `rm` (Remove Files/Directories)
Menghapus berkas atau direktori.

```bash
rm -rf /path/ke/folder
```
* **`-r`**: Menghapus direktori dan seluruh isinya secara rekursif.
* **`-f`**: Menghapus secara paksa (*force*) tanpa meminta konfirmasi.

---

## 3. Membaca & Memproses Teks

### 🔹 `cat` (Concatenate and Print)
Mencetak seluruh isi berkas ke layar terminal.

```bash
cat -n config.txt
```
* **`-n`**: Menampilkan nomor baris pada setiap teks.

### 🔹 `grep` (Global Regular Expression Print)
Mencari pola teks tertentu di dalam berkas.

```bash
grep -rn "ERROR" /var/log/
```
* **`-r`**: Mencari secara rekursif di dalam seluruh sub-direktori.
* **`-i`**: Mengabaikan perbedaan huruf besar/kecil (*case-insensitive*).
* **`-n`**: Menampilkan nomor baris tempat teks ditemukan.

### 🔹 `head` & `tail` (Output First/Last Part of Files)
```bash
head -n 20 /var/log/syslog
tail -f /var/log/syslog
```
* **`head -n 20`**: Menampilkan 20 baris pertama dari berkas.
* **`tail -f`**: Pemantauan langsung (*real-time follow*), terus memperbarui tampilan saat ada baris baru yang ditambahkan ke berkas.

---

## 4. Hak Akses & Pengguna (Permissions & Users)

### 🔹 `chmod` (Change File Modes/Permissions)
Mengubah hak akses baca (`r`), tulis (`w`), dan eksekusi (`x`) pada berkas.

```bash
chmod -R 755 /var/www/html
```
* **`-R`**: Menerapkan perubahan hak akses secara rekursif ke seluruh sub-folder dan berkas di dalamnya.
* **`755`**: Owner (Read/Write/Execute = 7), Group (Read/Execute = 5), Others (Read/Execute = 5).

### 🔹 `chown` (Change File Owner and Group)
Mengubah pemilik (*owner*) dan grup dari suatu berkas.

```bash
chown -R www-data:www-data /var/www/html
```
* **`-R`**: Mengubah kepemilikan secara rekursif untuk seluruh isi direktori.

---

## 5. Manajemen Proses & Sistem

### 🔹 `ps` (Process Status)
Menampilkan daftar proses yang sedang berjalan.

```bash
ps aux | grep nginx
```
* **`a`**: Menampilkan proses dari seluruh pengguna.
* **`u`**: Menampilkan detail pengguna dan penggunaan memori/CPU.
* **`x`**: Menampilkan proses yang berjalan tanpa terikat pada terminal tertentu.

### 🔹 `kill` (Terminate Process)
Mengeksekusi penghentian proses berdasarkan PID (*Process ID*).

```bash
kill -9 1234
```
* **`-9`**: Mengirim sinyal `SIGKILL` untuk menghentikan proses secara paksa.

### 🔹 `df` & `du` (Disk Space Usage)
```bash
df -h
du -sh /var/log/*
```
* **`df -h`**: Menampilkan penggunaan ruang disk pada seluruh *filesystem* dalam format *human-readable*.
* **`du -sh`**: Menampilkan total ringkasan (*summary*) ukuran folder spesifik.

---

## 6. Jaringan & Transfer File

### 🔹 `ping` (Test Network Connectivity)
Menguji koneksi jaringan ke host target.

```bash
ping -c 4 google.com
```
* **`-c <jumlah>`**: Membatasi pengiriman paket *echo request* sebanyak N kali.

### 🔹 `curl` / `wget` (Transfer Data from URL)
```bash
curl -i -X GET https://api.example.com
wget -c https://example.com/file.zip
```
* **`curl -i`**: Menampilkan *header* respon HTTP.
* **`wget -c`**: Melanjutkan unduhan berkas yang sempat terputus (*resume download*).

---

## 7. Pencarian Berkas (Finding Files)

### 🔹 `find` (Search Files in Directory Hierarchy)
Mencari berkas berdasarkan nama, ukuran, atau tanggal modifikasi.

```bash
find /var/www -type f -name "*.php"
```
* **`-type f`**: Membatasi pencarian hanya untuk berkas biasa (*files*).
* **`-name`**: Pencarian berdasarkan pola nama berkas.

---

## 8. Tabel Referensi Perintah Populer

| Perintah | Opsi / Flag | Fungsi / Deskripsi |
| :--- | :--- | :--- |
| `ls` | `-la` | Menampilkan seluruh berkas (termasuk tersembunyi) dengan format rincian lengkap |
| `cp` | `-r` | Menyalin berkas/direktori secara rekursif |
| `rm` | `-rf` | Menghapus berkas/direktori secara paksa dan rekursif |
| `grep` | `-rn` | Mencari kata/teks tertentu di seluruh folder beserta nomor barisnya |
| `tail` | `-f` | Memantau perubahan isi berkas log secara *real-time* |
| `chmod` | `-R` | Mengubah hak akses berkas secara rekursif |
| `chown` | `-R` | Mengubah pemilik berkas/folder secara rekursif |
| `ps` | `aux` | Menampilkan seluruh proses sistem yang sedang aktif |
| `kill` | `-9` | Mematikan proses secara paksa berdasarkan PID |
| `df` | `-h` | Menampilkan informasi kapasitas dan ruang disk sistem |
| `find` | `-type f` | Mencari berkas fisik berdasarkan kriteria tertentu |

---