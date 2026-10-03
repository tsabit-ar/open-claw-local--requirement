# OpenClaw Setelah Sleep / Shutdown Windows

Dokumentasi ini menjelaskan apa yang terjadi pada OpenClaw ketika laptop masuk **Sleep**, **Restart**, atau **Shutdown**, serta cara mengaktifkan kembali OpenClaw pada setup:

```text
Windows
└── WSL2
    └── OpenClawGateway
        └── OpenClaw Gateway
```

## 1. Hal Penting

OpenClaw pada setup ini berjalan di laptop melalui **WSL2**.

Karena itu:

- OpenClaw menggunakan resource laptop ketika aktif.
- Sleep dapat menghentikan sementara aktivitas komputasi.
- Shutdown benar-benar menghentikan proses OpenClaw dan WSL.
- **Shutdown tidak menghapus OpenClaw atau konfigurasi.**
- Setelah Windows dinyalakan kembali, cukup pastikan `OpenClawGateway` aktif sebelum menggunakan TUI.

---

# 2. Saat Laptop Sleep

Ketika laptop masuk Sleep:

```text
Laptop
  ↓
Sleep
  ↓
Windows menangguhkan aktivitas
  ↓
WSL/OpenClaw berhenti bekerja sementara
  ↓
Laptop Wake
  ↓
Windows kembali aktif
  ↓
OpenClaw dapat digunakan kembali
```

Biasanya tidak perlu melakukan instalasi atau konfigurasi ulang.

Setelah laptop bangun, jika TUI masih dapat digunakan, tidak perlu melakukan apa-apa.

Jika OpenClaw tidak merespons, lakukan pengecekan Gateway.

---

# 3. Cek Gateway Setelah Wake

Buka Command Prompt atau PowerShell:

```cmd
wsl --list --verbose
```

Contoh:

```text
NAME               STATE           VERSION
* Ubuntu           Stopped         2
  docker-desktop   Stopped         2
  OpenClawGateway  Running         2
```

Yang perlu diperhatikan:

```text
OpenClawGateway    Running    2
```

Jika sudah `Running`, masuk ke Gateway:

```cmd
wsl -d OpenClawGateway
```

Kemudian cek:

```bash
openclaw gateway status
```

Jika Gateway normal, jalankan:

```bash
openclaw tui
```

---

# 4. Jika Gateway Tidak Berjalan

Jika hasil:

```cmd
wsl --list --verbose
```

menunjukkan:

```text
OpenClawGateway    Stopped    2
```

jangan langsung menginstal ulang OpenClaw.

Karena `OpenClawGateway` dibuat dan dikelola oleh OpenClaw Windows Companion, periksa terlebih dahulu status Gateway melalui Companion/Command Center.

Jika sudah masuk ke environment `OpenClawGateway`, status dapat diperiksa dengan:

```bash
openclaw gateway status
```

Jika diperlukan, Gateway dapat di-restart:

```bash
openclaw gateway restart
```

Kemudian:

```bash
openclaw tui
```

---

# 5. Setelah Shutdown Windows

Shutdown berbeda dengan Sleep.

Saat shutdown:

```text
Windows Shutdown
      ↓
WSL berhenti
      ↓
OpenClaw Gateway berhenti
      ↓
Laptop mati
```

Ini normal.

Tidak perlu menginstal ulang OpenClaw.

Setelah laptop dinyalakan kembali:

## Langkah 1 — Buka CMD / PowerShell

```cmd
wsl --list --verbose
```

## Langkah 2 — Pastikan `OpenClawGateway`

Idealnya:

```text
OpenClawGateway    Running    2
```

## Langkah 3 — Masuk ke Gateway

```cmd
wsl -d OpenClawGateway
```

## Langkah 4 — Jalankan TUI

```bash
openclaw tui
```

---

# 6. Quick Start Setelah Laptop Dinyalakan

Jika OpenClaw Gateway sudah aktif, cukup:

```cmd
wsl -d OpenClawGateway
```

kemudian:

```bash
openclaw tui
```

Workflow:

```text
Nyalakan laptop
      ↓
Buka CMD / Windows Terminal
      ↓
wsl -d OpenClawGateway
      ↓
openclaw tui
      ↓
Chat dengan OpenClaw
```

---

# 7. Jika TUI Tidak Bisa Terhubung

Jangan langsung reinstall.

Lakukan pemeriksaan berikut.

## A. Cek WSL

Dari Windows:

```cmd
wsl --list --verbose
```

Pastikan:

```text
OpenClawGateway    Running    2
```

## B. Masuk ke Gateway

```cmd
wsl -d OpenClawGateway
```

## C. Cek Gateway

```bash
openclaw gateway status
```

## D. Cek status OpenClaw

```bash
openclaw status
```

## E. Cek model/provider

```bash
openclaw models status
```

## F. Jika diperlukan, restart Gateway

```bash
openclaw gateway restart
```

Kemudian:

```bash
openclaw tui
```

---

# 8. Apakah OpenClaw Tetap Memakan RAM Setelah TUI Ditutup?

TUI dan Gateway adalah dua hal berbeda.

```text
openclaw tui
      │
      └── Client / interface terminal
              │
              ▼
      OpenClaw Gateway
```

Menutup TUI tidak selalu berarti Gateway ikut berhenti.

Karena itu:

```text
Ctrl + D
```

untuk keluar dari TUI **bukan berarti seluruh OpenClaw Gateway dimatikan**.

Jika ingin mengetahui apakah Gateway masih aktif, dari Windows:

```cmd
wsl --list --verbose
```

atau dari OpenClaw:

```bash
openclaw gateway status
```

---

# 9. Sleep vs Shutdown

| Kondisi | OpenClaw | Perlu Install Ulang? |
|---|---|---|
| TUI ditutup | Gateway dapat tetap berjalan | Tidak |
| Sleep | Aktivitas ditangguhkan | Tidak |
| Wake dari Sleep | Dapat digunakan kembali | Tidak |
| Restart Windows | WSL/Gateway berhenti lalu dapat aktif kembali | Tidak |
| Shutdown | WSL/Gateway berhenti | Tidak |
| Menyalakan Windows lagi | Gateway perlu tersedia kembali | Tidak |

---

# 10. Jangan Membuat Dua Gateway

Jika OpenClaw sudah menggunakan:

```text
OpenClawGateway
```

jangan membuat Gateway kedua secara manual hanya karena Gateway tidak langsung aktif setelah boot.

Dua Gateway dapat menyebabkan masalah seperti:

- port `18789` bentrok
- konfigurasi berbeda
- token berbeda
- TUI terhubung ke Gateway yang salah
- proses berjalan lebih banyak dari yang diperlukan

Pertahankan satu Gateway utama:

```text
OpenClawGateway
```

---

# 11. Automatic Startup

OpenClaw Windows Companion memiliki opsi:

```text
Launch OpenClaw at startup
```

Jika opsi tersebut aktif, OpenClaw dapat dipersiapkan ketika Windows startup.

Namun setup otomatis sebaiknya tidak dicampur dengan service Gateway manual lain.

Tujuannya:

```text
Windows Startup
      ↓
OpenClaw Companion / Gateway
      ↓
OpenClawGateway
      ↓
127.0.0.1:18789
      ↓
openclaw tui
```

Untuk setup saat ini, pertahankan konfigurasi Windows Companion yang sudah berhasil daripada membuat service tambahan secara manual.

---

# 12. Penggunaan yang Disarankan

Untuk penggunaan sehari-hari:

### Mulai menggunakan OpenClaw

```cmd
wsl -d OpenClawGateway
```

kemudian:

```bash
openclaw tui
```

### Selesai chatting

Keluar dari TUI:

```text
Ctrl + D
```

Kemudian laptop boleh:

- Sleep
- Restart
- Shutdown

Tidak perlu melakukan uninstall atau setup ulang.

---

# 13. Troubleshooting Cepat

### `openclaw` tidak dikenali di CMD Windows

Jangan menjalankan:

```cmd
openclaw tui
```

langsung dari CMD Windows jika CLI belum tersedia di PATH.

Gunakan:

```cmd
wsl -d OpenClawGateway
```

kemudian:

```bash
openclaw tui
```

### `OpenClawGateway` tidak ditemukan

Cek:

```cmd
wsl --list --verbose
```

Jika distro tidak muncul, periksa OpenClaw Windows Companion dan konfigurasi Gateway.

### `OpenClawGateway` Stopped

Periksa status melalui Windows Companion/Command Center.

Setelah Gateway aktif:

```cmd
wsl -d OpenClawGateway
```

kemudian:

```bash
openclaw gateway status
```

### TUI tidak merespons

Coba:

```bash
openclaw gateway status
```

kemudian:

```bash
openclaw gateway restart
```

lalu:

```bash
openclaw tui
```

---

# 14. Command Cheat Sheet

## Windows

```cmd
wsl --list --verbose
```

Melihat status semua distro WSL.

```cmd
wsl -d OpenClawGateway
```

Masuk ke environment OpenClaw.

## OpenClaw / WSL

```bash
openclaw tui
```

Membuka chat interface terminal.

```bash
openclaw gateway status
```

Melihat status Gateway.

```bash
openclaw gateway restart
```

Restart Gateway.

```bash
openclaw status
```

Melihat status OpenClaw.

```bash
openclaw models status
```

Melihat status model/provider.

```bash
openclaw logs --follow
```

Melihat log secara live.

---

# 15. Ringkasan

Setup OpenClaw ini menggunakan:

```text
Windows
   │
   ▼
WSL2
   │
   ▼
OpenClawGateway
   │
   ▼
OpenClaw Gateway
   │
   ▼
127.0.0.1:18789
   │
   ▼
openclaw tui
```

Setelah shutdown, **OpenClaw tidak hilang**.

Untuk menggunakan kembali:

```cmd
wsl -d OpenClawGateway
```

kemudian:

```bash
openclaw tui
```

Jika Gateway tidak aktif:

```bash
openclaw gateway status
```

dan jika diperlukan:

```bash
openclaw gateway restart
```

---

## Referensi Resmi

- Windows:
  https://docs.openclaw.ai/platforms/windows

- TUI:
  https://docs.openclaw.ai/web/tui

- Gateway:
  https://docs.openclaw.ai/gateway

- Gateway Troubleshooting:
  https://docs.openclaw.ai/gateway/troubleshooting

> **Keamanan:** Jangan menyimpan Gateway token, API key, password, atau secret di repository GitHub.
