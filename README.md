# 🛡️ Ultimate Cybersecurity Cheatsheet

Kumpulan cheatsheet pribadi untuk kebutuhan **penetration testing**, **network analysis**, dan **system administration**. Repo ini dibuat agar bisa diakses dari mana saja, sekaligus dibagikan publik dengan harapan membantu orang lain yang sedang belajar atau bekerja di bidang keamanan siber.

---

## 📋 Daftar Isi
1. [⚠️ Disclaimer & Etika Penggunaan](#️-disclaimer--etika-penggunaan)
2. [📚 Daftar Cheatsheet](#-daftar-cheatsheet)
3. [🧭 Cara Menggunakan Repo Ini](#-cara-menggunakan-repo-ini)
4. [🗺️ Alur Kerja Umum (Typical Workflow)](#️-alur-kerja-umum-typical-workflow)
5. [🤝 Kontribusi](#-kontribusi)
6. [📄 Lisensi](#-lisensi)

---

## ⚠️ Disclaimer & Etika Penggunaan

> 🚨 **PENTING — BACA SEBELUM MENGGUNAKAN.**

Seluruh perintah, teknik, dan *tool* yang didokumentasikan di repo ini **hanya untuk tujuan edukasi, riset pribadi, dan pengujian yang sah (authorized penetration testing)**.

* Gunakan hanya pada sistem, jaringan, atau aplikasi yang **Anda miliki sendiri**, atau yang **Anda punya izin tertulis (scope of engagement)** untuk mengujinya — misalnya lingkungan lab (HTB, TryHackMe, VulnHub, DVWA) atau kontrak *pentest* resmi.
* Melakukan *scanning*, eksploitasi, atau pengumpulan kredensial pada sistem milik pihak lain **tanpa izin adalah tindakan ilegal** di hampir semua yurisdiksi (termasuk UU ITE di Indonesia).
* Penulis repo ini **tidak bertanggung jawab** atas penyalahgunaan informasi di dalamnya.
* Selalu terapkan prinsip *responsible disclosure* jika menemukan kerentanan pada sistem pihak ketiga.

Kalau ragu — jangan lakukan. Minta izin tertulis dulu.

---

## 📚 Daftar Cheatsheet

| Folder | Kategori | Fokus Utama |
| :--- | :--- | :--- |
| [`Nmap/`](./Nmap) | Reconnaissance / Scanning | Port scanning, deteksi service & OS, NSE scripting |
| [`FFUF/`](./FFUF) | Web Fuzzing | Directory brute force, subdomain/VHOST enum, parameter fuzzing |
| [`cURL/`](./cURL) | Web / API Testing | HTTP request manual, debugging API, autentikasi |
| [`Metasploit/`](./Metasploit) | Exploitation | Eksploitasi, payload generation (MSFVenom), post-exploitation |
| [`Wireshark-Tshark/`](./Wireshark-Tshark) | Network Analysis | Packet capture, filtering, analisis protokol |
| [`Linux/`](./Linux) | System Administration | Navigasi, file management, proses, jaringan, hak akses |

---

## 🧭 Cara Menggunakan Repo Ini

Setiap folder berisi satu berkas `README.md` dengan format yang konsisten:

1. **Daftar Isi** — navigasi cepat ke tiap bagian.
2. **Penjelasan per-kategori** — dikelompokkan berdasarkan fungsi (misal: *scanning*, *filtering*, *output*).
3. **Contoh perintah siap pakai** — bisa langsung disalin dan disesuaikan dengan target.
4. **Penjelasan tiap flag/opsi** — agar tidak sekadar hafal perintah, tapi paham fungsinya.
5. **Tabel referensi flag populer** — rangkuman cepat di akhir setiap berkas.

Gunakan `Ctrl+F` / pencarian GitHub untuk mencari flag atau *tool* tertentu dengan cepat.

---

## 🗺️ Alur Kerja Umum (Typical Workflow)

Cheatsheet di repo ini pada dasarnya mengikuti alur *penetration testing* yang umum:

```text
1. Reconnaissance      → Nmap (port & service discovery)
2. Web Enumeration     → FFUF (directory/subdomain fuzzing) + cURL (manual request testing)
3. Vulnerability Scan  → Nmap NSE (--script vuln), Wireshark/TShark (analisis traffic mencurigakan)
4. Exploitation        → Metasploit (msfconsole, msfvenom)
5. Post-Exploitation   → Meterpreter, privilege escalation, lateral movement
6. System Handling     → Linux commands (setelah mendapatkan akses shell)
```

> 💡 **Tips:** Alur di atas tidak selalu linear — di dunia nyata, kamu akan bolak-balik antara *recon* dan *exploitation* seiring ditemukannya informasi baru.

---

## 🤝 Kontribusi

Repo ini awalnya dibuat untuk kebutuhan pribadi, tapi kontribusi tetap terbuka:

* Temukan kesalahan atau flag yang sudah *deprecated*? Buka [Issue](../../issues) atau [Pull Request](../../pulls).
* Ingin menambahkan cheatsheet tool baru? Silakan ajukan PR dengan format yang sama seperti berkas lain di repo ini.

---

## 📄 Lisensi

Konten repo ini dibagikan secara bebas untuk keperluan edukasi. Gunakan dengan bijak dan bertanggung jawab.