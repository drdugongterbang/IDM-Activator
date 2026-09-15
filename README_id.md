# IDM Activator by dugong

Aktivator untuk **Internet Download Manager (IDM)** berbasis batch script (`.cmd`) yang bekerja melalui manipulasi registry Windows. Dibuat oleh **dugong_terbang**. Petunjuk pemakaian ada di bawah

> **Disclaimer:** Tool ini disediakan untuk tujuan edukasi. Pengguna tool bertanggung jawab penuh atas penggunaannya. Jangan dijual ulang.

---

## Isi Folder

| File | Keterangan |
|------|------------|
| `IDM Activator by dugong.cmd` | Script aktivator utama (~24 KB) |
| `idman642build7.exe` | Installer IDM v6.42 Build 7 (~12 MB, Maret 2024) |

---

## Persyaratan Sistem

- **OS:** Windows 7 / 8 / 8.1 / 10 / 11 atau Windows Server
- **Arsitektur:** x86 (32-bit) dan x64 (64-bit), termasuk ARM64
- **PowerShell:** Harus terinstall (sudah default di Windows 7+)
- **Internet:** WAJIB terkoneksi saat proses aktivasi
- **IDM:** Harus sudah terinstall di komputer

---

## Cara Kerja Script

Script ini melakukan aktivasi IDM melalui manipulasi registry Windows secara langsung. Berikut alur kerjanya:

### 1. Deteksi Arsitektur & Elevasi Admin
- Script otomatis mendeteksi apakah sistem x86, x64, atau ARM64
- Script akan meminta **elevasi hak Administrator** (UAC prompt) — cukup klik **YES**
- Jika dijalankan dari x86 pada sistem x64, script akan me-launch ulang dirinya dalam proses x64

### 2. Aktivasi IDM (Opsi 1)
Alur aktivasi:
1. **Kill proses IDM** yang sedang berjalan
2. **Hapus registry keys** lama yang berkaitan dengan trial/serial IDM di:
   - `HKCU\Software\DownloadManager` (FName, LName, Email, Serial, scansk, tvfrdt, dll.)
   - `HKLM\SOFTWARE\Internet Download Manager` (atau Wow6432Node pada x64)
3. **Reset state IDM** — menghapus data trial agar dianggap fresh install
4. **Menulis data registrasi baru** ke registry:
   - Nama yang diinput user (atau default `dugongterbang`)
   - Email: `info@tonec.com`
   - Serial: `FOX6H-3KWH4-7TSIN-Q4US7`
5. **Trigger download** file kecil dari `internetdownloadmanager.com` untuk memicu IDM membuat registry keys internal yang dibutuhkan
6. **Scan dan hitung registry keys** di `HKCU\Software\Classes\CLSID` — mencari keys dengan pola GUID yang berkaitan dengan IDM
7. **Lock registry keys**, mengubah permission menjadi **Deny FullControl** untuk user `Everyone` (S-1-1-0), sehingga IDM tidak bisa mengubahnya kembali dan tidak bisa mendeteksi status trial/serial
8. Minimal **7 registry keys** harus berhasil di-lock untuk aktivasi dianggap sukses

### 3. Reset IDM Trial (Opsi 2)
- Menghapus semua registry keys terkait aktivasi dan trial IDM
- Menghapus file `settings.bak` di folder DMCache
- Menambahkan kembali key `AdvIntDriverEnabled2` bernilai `1`
- IDM kembali ke status trial 30 hari
- Juga berguna untuk **memperbaiki error "Fake Serial Number"**

### 4. Toggle Windows Firewall (Opsi 3)
- Menampilkan status firewall (Enabled/Disabled/Unclear)
- Bisa menyalakan atau mematikan firewall dari menu
- Berguna karena firewall kadang memblokir proses aktivasi

### 5. Registry Key Locking Mechanisme
Inti dari proteksi aktivator ini adalah fungsi `lock_key` yang:
- Mengambil ownership registry key menggunakan PowerShell (via snippet oleh AveYo)
- Menetapkan **Deny FullControl** permission ke SID `S-1-1-0` (Everyone)
- Memverifikasi bahwa reg_delete **gagal** (artinya lock berhasil — key tidak bisa dihapus)
- Setelah lock, IDM tidak bisa mengubah/menghapus key tersebut, sehingga serial tetap terdaftar

---

## PETUNJUK PEMAKAIAN

### Aktivasi IDM (Pertama Kali atau Setelah Install)

1. **Install IDM** terlebih dahulu (gunakan `idman642build7.exe` atau download versi terbaru dari [situs resmi](https://www.internetdownloadmanager.com/download.html))
2. Pastikan **terkoneksi internet**
3. **Double-click** `IDM Activator by dugong.cmd`
4. Saat muncul UAC prompt, klik **YES**
5. Pilih menu **[1] Aktivasi IDM Anda**
6. Masukkan nama (atau tekan Enter untuk default)
7. Tunggu proses selesai — jika berhasil akan tertulis **"IDM Berhasil diaktivasi"**
8. Buka IDM, seharusnya sudah full version

### Reset Trial / Fix Fake Serial

1. Double-click script
2. Pilih menu **[2] Reset IDM Trial**
3. Tunggu hingga selesai
4. Buka IDM — trial sudah direset, bisa dipakai 30 hari lagi

### Mode Command Line (Silent/Unattended)

```
"IDM Activator by dugong.cmd" /act      :: Aktivasi langsung
"IDM Activator by dugong.cmd" /res      :: Reset trial langsung
"IDM Activator by dugong.cmd" /act /s   :: Aktivasi silent (tanpa menu)
"IDM Activator by dugong.cmd" /res /s   :: Reset silent (tanpa menu)
```

---

## Troubleshooting

| Masalah | Solusi |
|---------|--------|
| **"Fake Serial Number"** muncul | Jalankan Opsi 2 (Reset), lalu Opsi 1 (Aktivasi) ulang |
| **Access Denied** saat aktivasi | Buat user Windows baru dengan akses Administrator, login ke user baru, jalankan aktivasi dari sana |
| **Gagal membuat registry keys** | Disable Windows Firewall lewat Opsi 3, lalu coba aktivasi lagi |
| **Antivirus mendeteksi virus** | Ini false positive. Suspend sementara real-time protection antivirus, atau exclude folder script dari scanning |
| **IDM minta aktivasi lagi** setelah update | Jalankan ulang aktivasi (Opsi 1) — jarang terjadi |
| **Script tidak jalan sama sekali** | Pastikan PowerShell terinstall, dan OS minimal Windows 7 |

### Tips Penting

- **Jangan** klik kanan > Run as Administrator. Cukup double-click biasa, lalu klik YES saat UAC prompt muncul
- **Jangan edit/modifikasi** script — bisa menyebabkan error fatal
- Jika sebelumnya menggunakan aktivator lain, **uninstall** dulu aktivator tersebut (terutama yang memakai metode firewall block)
- Jika masih gagal, uninstall IDM dari Control Panel, download installer terbaru dari situs resmi, install ulang, lalu jalankan aktivator

---

## File Installer IDM

File `idman642build7.exe` yang disertakan adalah installer resmi IDM versi **6.42 Build 7** (dirilis Maret 2024). Untuk versi terbaru, download dari:
https://www.internetdownloadmanager.com/download.html

---

## Credits

- **Author:** dugong_terbang
- **Registry lock snippet:** AveYo
- **GitHub:** https://github.com/drdugongterbang
