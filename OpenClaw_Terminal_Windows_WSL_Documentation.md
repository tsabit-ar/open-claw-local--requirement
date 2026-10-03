# OpenClaw via Terminal di Windows + WSL2

Dokumentasi ini menjelaskan cara mengakses **OpenClaw melalui terminal/TUI** pada Windows setelah OpenClaw Windows Companion membuat Gateway WSL bernama `OpenClawGateway`.

> **Tujuan:** menggunakan OpenClaw tanpa harus membuka browser Control UI atau jendela Windows Companion setiap kali ingin chatting.

---

## 1. Arsitektur Setup

Pada setup ini, OpenClaw menggunakan Windows Hub/Companion untuk membuat Gateway WSL khusus.

```text
Windows
│
├── OpenClaw Windows Companion
│
└── WSL2
    └── OpenClawGateway
        └── OpenClaw Gateway
             │
             └── 127.0.0.1:18789
```

Distro `OpenClawGateway` berbeda dari distro Ubuntu biasa yang mungkin sudah ada di Windows.

Untuk melihat distro WSL:

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

Dalam setup ini, `OpenClawGateway` adalah distro yang digunakan oleh Windows Companion untuk Gateway lokal.

---

## 2. Mengapa `openclaw` Tidak Bisa Dipanggil dari CMD Windows?

Jika menjalankan:

```cmd
openclaw tui
```

dan muncul:

```text
'openclaw' is not recognized as an internal or external command
```

itu berarti command `openclaw` tidak tersedia pada PATH Windows saat ini.

Jangan langsung menginstal ulang OpenClaw.

Jika Gateway berada di WSL, masuk terlebih dahulu ke distro `OpenClawGateway`.

---

## 3. Masuk ke OpenClawGateway

Dari **Command Prompt** atau **PowerShell** Windows:

```cmd
wsl -d OpenClawGateway
```

Setelah berhasil masuk, prompt terminal akan berubah dari Windows, misalnya:

```text
C:\Users\...
```

menjadi prompt Linux/WSL.

---

## 4. Pastikan CLI OpenClaw Tersedia

Di dalam `OpenClawGateway`, jalankan:

```bash
which openclaw
```

Jika menghasilkan path seperti:

```text
/usr/bin/openclaw
```

atau path lain yang valid, berarti CLI tersedia.

Cek versi:

```bash
openclaw --version
```

---

## 5. Menjalankan OpenClaw TUI

Setelah masuk ke `OpenClawGateway`, jalankan:

```bash
openclaw tui
```

TUI (**Terminal User Interface**) akan membuka antarmuka chat langsung di terminal.

Kemudian ketik pesan dan tekan:

```text
Enter
```

Menurut dokumentasi OpenClaw, `openclaw tui` secara default digunakan dalam **Gateway mode**, sedangkan `openclaw tui --local` digunakan untuk local embedded agent runtime. Untuk setup yang sudah memiliki Gateway, gunakan `openclaw tui`.

---

## 6. Alur Penggunaan Sehari-hari

Jika `OpenClawGateway` sudah berjalan:

### Langkah 1 — buka CMD/PowerShell

```cmd
wsl -d OpenClawGateway
```

### Langkah 2 — buka TUI

```bash
openclaw tui
```

### Langkah 3 — mulai chatting

Contoh:

```text
Buatkan ringkasan file ini.
```

atau:

```text
Buka project saya dan jelaskan struktur foldernya.
```

atau:

```text
Cari masalah pada kode Python saya.
```

---

## 7. Keluar dari TUI

Untuk keluar dari TUI, gunakan shortcut/command keluar yang tersedia pada TUI.

Jika perlu menghentikan proses terminal secara paksa:

```text
Ctrl + C
```

**Catatan:** keluar dari TUI tidak sama dengan mematikan Gateway. TUI adalah client yang terhubung ke Gateway.

---

## 8. Mengecek Status Gateway

Di dalam environment OpenClaw:

```bash
openclaw gateway status
```

Untuk melihat status OpenClaw secara umum:

```bash
openclaw status
```

Untuk melihat status model:

```bash
openclaw models status
```

Untuk melihat log secara live:

```bash
openclaw logs --follow
```

Jika TUI tidak memberikan respons, pemeriksaan yang berguna adalah:

```bash
openclaw status
openclaw models status
openclaw logs --follow
```

---

## 9. Mengecek Status WSL dari Windows

Keluar ke Windows atau buka terminal Windows baru, kemudian:

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

Hal yang perlu diperhatikan:

- `VERSION` sebaiknya `2`
- `OpenClawGateway` harus tersedia
- Gateway yang aktif biasanya terlihat sebagai `Running`

---

## 10. Jika `OpenClawGateway` Tidak Berjalan

Dari Windows:

```cmd
wsl --list --verbose
```

Jika `OpenClawGateway` terlihat `Stopped`, jangan langsung membuat distro baru.

Karena distro ini dikelola oleh Windows Companion, periksa terlebih dahulu status Gateway melalui OpenClaw Companion/Command Center.

Untuk instalasi WSL manual, OpenClaw juga mendukung pengecekan Gateway dari WSL:

```bash
openclaw gateway status
```

---

## 11. Gateway Lokal

Pada setup ini Gateway menggunakan alamat loopback:

```text
127.0.0.1:18789
```

Ini berarti Gateway hanya diekspos secara lokal pada komputer tersebut.

TUI biasanya dapat menggunakan konfigurasi Gateway yang sudah tersimpan, sehingga tidak perlu memasukkan URL dan token secara manual jika konfigurasi lokal sudah benar.

---

## 12. Jangan Menaruh Gateway Token di GitHub

**Jangan pernah memasukkan Gateway token, API key, password, atau secret ke repository GitHub.**

Jangan membuat dokumentasi seperti:

```bash
openclaw tui --token my-secret-token
```

dengan token asli.

Jika token diperlukan secara manual, gunakan placeholder:

```bash
openclaw tui --url ws://127.0.0.1:18789 --token <GATEWAY_TOKEN>
```

Gunakan:

```text
<GATEWAY_TOKEN>
```

bukan token sebenarnya.

---

## 13. Gateway Mode vs Local Mode

OpenClaw TUI memiliki dua konsep utama.

### Gateway mode

```bash
openclaw tui
```

TUI terhubung ke Gateway.

Ini adalah mode yang digunakan untuk setup Windows + `OpenClawGateway`.

### Local mode

```bash
openclaw tui --local
```

atau:

```bash
openclaw chat
```

Mode ini menjalankan embedded agent runtime secara lokal tanpa Gateway.

Perbedaannya penting karena fitur yang hanya tersedia melalui Gateway tidak tersedia dalam local mode.

**Untuk setup ini, gunakan:**

```bash
openclaw tui
```

---

## 14. Jika `which openclaw` Tidak Menghasilkan Apa Pun

Jika:

```bash
which openclaw
```

tidak menghasilkan path, jangan langsung menjalankan banyak perintah instalasi.

Periksa dulu:

```bash
ls -la
```

dan:

```bash
echo $PATH
```

Kemudian cek status Gateway:

```bash
openclaw gateway status
```

Jika command `openclaw` juga tidak ditemukan, periksa dokumentasi/setup Windows Companion dan pastikan CLI memang tersedia di environment Gateway.

Alternatif resmi lainnya adalah memasang OpenClaw CLI native di Windows menggunakan PowerShell:

```powershell
iwr -useb https://openclaw.ai/install.ps1 | iex
```

**Catatan:** instalasi CLI native merupakan jalur instalasi yang berbeda dari CLI yang berada di WSL. Jangan memasang salinan kedua hanya untuk mengatasi masalah PATH sebelum memastikan kebutuhan setup.

---

## 15. Perintah Penting

| Kebutuhan | Command |
|---|---|
| Melihat distro WSL | `wsl --list --verbose` |
| Masuk OpenClawGateway | `wsl -d OpenClawGateway` |
| Mencari CLI | `which openclaw` |
| Cek versi | `openclaw --version` |
| Buka TUI | `openclaw tui` |
| TUI local mode | `openclaw tui --local` |
| Status Gateway | `openclaw gateway status` |
| Status OpenClaw | `openclaw status` |
| Status model | `openclaw models status` |
| Follow log | `openclaw logs --follow` |

---

## 16. Quick Start

Jika setup sudah benar, cukup:

### Windows CMD / PowerShell

```cmd
wsl -d OpenClawGateway
```

### Di WSL

```bash
openclaw tui
```

Selesai.

Workflow sehari-hari:

```text
Windows Terminal
      │
      ▼
wsl -d OpenClawGateway
      │
      ▼
openclaw tui
      │
      ▼
Chat dengan OpenClaw
```

---

## 17. Referensi Resmi

- OpenClaw Windows:
  https://docs.openclaw.ai/platforms/windows

- OpenClaw TUI:
  https://docs.openclaw.ai/web/tui

- OpenClaw TUI CLI reference:
  https://docs.openclaw.ai/id/cli/tui

- OpenClaw installation:
  https://docs.openclaw.ai/id/install

---

## Catatan Setup Lokal

Konfigurasi yang digunakan pada komputer ini:

```text
OS: Windows
Runtime: WSL2
Distro Gateway: OpenClawGateway
Gateway: 127.0.0.1:18789
Interface: OpenClaw TUI
```

> Jangan menyimpan API key atau Gateway token di file dokumentasi ini.
