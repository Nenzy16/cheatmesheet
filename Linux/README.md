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
8. [Manajemen Paket (Package Management)](#8-manajemen-paket-package-management)
9. [Manajemen Service & Systemd](#9-manajemen-service--systemd)
10. [Penjadwalan Tugas (Cron Jobs)](#10-penjadwalan-tugas-cron-jobs)
11. [SSH & Key Management](#11-ssh--key-management)
12. [Kompresi & Arsip Berkas](#12-kompresi--arsip-berkas)
13. [Tabel Referensi Perintah Populer](#13-tabel-referensi-perintah-populer)

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

## 8. Manajemen Paket (Package Management)

Perintah untuk memasang, memperbarui, dan menghapus perangkat lunak berbeda-beda tergantung distribusi Linux yang digunakan.

### 🔹 Debian / Ubuntu (`apt`)
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install nmap -y
sudo apt remove nmap -y
```
* **`apt update`**: Memperbarui daftar paket dari repository.
* **`apt upgrade`**: Memperbarui seluruh paket terpasang ke versi terbaru.
* **`apt install` / `apt remove`**: Memasang atau menghapus paket tertentu.

### 🔹 RHEL / CentOS / Fedora (`yum` / `dnf`)
```bash
sudo dnf install nmap -y
sudo dnf remove nmap -y
```
* **`dnf`**: Pengganti `yum` di distro modern (Fedora, RHEL 8+, CentOS Stream).

### 🔹 Mencari Paket & Melihat Informasi
```bash
apt search wireshark
apt show wireshark
```
* **`search`**: Mencari nama paket berdasarkan kata kunci.
* **`show`**: Menampilkan detail versi, dependensi, dan deskripsi paket.

---

## 9. Manajemen Service & Systemd

`systemctl` adalah alat utama untuk mengelola *service* (daemon) pada sistem Linux modern (systemd-based).

### 🔹 Mengelola Status Service
```bash
sudo systemctl start ssh
sudo systemctl stop ssh
sudo systemctl restart ssh
sudo systemctl status ssh
```
* **`start` / `stop` / `restart`**: Menjalankan, menghentikan, atau me-restart service.
* **`status`**: Menampilkan status aktif/tidak beserta log terakhir.

### 🔹 Mengaktifkan Service Saat Boot
```bash
sudo systemctl enable ssh
sudo systemctl disable ssh
```
* **`enable`**: Membuat service berjalan otomatis saat sistem dinyalakan.
* **`disable`**: Mencegah service berjalan otomatis saat boot.

### 🔹 Melihat Log Service (`journalctl`)
```bash
journalctl -u ssh -f
```
* **`-u <service>`**: Menampilkan log khusus untuk unit/service tertentu.
* **`-f`**: Mode *follow*, menampilkan log baru secara real-time.

---

## 10. Penjadwalan Tugas (Cron Jobs)

`cron` digunakan untuk menjalankan perintah/skrip secara otomatis pada waktu yang terjadwal.

### 🔹 Mengedit Jadwal Cron Pengguna
```bash
crontab -e
```

### 🔹 Format Penjadwalan Cron
```text
* * * * * /path/ke/skrip.sh
│ │ │ │ │
│ │ │ │ └── Hari (0-6, Minggu=0)
│ │ │ └──── Bulan (1-12)
│ │ └────── Tanggal (1-31)
│ └──────── Jam (0-23)
└────────── Menit (0-59)
```

### 🔹 Contoh Praktis
```bash
0 2 * * * /home/user/backup.sh        # Jalankan setiap jam 2 pagi
*/15 * * * * /home/user/monitor.sh    # Jalankan setiap 15 menit
```

### 🔹 Melihat Jadwal Cron Aktif
```bash
crontab -l
```
* **`-l`**: Menampilkan daftar cron job milik user saat ini.

---

## 11. SSH & Key Management

### 🔹 Membuat Pasangan SSH Key
```bash
ssh-keygen -t ed25519 -C "email@contoh.com"
```
* **`-t`**: Menentukan tipe algoritma key (`ed25519` direkomendasikan, `rsa` untuk kompatibilitas lama).
* **`-C`**: Menambahkan komentar/label pada key (biasanya email).

### 🔹 Menyalin Public Key ke Server (`ssh-copy-id`)
```bash
ssh-copy-id user@192.168.1.10
```
* **Penjelasan:** Menambahkan public key lokal ke `~/.ssh/authorized_keys` di server tujuan, memungkinkan login tanpa password.

### 🔹 Login SSH dengan Key Spesifik
```bash
ssh -i ~/.ssh/id_ed25519 user@192.168.1.10
```
* **`-i <file>`**: Menentukan private key yang digunakan untuk autentikasi.

### 🔹 Transfer Berkas via SSH (`scp`)
```bash
scp file.txt user@192.168.1.10:/home/user/
scp -r folder/ user@192.168.1.10:/home/user/
```
* **`-r`**: Menyalin direktori beserta seluruh isinya secara rekursif.

---

## 12. Kompresi & Arsip Berkas

### 🔹 Membuat Arsip TAR (`tar`)
```bash
tar -czvf arsip.tar.gz /path/ke/folder
```
* **`-c`**: Membuat arsip baru.
* **`-z`**: Kompresi menggunakan gzip.
* **`-v`**: Mode verbose, menampilkan proses.
* **`-f`**: Menentukan nama file arsip output.

### 🔹 Mengekstrak Arsip TAR
```bash
tar -xzvf arsip.tar.gz -C /path/tujuan
```
* **`-x`**: Mengekstrak isi arsip.
* **`-C`**: Menentukan direktori tujuan hasil ekstraksi.

### 🔹 Kompresi & Ekstraksi ZIP
```bash
zip -r arsip.zip folder/
unzip arsip.zip -d /path/tujuan
```
* **`zip -r`**: Mengompresi folder secara rekursif ke dalam `.zip`.
* **`unzip -d`**: Mengekstrak isi `.zip` ke direktori tujuan tertentu.

---

## 13. Tabel Referensi Perintah Populer

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
| `apt`/`dnf` | `install` | Memasang paket perangkat lunak dari repository |
| `systemctl` | `status` | Melihat/mengelola status service systemd |
| `crontab` | `-e` | Mengedit jadwal tugas otomatis (cron job) |
| `ssh-keygen` | `-t ed25519` | Membuat pasangan SSH key baru |
| `scp` | `-r` | Menyalin berkas/folder antar host via SSH |
| `tar` | `-czvf` | Membuat arsip terkompresi `.tar.gz` |

---