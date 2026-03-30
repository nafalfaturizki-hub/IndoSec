# 🛡️ IndoSec CLI
### Dashboard Keamanan Situs Berbahasa Indonesia

```
██╗███╗   ██╗██████╗  ██████╗ ███████╗███████╗ ██████╗
██║████╗  ██║██╔══██╗██╔═══██╗██╔════╝██╔════╝██╔════╝
██║██╔██╗ ██║██║  ██║██║   ██║███████╗█████╗  ██║
██║██║╚██╗██║██║  ██║██║   ██║╚════██║██╔══╝  ██║
██║██║ ╚████║██████╔╝╚██████╔╝███████║███████╗╚██████╗
╚═╝╚═╝  ╚═══╝╚═════╝  ╚═════╝ ╚══════╝╚══════╝ ╚═════╝
                   CLI • v1.0.0 • Bahasa Indonesia
```

[![Build Status](https://github.com/username/indosec-rust/actions/workflows/ci.yml/badge.svg)](https://github.com/username/indosec-rust)
[![Rust Version](https://img.shields.io/badge/rust-1.75%2B-orange)](https://www.rust-lang.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-Termux%20%7C%20Linux-blue)](https://termux.dev)
[![Fitur](https://img.shields.io/badge/fitur-1000%2B-brightgreen)](docs/spesifikasi.md)

> **Tools keamanan siber pribadi berbahasa Indonesia untuk Termux & Linux.**
> Seluruh menu, perintah, dan output menggunakan Bahasa Indonesia.

---

## ✨ Fitur Unggulan

| Kategori | Fitur |
|---|---|
| 🔍 **Recon** | 100 shortcut – fingerprint server, TLS, DNS, WAF, crawl, OSINT pasif |
| 🔌 **Scanner** | 120 shortcut – port TCP/UDP, direktori, subdomain, vhost, CMS, GraphQL |
| 📋 **Form Testing** | 80 shortcut – XSS, SQLi (safe), CSRF, upload, rate limit |
| 🧠 **Analisis Konten** | 60 shortcut – NLP B.Indonesia, keyword sensitif, JS audit |
| 🌐 **Keamanan Jaringan** | 80 shortcut – TLS grading, cipher analysis, DoS resilience test |
| 🔐 **Kriptografi** | 60 shortcut – Hash, JWT, encoding, SSL grade, entropy check |
| 🕵️ **OSINT** | 80 shortcut – Wayback, breach check, GitHub leak, CVE lookup |
| 📊 **Laporan** | 80 shortcut – JSON/CSV/HTML/MD export, risk scoring, visualisasi ASCII |
| ⚙️ **Konfigurasi** | 80 shortcut – target management, proxy, auth, scheduling |
| 🤖 **Otomasi** | 60 shortcut – workflow, monitoring otomatis, notifikasi Telegram |
| 🔌 **API Testing** | 60 shortcut – REST, GraphQL, WebSocket, JWT, OAuth |
| 🔬 **Forensik** | 60 shortcut – log analysis, IOC extraction, STIX export |
| 📱 **Termux** | 60 shortcut – notifikasi Android, vibrate, GPS, clipboard |

**Total: 1.000 shortcut perintah Bahasa Indonesia**

---

## 🚀 Instalasi

### Persyaratan

| Komponen | Versi Minimum |
|---|---|
| Rust | 1.75.0 |
| Android | 8.0 (Termux) |
| RAM | 512 MB |
| Ruang Disk | 50 MB |

### Di Termux (Android)

```bash
# 1. Update paket Termux
pkg update && pkg upgrade -y

# 2. Install Rust
pkg install rust binutils openssl-dev pkg-config -y

# 3. Clone repositori
git clone https://github.com/username/indosec-rust.git
cd indosec-rust

# 4. Build
cargo build --release

# 5. Jalankan
./target/release/indosec
```

### Di Linux (x86_64 / ARM64)

```bash
# 1. Pastikan Rust terinstall
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.cargo/env

# 2. Clone & build
git clone https://github.com/username/indosec-rust.git
cd indosec-rust
cargo build --release

# 3. Install ke PATH (opsional)
sudo cp target/release/indosec /usr/local/bin/

# 4. Jalankan
indosec
```

### Build dengan Fitur Tertentu

```bash
# Hanya fitur dasar (lebih kecil)
cargo build --release --no-default-features --features "recon,scanner,form,report"

# Semua fitur
cargo build --release --features full

# Fitur spesifik
cargo build --release --features "recon,scanner,form,osint,report,termux"
```

### Pre-built Binary (Rilis)

```bash
# ARM64 Termux
wget https://github.com/username/indosec-rust/releases/latest/download/indosec-aarch64-termux
chmod +x indosec-aarch64-termux
./indosec-aarch64-termux

# Linux x86_64
wget https://github.com/username/indosec-rust/releases/latest/download/indosec-x86_64-linux
chmod +x indosec-x86_64-linux
./indosec-x86_64-linux
```

---

## 📖 Penggunaan

### Menu Utama

```
╔══════════════════════════════════════════════════╗
║           IndoSec CLI v1.0.0                     ║
║     Dashboard Keamanan Situs - B.Indonesia       ║
╠══════════════════════════════════════════════════╣
║  Target Aktif: https://contoh.co.id              ║
║  Waktu: Rabu, 15 Januari 2026 08:30 WIB          ║
╠══════════════════════════════════════════════════╣
║  [A] Recon & Informasi Situs                     ║
║  [B] Scan Port & Direktori                       ║
║  [C] Uji Form & Input                            ║
║  [D] Analisis Konten & NLP                       ║
║  [E] Keamanan Jaringan                           ║
║  [F] Kriptografi & TLS                           ║
║  [G] OSINT & Footprinting                        ║
║  [H] Laporan & Log                               ║
║  [I] Konfigurasi                                 ║
║  [J] CLI & UX                                    ║
║  [K] Otomasi                                     ║
║  [L] Analisis API                                ║
║  [M] Forensik Digital                            ║
║  [N] Termux Integration                          ║
║  [Q] Keluar                                      ║
╚══════════════════════════════════════════════════╝
indosec> 
```

### Contoh Penggunaan

#### Recon Cepat

```bash
# Set target
indosec> atur_target https://situs-saya.co.id

# Kumpulkan informasi dasar
indosec> informasi

# Hasil:
# ═══════════════════════════════════════
# INFORMASI SITUS: https://situs-saya.co.id
# ═══════════════════════════════════════
# Server      : nginx/1.18.0
# Framework   : Laravel 10.x
# TLS         : 1.3 ✅
# HSTS        : ✅ max-age=31536000
# CSP         : ⚠️  Tidak dikonfigurasi
# X-Frame     : ❌ Tidak ditemukan
# Skor Header : 45/100 ⚠️
```

#### Scan Port

```bash
indosec> pindai_port_penting

# Hasil:
# PORT 80   (HTTP)  : TERBUKA 🔓
# PORT 443  (HTTPS) : TERBUKA 🔓
# PORT 22   (SSH)   : TERTUTUP ✅
# PORT 3306 (MySQL) : TERTUTUP ✅
```

#### Uji Form

```bash
indosec> uji_form

# Hasil:
# Form #1: Login (/login) - POST
#   ├─ Field: username [text] ✅
#   ├─ Field: password [password] ✅
#   ├─ CSRF Token: ✅ Ditemukan
#   ├─ XSS Test: Tidak terpengaruh ✅
#   └─ SQLi Test: Tidak terpengaruh ✅
```

#### Laporan

```bash
# Export laporan JSON
indosec> ekspor_laporan_json

# Buat laporan teknis
indosec> buat_laporan_teknis

# Laporan disimpan di: laporan/laporan_teknis_2026-01-15.html
```

---

## 📚 Daftar Shortcut Utama

### Perintah Sistem

| Perintah | Fungsi |
|---|---|
| `atur_target <url>` | Set target aktif |
| `daftar_target` | Lihat semua target |
| `informasi` | Recon cepat target aktif |
| `pindai` | Scan port & direktori |
| `uji_form` | Uji semua form |
| `laporan` | Tampilkan laporan |
| `konfigurasi` | Buka pengaturan |
| `bantu` | Bantuan lengkap |
| `keluar` | Keluar dari CLI |

### Perintah Recon (Sebagian)

| Perintah | Fungsi |
|---|---|
| `cek_server` | Deteksi web server |
| `cek_cms` | Deteksi CMS |
| `analisis_csp` | Analisis CSP detail |
| `cek_ssl` | Info sertifikat SSL |
| `cek_dns` | Semua DNS records |
| `cek_whois` | Info WHOIS |
| `deteksi_waf` | Deteksi WAF |
| `cek_git_exposed` | Cek /.git/ terbuka |

### Perintah Scanner (Sebagian)

| Perintah | Fungsi |
|---|---|
| `pindai_port_cepat` | Top-100 port cepat |
| `pindai_direktori` | Directory brute force |
| `pindai_admin_panel` | Cari panel admin |
| `pindai_wordpress` | WordPress paths |
| `pindai_subdomain` | Subdomain discovery |
| `pindai_graphql` | GraphQL endpoint |
| `cek_trace_aktif` | Cek metode TRACE |

### Perintah Form (Sebagian)

| Perintah | Fungsi |
|---|---|
| `deteksi_form_login` | Cari form login |
| `uji_xss` | XSS reflection test |
| `uji_sqli` | SQLi test (safe) |
| `cek_csrf_token` | CSRF token check |
| `cek_rate_limit` | Rate limiting test |
| `skor_keamanan_form` | Skor keamanan form |

> **Lihat semua 1.000 shortcut di:** [`docs/spesifikasi.md`](docs/spesifikasi.md)

---

## 🗂️ Struktur Proyek

```
indosec-rust/
├── src/                    # Binary crate utama
│   ├── main.rs
│   └── cli/                # Dashboard & parser CLI
├── crates/                 # Library crates per modul
│   ├── indosec-core/       # Error, Config, Target
│   ├── indosec-recon/      # Modul A
│   ├── indosec-scanner/    # Modul B
│   ├── indosec-form/       # Modul C
│   ├── indosec-nlp/        # Modul D
│   ├── indosec-network/    # Modul E
│   ├── indosec-crypto/     # Modul F
│   ├── indosec-osint/      # Modul G
│   ├── indosec-report/     # Modul H
│   ├── indosec-automation/ # Modul K
│   ├── indosec-api/        # Modul L
│   ├── indosec-forensic/   # Modul M
│   └── indosec-termux/     # Modul N
├── config/
│   ├── targets.json        # Target list
│   ├── settings.toml       # Pengaturan global
│   └── payloads/           # Safe payloads
├── logs/                   # Hasil scan
└── laporan/                # Generated reports
```

---

## ⚙️ Konfigurasi

### targets.json

```json
[
  {
    "nama": "Website Pribadi",
    "url": "https://situs-saya.co.id",
    "catatan": "Target legal - milik sendiri",
    "izin_legal": true,
    "konfigurasi": {
      "timeout_ms": 5000,
      "ikuti_redirect": true,
      "verifikasi_ssl": true
    }
  },
  {
    "nama": "Lab Pengujian",
    "url": "https://lab.lokal:8080",
    "catatan": "Lingkungan lab lokal",
    "izin_legal": true,
    "konfigurasi": {
      "timeout_ms": 10000,
      "verifikasi_ssl": false
    }
  }
]
```

### settings.toml

```toml
[umum]
bahasa = "id"
warna = true
tema = "gelap"
konfirmasi_sebelum_aksi = true
log_level = "info"

[jaringan]
timeout_ms = 5000
maks_thread = 50
delay_antar_request_ms = 100
user_agent = "IndoSec/1.0.0 (Tools Keamanan Pribadi)"

[log]
direktori = "logs/"
rotasi_mb = 50
backup_otomatis = true

[notifikasi]
telegram_bot_token = ""
telegram_chat_id = ""
webhook_url = ""
```

---

## 🧪 Pengujian

```bash
# Semua unit test
cargo test

# Test modul tertentu
cargo test -p indosec-recon
cargo test -p indosec-scanner

# Test dengan output verbose
cargo test -- --nocapture

# Clippy lint check
cargo clippy -- -D warnings

# Format check
cargo fmt --check
```

---

## 🏗️ CI/CD

File `.github/workflows/ci.yml` menjalankan:

1. **Build** – `cargo build --release`
2. **Test** – `cargo test --all`
3. **Lint** – `cargo clippy -- -D warnings`
4. **Format** – `cargo fmt --check`
5. **Security Audit** – `cargo audit`
6. **Cross-compile** – Target ARM64 untuk Termux

---

## ⚖️ Catatan Legal

> **PENTING: Baca sebelum menggunakan.**

IndoSec CLI hanya boleh digunakan pada:

- ✅ Situs web yang **Anda miliki** sepenuhnya
- ✅ Situs web yang memberikan **izin tertulis eksplisit** untuk pengujian keamanan
- ✅ **Lingkungan lab / sandbox** yang Anda kontrol sendiri

**DILARANG:**
- ❌ Menguji situs web milik orang lain tanpa izin
- ❌ Menggunakan untuk tujuan destruktif atau ilegal
- ❌ Melanggar privasi pengguna situs yang diuji

Pelanggaran dapat dikenakan sanksi sesuai:
- **UU ITE No. 11 Tahun 2008** (diubah UU No. 19/2016)
- Pasal 30-32 tentang akses ilegal ke sistem komputer

---

## 🤝 Kontribusi

1. Fork repositori ini
2. Buat branch fitur baru: `git checkout -b fitur/nama-fitur`
3. Ikuti standar:
   - Semua string UI dalam Bahasa Indonesia
   - Tidak ada `unwrap()` di production code (gunakan `?`)
   - Dokumentasi Rustdoc untuk setiap fungsi publik
   - Test unit untuk setiap modul baru
4. Commit: `git commit -m "tambah: fitur_baru"`
5. Push & buat Pull Request

### Standar Penamaan

```rust
// Fungsi: snake_case Bahasa Indonesia
fn kumpulkan_informasi() -> Result<InfoSitus, IndoSecError> { ... }

// Struct: PascalCase
struct HasilRecon { ... }

// Enum: PascalCase dengan varian bahasa Indonesia
enum TingkatKeparahan {
    Kritis,
    Tinggi,
    Sedang,
    Rendah,
    Informasi,
}

// Konstanta: SCREAMING_SNAKE_CASE
const MAKS_REDIRECT: u8 = 10;
```

---

## 🐛 Laporan Bug

Buka Issue di GitHub dengan menyertakan:

1. Versi IndoSec (`info_versi`)
2. Platform (Termux / Linux)
3. Perintah yang dijalankan
4. Output error lengkap
5. Langkah reproduksi

---

## 📋 Changelog

### v1.0.0 (2026)
- 1.000 shortcut perintah Bahasa Indonesia
- 14 modul terpisah sebagai Cargo workspace
- Termux integration lengkap
- Otomasi workflow & monitoring
- Forensik digital & IOC extraction
- Laporan multi-format (JSON/CSV/HTML/MD)

### v0.1.0 (MVP)
- Dashboard CLI Bahasa Indonesia
- Recon dasar, port scan, directory scan
- Form detection & testing
- Logging & JSON export

---

## 📄 Lisensi

MIT License – Copyright © 2026

Permission is hereby granted, free of charge, to any person obtaining a copy of this software, to deal in the Software without restriction.

**Catatan:** Lisensi ini berlaku untuk kode sumber. Penggunaan tools ini tetap harus mematuhi hukum yang berlaku.

---

<div align="center">

Dibuat dengan ❤️ untuk komunitas keamanan siber Indonesia

**[Dokumentasi](docs/)** • **[Spesifikasi](docs/spesifikasi.md)** • **[Proposal](docs/proposal.md)** • **[Issues](https://github.com/username/indosec-rust/issues)**

</div>
