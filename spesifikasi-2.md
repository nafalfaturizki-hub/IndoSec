# SPESIFIKASI TEKNIS INDOSEC CLI v1.0.0
### Dashboard Keamanan Situs Berbahasa Indonesia

```
Copyright © 2026 IndoSec Project
Lisensi: MIT / Personal Use
Platform: Termux (Android) + Linux
Bahasa Implementasi: Rust (Edition 2021)
```

---

## DAFTAR ISI

1. [Gambaran Umum Sistem](#1-gambaran-umum-sistem)
2. [Arsitektur & Struktur Proyek](#2-arsitektur--struktur-proyek)
3. [Modul A – Recon & Informasi Situs](#3-modul-a--recon--informasi-situs)
4. [Modul B – Scan Port & Direktori](#4-modul-b--scan-port--direktori)
5. [Modul C – Uji Form & Input](#5-modul-c--uji-form--input)
6. [Modul D – Analisis Konten & NLP](#6-modul-d--analisis-konten--nlp)
7. [Modul E – Keamanan Jaringan](#7-modul-e--keamanan-jaringan)
8. [Modul F – Analisis Kriptografi & TLS](#8-modul-f--analisis-kriptografi--tls)
9. [Modul G – OSINT & Footprinting](#9-modul-g--osint--footprinting)
10. [Modul H – Logging & Reporting](#10-modul-h--logging--reporting)
11. [Modul I – Konfigurasi & Sistem](#11-modul-i--konfigurasi--sistem)
12. [Modul J – CLI Interaktif & UX](#12-modul-j--cli-interaktif--ux)
13. [Modul K – Otomasi & Scripting](#13-modul-k--otomasi--scripting)
14. [Modul L – Analisis API & REST](#14-modul-l--analisis-api--rest)
15. [Modul M – Forensik Digital](#15-modul-m--forensik-digital)
16. [Modul N – Termux Integration](#16-modul-n--termux-integration)
17. [Tabel Shortcut Lengkap (1000 Fitur)](#17-tabel-shortcut-lengkap-1000-fitur)
18. [Spesifikasi Teknis Rust](#18-spesifikasi-teknis-rust)
19. [Skema Data & Format Log](#19-skema-data--format-log)
20. [Roadmap Pengembangan](#20-roadmap-pengembangan)

---

## 1. GAMBARAN UMUM SISTEM

### 1.1 Identitas Proyek

| Atribut | Nilai |
|---|---|
| Nama | IndoSec CLI |
| Versi | 1.0.0 |
| Target Platform | Termux (Android 8+), Linux x86_64, Linux ARM64 |
| Bahasa Implementasi | Rust 2021 Edition |
| Minimum Rust | 1.75.0 |
| Binary Size Target | < 8 MB (stripped, UPX compressed) |
| Runtime RAM | < 32 MB idle, < 128 MB aktif |
| Lisensi | MIT / Personal Use |

### 1.2 Filosofi Desain

IndoSec CLI dibangun di atas lima prinsip utama:

1. **Bahasa Pertama (Language-First):** Seluruh antarmuka, pesan error, dan dokumentasi dalam Bahasa Indonesia. Tidak ada bahasa Inggris yang terekspos ke pengguna.
2. **Modularitas Ketat:** Setiap modul dapat dikompilasi secara independen via Cargo features.
3. **Zero External Runtime:** Tidak bergantung pada Python, Node.js, atau runtime eksternal lain saat execution.
4. **Fail Fast:** Setiap operasi yang gagal menghasilkan pesan diagnostik eksplisit dalam B.Indonesia.
5. **Etika Bawaan:** Guard clause legal aktif di setiap modul yang melakukan pengujian aktif.

### 1.3 Batasan Legal

IndoSec CLI hanya digunakan pada:
- Situs web yang **dimiliki sendiri** oleh pengguna
- Situs web yang **memberikan izin eksplisit tertulis** untuk pengujian
- Lingkungan lab / sandbox yang dikontrol pengguna

Penggunaan tanpa izin melanggar UU ITE No. 11 Tahun 2008 dan perubahannya (UU No. 19/2016), serta Pasal 30–32 yang mengatur akses ilegal sistem komputer.

---

## 2. ARSITEKTUR & STRUKTUR PROYEK

### 2.1 Workspace Rust

```
indosec-rust/                          # Workspace root
├── Cargo.toml                         # Workspace manifest
├── Cargo.lock
├── README.md
├── LICENSE
├── .github/
│   └── workflows/
│       ├── ci.yml                     # CI: build + test + clippy
│       └── release.yml                # Cross-compile release builds
│
├── crates/
│   ├── indosec-core/                  # Library crate inti
│   │   ├── src/
│   │   │   ├── lib.rs
│   │   │   ├── error.rs               # IndoSecError enum
│   │   │   ├── config.rs              # Config struct (serde)
│   │   │   ├── target.rs              # Target struct
│   │   │   └── bahasa.rs              # i18n strings B.Indonesia
│   │   └── Cargo.toml
│   │
│   ├── indosec-recon/                 # Modul A
│   ├── indosec-scanner/               # Modul B
│   ├── indosec-form/                  # Modul C
│   ├── indosec-nlp/                   # Modul D
│   ├── indosec-network/               # Modul E
│   ├── indosec-crypto/                # Modul F
│   ├── indosec-osint/                 # Modul G
│   ├── indosec-report/                # Modul H
│   ├── indosec-automation/            # Modul K
│   ├── indosec-api/                   # Modul L
│   ├── indosec-forensic/              # Modul M
│   └── indosec-termux/                # Modul N
│
├── src/                               # Binary crate utama
│   ├── main.rs
│   ├── cli/
│   │   ├── mod.rs
│   │   ├── dashboard.rs               # Menu utama interaktif
│   │   ├── parser.rs                  # Shortcut → fungsi mapping
│   │   ├── autocomplete.rs            # Tab completion B.Indonesia
│   │   ├── highlight.rs               # Colored terminal output
│   │   └── progress.rs                # Progress bar & spinner
│   └── dispatch.rs                    # Command dispatcher
│
├── config/
│   ├── targets.json                   # Daftar target
│   ├── settings.toml                  # Pengaturan global
│   ├── payloads/
│   │   ├── xss_safe.txt               # Safe XSS payloads
│   │   ├── sqli_safe.txt              # Safe SQLi payloads
│   │   └── direktori_umum.txt         # Wordlist direktori B.Indonesia
│   └── wordlists/
│       ├── subdomain_id.txt           # Subdomain umum Indonesia
│       └── endpoint_id.txt            # Endpoint API umum
│
├── logs/
│   ├── hasil_scan.log
│   ├── hasil_recon.log
│   ├── hasil_form.log
│   └── arsip/                         # Rotated logs
│
└── laporan/
    ├── template_html.hbs              # Handlebars HTML template
    └── template_json.json             # JSON schema laporan
```

### 2.2 Dependency Graph

```toml
# Cargo.toml (workspace root)
[workspace]
members = [
    ".", "crates/indosec-core", "crates/indosec-recon",
    "crates/indosec-scanner", "crates/indosec-form",
    "crates/indosec-nlp", "crates/indosec-network",
    "crates/indosec-crypto", "crates/indosec-osint",
    "crates/indosec-report", "crates/indosec-automation",
    "crates/indosec-api", "crates/indosec-forensic",
    "crates/indosec-termux",
]

[workspace.dependencies]
tokio         = { version = "1.35", features = ["full"] }
reqwest       = { version = "0.11", features = ["json", "rustls-tls"] }
serde         = { version = "1", features = ["derive"] }
serde_json    = "1"
scraper       = "0.18"          # HTML parsing
url           = "2.5"
clap          = { version = "4", features = ["derive"] }
colored       = "2"             # Terminal warna
indicatif     = "0.17"          # Progress bar
dialoguer     = "0.11"          # Prompt interaktif
crossterm     = "0.27"          # Terminal control
anyhow        = "1"
thiserror     = "1"
log           = "0.4"
env_logger    = "0.11"
chrono        = { version = "0.4", features = ["serde"] }
regex         = "1.10"
trust-dns-resolver = "0.23"    # DNS resolution
native-tls    = "0.2"
csv           = "1.3"
handlebars    = "5"             # Template laporan
rusqlite      = "0.31"          # Lokal database log
```

### 2.3 Cargo Features

```toml
[features]
default = ["recon", "scanner", "form", "report", "termux"]
full    = ["default", "nlp", "network", "crypto", "osint", "automation", "api", "forensic"]
recon   = ["crates/indosec-recon"]
scanner = ["crates/indosec-scanner"]
form    = ["crates/indosec-form"]
nlp     = ["crates/indosec-nlp"]
network = ["crates/indosec-network"]
crypto  = ["crates/indosec-crypto"]
osint   = ["crates/indosec-osint"]
report  = ["crates/indosec-report"]
automation = ["crates/indosec-automation"]
api     = ["crates/indosec-api"]
forensic = ["crates/indosec-forensic"]
termux  = ["crates/indosec-termux"]
```

---

## 3. MODUL A – RECON & INFORMASI SITUS

**Crate:** `indosec-recon`
**File utama:** `src/recon.rs`

### 3.1 Submodul

#### A1 – Pengumpulan Informasi Dasar
- Fetch halaman utama (GET + HEAD)
- Deteksi server (header `Server`)
- Deteksi framework (X-Powered-By, generator meta)
- Deteksi CMS: WordPress, Joomla, Drupal, Laravel, CodeIgniter
- Deteksi teknologi frontend: React, Vue, Angular, jQuery versi
- Ekstraksi semua meta tag
- Parsing `<title>` dan deskripsi
- Deteksi bahasa halaman (`<html lang="">`)
- Waktu respons (TTFB)
- Ukuran konten (Content-Length vs actual)
- Deteksi redirect chain (301/302/307)
- Deteksi canonical URL
- Deteksi Open Graph tags
- Deteksi robots.txt
- Parsing sitemap.xml

#### A2 – Analisis Link & Struktur
- Ekstraksi semua URL internal
- Ekstraksi semua URL eksternal
- Ekstraksi semua resource (img, script, link, video, audio)
- Deteksi broken links (404 check)
- Pemetaan hierarki halaman
- Deteksi form action URL
- Deteksi link dengan `target="_blank"` tanpa `noopener`
- Deteksi mixed content (HTTP dalam HTTPS)
- Deteksi resource dari CDN pihak ketiga
- Hitung rasio link internal vs eksternal

#### A3 – Analisis HTTP Headers Keamanan
- Cek `Strict-Transport-Security` (HSTS)
- Cek `Content-Security-Policy` (CSP) dan analisis direktif
- Cek `X-Frame-Options`
- Cek `X-Content-Type-Options`
- Cek `Referrer-Policy`
- Cek `Permissions-Policy`
- Cek `X-XSS-Protection`
- Cek `Cross-Origin-Opener-Policy`
- Cek `Cross-Origin-Resource-Policy`
- Cek `Cross-Origin-Embedder-Policy`
- Scoring keamanan header (0–100)
- Rekomendasi perbaikan header dalam B.Indonesia

#### A4 – Analisis TLS/SSL
- Versi TLS (1.0, 1.1, 1.2, 1.3)
- Cipher suite yang digunakan
- Tanggal kadaluarsa sertifikat
- Issuer CA
- Subject Alternative Names (SAN)
- Deteksi self-signed certificate
- Deteksi wildcard certificate
- OCSP stapling check
- Certificate Transparency log check
- Hitung hari hingga kadaluarsa

### 3.2 Shortcut Perintah Modul A (100 shortcut)

| # | Perintah Indonesia | Fungsi Rust | Keterangan |
|---|---|---|---|
| A001 | `informasi` | `recon::dasar()` | Kumpulkan semua informasi dasar |
| A002 | `kumpulkan_info` | `recon::kumpulkan()` | Alias informasi |
| A003 | `cek_server` | `recon::cek_server()` | Deteksi web server |
| A004 | `cek_framework` | `recon::cek_framework()` | Deteksi framework/CMS |
| A005 | `cek_cms` | `recon::cek_cms()` | Khusus deteksi CMS |
| A006 | `cek_teknologi` | `recon::cek_teknologi()` | Stack teknologi lengkap |
| A007 | `analisis_meta` | `recon::analisis_meta()` | Semua meta tag |
| A008 | `cek_judul` | `recon::cek_judul()` | Title & deskripsi halaman |
| A009 | `cek_bahasa` | `recon::cek_bahasa()` | Bahasa halaman |
| A010 | `ukur_waktu_respons` | `recon::waktu_respons()` | TTFB measurement |
| A011 | `cek_ukuran` | `recon::ukuran_konten()` | Ukuran halaman |
| A012 | `cek_redirect` | `recon::redirect_chain()` | Redirect chain analysis |
| A013 | `cek_canonical` | `recon::cek_canonical()` | Canonical URL |
| A014 | `cek_opengraph` | `recon::open_graph()` | Open Graph tags |
| A015 | `cek_robots` | `recon::robots_txt()` | Analisis robots.txt |
| A016 | `cek_sitemap` | `recon::sitemap()` | Parse sitemap.xml |
| A017 | `ekstrak_link` | `recon::ekstrak_link()` | Semua URL internal |
| A018 | `ekstrak_link_luar` | `recon::link_eksternal()` | Semua URL eksternal |
| A019 | `ekstrak_resource` | `recon::resource()` | Semua resource halaman |
| A020 | `cek_link_rusak` | `recon::broken_links()` | Cek 404 pada semua link |
| A021 | `peta_halaman` | `recon::peta_struktur()` | Hierarki halaman |
| A022 | `cek_form_action` | `recon::form_action()` | URL action form |
| A023 | `cek_target_blank` | `recon::target_blank()` | Link tanpa noopener |
| A024 | `cek_mixed_content` | `recon::mixed_content()` | HTTP dalam HTTPS |
| A025 | `cek_cdn` | `recon::cdn_pihak_ketiga()` | Resource dari CDN asing |
| A026 | `rasio_link` | `recon::rasio_link()` | Rasio internal vs eksternal |
| A027 | `cek_hsts` | `recon::cek_hsts()` | HSTS header |
| A028 | `cek_csp` | `recon::cek_csp()` | Content-Security-Policy |
| A029 | `analisis_csp` | `recon::analisis_csp()` | Direktif CSP detail |
| A030 | `cek_xframe` | `recon::x_frame()` | X-Frame-Options |
| A031 | `cek_xcontent` | `recon::x_content_type()` | X-Content-Type-Options |
| A032 | `cek_referrer` | `recon::referrer_policy()` | Referrer-Policy |
| A033 | `cek_permissions` | `recon::permissions_policy()` | Permissions-Policy |
| A034 | `cek_xss_header` | `recon::x_xss_protection()` | X-XSS-Protection |
| A035 | `cek_coop` | `recon::cross_origin_opener()` | COOP header |
| A036 | `cek_corp` | `recon::cross_origin_resource()` | CORP header |
| A037 | `cek_coep` | `recon::cross_origin_embedder()` | COEP header |
| A038 | `skor_keamanan_header` | `recon::skor_header()` | Scoring 0-100 |
| A039 | `rekomendasi_header` | `recon::rekomendasi_header()` | Saran perbaikan |
| A040 | `cek_tls` | `recon::versi_tls()` | Versi TLS aktif |
| A041 | `cek_cipher` | `recon::cipher_suite()` | Cipher yang dipakai |
| A042 | `cek_sertifikat` | `recon::info_sertifikat()` | Info lengkap sertifikat |
| A043 | `cek_kadaluarsa_ssl` | `recon::ssl_kadaluarsa()` | Tanggal expired cert |
| A044 | `cek_issuer` | `recon::ssl_issuer()` | Issuer CA |
| A045 | `cek_san` | `recon::ssl_san()` | Subject Alt Names |
| A046 | `cek_self_signed` | `recon::self_signed()` | Deteksi self-signed |
| A047 | `cek_wildcard` | `recon::wildcard_cert()` | Wildcard certificate |
| A048 | `cek_ocsp` | `recon::ocsp_stapling()` | OCSP stapling |
| A049 | `cek_ct_log` | `recon::ct_log()` | Certificate Transparency |
| A050 | `hitung_hari_ssl` | `recon::hari_ssl()` | Hari hingga expired |
| A051 | `crawl_situs` | `recon::crawl()` | Crawl seluruh halaman |
| A052 | `crawl_dalam` | `recon::crawl_depth(n)` | Crawl dengan kedalaman n |
| A053 | `crawl_lambat` | `recon::crawl_lambat()` | Crawl dengan delay |
| A054 | `crawl_cepat` | `recon::crawl_cepat()` | Crawl paralel |
| A055 | `buat_sitemap` | `recon::buat_sitemap()` | Generate sitemap target |
| A056 | `cek_favicon` | `recon::favicon()` | Deteksi & hash favicon |
| A057 | `cek_apple_touch` | `recon::apple_touch()` | Apple touch icon |
| A058 | `cek_manifest` | `recon::web_manifest()` | PWA manifest.json |
| A059 | `cek_service_worker` | `recon::service_worker()` | Service worker |
| A060 | `cek_cookie` | `recon::analisis_cookie()` | Flag cookie (Secure, HttpOnly, SameSite) |
| A061 | `cek_cors` | `recon::cors_policy()` | CORS configuration |
| A062 | `cek_dns` | `recon::dns_records()` | Semua DNS records |
| A063 | `cek_a_record` | `recon::dns_a()` | A record |
| A064 | `cek_mx_record` | `recon::dns_mx()` | MX record |
| A065 | `cek_txt_record` | `recon::dns_txt()` | TXT record (SPF, DKIM, DMARC) |
| A066 | `cek_spf` | `recon::spf_record()` | SPF validation |
| A067 | `cek_dkim` | `recon::dkim_record()` | DKIM record |
| A068 | `cek_dmarc` | `recon::dmarc_record()` | DMARC policy |
| A069 | `cek_ns` | `recon::nameserver()` | Nameserver |
| A070 | `cek_whois` | `recon::whois_info()` | WHOIS data |
| A071 | `cek_registrar` | `recon::registrar()` | Domain registrar |
| A072 | `cek_asn` | `recon::asn_info()` | ASN & IP range |
| A073 | `cek_ip` | `recon::ip_address()` | Resolve IP target |
| A074 | `cek_geolokasi` | `recon::geolokasi_ip()` | Geolokasi IP |
| A075 | `cek_hosting` | `recon::info_hosting()` | Deteksi provider hosting |
| A076 | `deteksi_waf` | `recon::waf_detection()` | Deteksi Web Application Firewall |
| A077 | `deteksi_cdn_jenis` | `recon::cdn_jenis()` | Jenis CDN (Cloudflare, Akamai, dll) |
| A078 | `cek_load_balancer` | `recon::load_balancer()` | Deteksi load balancer |
| A079 | `cek_email_situs` | `recon::email_situs()` | Scraping email publik |
| A080 | `cek_nomor_telp` | `recon::telp_situs()` | Nomor telepon di halaman |
| A081 | `cek_media_sosial` | `recon::sosial_media()` | Link media sosial |
| A082 | `analisis_keyword` | `recon::keyword_sensitif()` | Kata kunci sensitif |
| A083 | `cek_komentar_html` | `recon::komentar_html()` | Komentar tersembunyi di HTML |
| A084 | `cek_debug_info` | `recon::debug_info()` | Info debug bocor |
| A085 | `cek_path_sensitif` | `recon::path_sensitif()` | Path file sensitif |
| A086 | `cek_backup_file` | `recon::backup_file()` | File backup (.bak, .old, .zip) |
| A087 | `cek_git_exposed` | `recon::git_exposed()` | /.git/ exposed |
| A088 | `cek_env_exposed` | `recon::env_exposed()` | /.env exposed |
| A089 | `cek_config_exposed` | `recon::config_exposed()` | Config file exposed |
| A090 | `cek_log_exposed` | `recon::log_exposed()` | Log file exposed |
| A091 | `fingerprint_penuh` | `recon::fingerprint_lengkap()` | Full fingerprint semua aspek |
| A092 | `bandingkan_target` | `recon::bandingkan(url1, url2)` | Bandingkan dua target |
| A093 | `simpan_info` | `recon::simpan()` | Simpan ke log |
| A094 | `export_json_info` | `recon::export_json()` | Export JSON |
| A095 | `export_csv_info` | `recon::export_csv()` | Export CSV |
| A096 | `export_xml_info` | `recon::export_xml()` | Export XML |
| A097 | `ringkasan_info` | `recon::ringkasan()` | Ringkasan eksekutif |
| A098 | `cetak_info` | `recon::cetak()` | Print dengan highlight warna |
| A099 | `ulangi_info` | `recon::refresh()` | Re-run recon |
| A100 | `bantu_info` | `recon::bantuan()` | Help modul recon |

---

## 4. MODUL B – SCAN PORT & DIREKTORI

**Crate:** `indosec-scanner`

### 4.1 Submodul

#### B1 – Port Scanning
- TCP SYN scan (async tokio)
- TCP Connect scan
- UDP scan
- Range scan (misal: 1-1024)
- Custom port list
- Filter port penting (21, 22, 23, 25, 53, 80, 110, 143, 443, 445, 3306, 5432, 6379, 8080, 8443, 27017)
- Banner grabbing per port
- Service detection (HTTP, FTP, SSH, SMTP fingerprint)
- Timeout konfigurasi
- Concurrent scan dengan thread pool

#### B2 – Direktori & Endpoint Scanning
- Wordlist direktori bahasa Indonesia
- Recursive directory scan
- Response code filtering
- Content-length filtering
- Custom extension (.php, .asp, .jsp, .env, .bak)
- Parallel HTTP requests
- Rate limiting (req/detik)
- Wildcard response detection
- Redirect following option
- Screenshot halaman yang ditemukan (teks dump)

### 4.2 Shortcut Perintah Modul B (120 shortcut)

| # | Perintah Indonesia | Fungsi Rust | Keterangan |
|---|---|---|---|
| B001 | `pindai` | `scanner::pindai_semua()` | Scan port & direktori lengkap |
| B002 | `pindai_port` | `scanner::port::scan()` | Scan semua port umum |
| B003 | `pindai_port_tcp` | `scanner::port::tcp()` | Scan TCP saja |
| B004 | `pindai_port_udp` | `scanner::port::udp()` | Scan UDP saja |
| B005 | `pindai_port_cepat` | `scanner::port::cepat()` | Top-100 ports |
| B006 | `pindai_port_lambat` | `scanner::port::lambat()` | Stealth scan |
| B007 | `pindai_port_range` | `scanner::port::range(a,b)` | Range port a sampai b |
| B008 | `pindai_port_custom` | `scanner::port::custom(list)` | Port list manual |
| B009 | `pindai_port_penting` | `scanner::port::penting()` | Filter port kritikal |
| B010 | `pindai_port_web` | `scanner::port::web()` | 80,443,8080,8443 |
| B011 | `pindai_port_database` | `scanner::port::database()` | 3306,5432,6379,27017 |
| B012 | `pindai_port_remote` | `scanner::port::remote()` | 22,23,3389,5900 |
| B013 | `pindai_port_email` | `scanner::port::email()` | 25,110,143,587,993 |
| B014 | `pindai_port_file` | `scanner::port::file()` | 21,445,139,2049 |
| B015 | `ambil_banner` | `scanner::port::banner(port)` | Banner grabbing port |
| B016 | `deteksi_layanan` | `scanner::port::layanan()` | Service fingerprint |
| B017 | `atur_timeout_scan` | `scanner::port::set_timeout(ms)` | Set timeout ms |
| B018 | `atur_thread_scan` | `scanner::port::set_threads(n)` | Set concurrent thread |
| B019 | `highlight_port` | `scanner::port::highlight()` | Highlight port terbuka |
| B020 | `simpan_scan_port` | `scanner::port::simpan()` | Simpan ke log |
| B021 | `export_json_port` | `scanner::port::export_json()` | Export JSON |
| B022 | `export_csv_port` | `scanner::port::export_csv()` | Export CSV |
| B023 | `ringkasan_port` | `scanner::port::ringkasan()` | Summary scan port |
| B024 | `pindai_direktori` | `scanner::dir::scan()` | Directory scan |
| B025 | `pindai_dir_wordlist` | `scanner::dir::wordlist(path)` | Wordlist custom |
| B026 | `pindai_dir_rekursif` | `scanner::dir::rekursif()` | Recursive scan |
| B027 | `pindai_dir_cepat` | `scanner::dir::cepat()` | High concurrency |
| B028 | `pindai_dir_lambat` | `scanner::dir::lambat()` | Low rate scan |
| B029 | `pindai_dir_ekstensi` | `scanner::dir::ekstensi(list)` | Filter ekstensi |
| B030 | `pindai_php` | `scanner::dir::php()` | Scan *.php |
| B031 | `pindai_asp` | `scanner::dir::asp()` | Scan *.asp/*.aspx |
| B032 | `pindai_jsp` | `scanner::dir::jsp()` | Scan *.jsp |
| B033 | `pindai_env_file` | `scanner::dir::env()` | Scan .env files |
| B034 | `pindai_backup` | `scanner::dir::backup()` | Scan .bak,.old,.zip |
| B035 | `pindai_config_file` | `scanner::dir::config()` | Config files |
| B036 | `pindai_admin_panel` | `scanner::dir::admin()` | Admin panel paths |
| B037 | `pindai_api_endpoint` | `scanner::dir::api()` | /api/* paths |
| B038 | `pindai_upload_dir` | `scanner::dir::upload()` | Upload directories |
| B039 | `filter_200` | `scanner::dir::filter_200()` | Hanya tampilkan 200 OK |
| B040 | `filter_301` | `scanner::dir::filter_301()` | Redirect paths |
| B041 | `filter_403` | `scanner::dir::filter_403()` | Forbidden (menarik) |
| B042 | `filter_500` | `scanner::dir::filter_500()` | Server error |
| B043 | `filter_status_custom` | `scanner::dir::filter_status(code)` | Status code custom |
| B044 | `filter_ukuran_min` | `scanner::dir::ukuran_min(bytes)` | Min content-length |
| B045 | `filter_ukuran_max` | `scanner::dir::ukuran_max(bytes)` | Max content-length |
| B046 | `abaikan_wildcard` | `scanner::dir::no_wildcard()` | Skip wildcard response |
| B047 | `ikuti_redirect` | `scanner::dir::follow_redirect()` | Follow 301/302 |
| B048 | `atur_req_per_detik` | `scanner::dir::rate(n)` | Rate limiting |
| B049 | `atur_user_agent` | `scanner::dir::user_agent(ua)` | Custom User-Agent |
| B050 | `atur_header_scan` | `scanner::dir::header(k,v)` | Tambah header custom |
| B051 | `dump_konten` | `scanner::dir::dump(path)` | Dump isi halaman |
| B052 | `simpan_scan_dir` | `scanner::dir::simpan()` | Simpan ke log |
| B053 | `export_json_dir` | `scanner::dir::export_json()` | Export JSON |
| B054 | `export_csv_dir` | `scanner::dir::export_csv()` | Export CSV |
| B055 | `ringkasan_dir` | `scanner::dir::ringkasan()` | Summary dir scan |
| B056 | `pindai_multi_target` | `scanner::multi::scan()` | Scan banyak target |
| B057 | `antrian_target` | `scanner::multi::antrian()` | Lihat antrian target |
| B058 | `tambah_antrian` | `scanner::multi::tambah(url)` | Tambah ke antrian |
| B059 | `hapus_antrian` | `scanner::multi::hapus(i)` | Hapus dari antrian |
| B060 | `kosongkan_antrian` | `scanner::multi::kosongkan()` | Kosongkan antrian |
| B061 | `ekspor_antrian` | `scanner::multi::ekspor()` | Export daftar antrian |
| B062 | `impor_antrian` | `scanner::multi::impor(file)` | Import dari file |
| B063 | `jeda_scan` | `scanner::kontrol::jeda()` | Pause scan |
| B064 | `lanjut_scan` | `scanner::kontrol::lanjut()` | Resume scan |
| B065 | `hentikan_scan` | `scanner::kontrol::hentikan()` | Stop scan |
| B066 | `status_scan` | `scanner::kontrol::status()` | Status scan aktif |
| B067 | `persentase_scan` | `scanner::kontrol::persen()` | Progress persen |
| B068 | `estimasi_waktu` | `scanner::kontrol::eta()` | ETA scan selesai |
| B069 | `pindai_subdomain` | `scanner::subdomain::scan()` | Subdomain bruteforce |
| B070 | `pindai_subdomain_wordlist` | `scanner::subdomain::wordlist(f)` | Subdomain wordlist |
| B071 | `pindai_subdomain_cepat` | `scanner::subdomain::cepat()` | Fast subdomain |
| B072 | `validasi_subdomain` | `scanner::subdomain::validasi()` | Verify subdomain aktif |
| B073 | `cek_vhost` | `scanner::vhost::scan()` | Virtual host scan |
| B074 | `pindai_parameter` | `scanner::param::scan()` | URL parameter scan |
| B075 | `temukan_parameter` | `scanner::param::temukan()` | Parameter discovery |
| B076 | `fuzz_parameter` | `scanner::param::fuzz(wordlist)` | Parameter fuzzing |
| B077 | `pindai_header` | `scanner::header::scan()` | Scan semua header |
| B078 | `pindai_method` | `scanner::method::scan()` | HTTP method test (GET,POST,PUT,DELETE,PATCH) |
| B079 | `cek_put_aktif` | `scanner::method::put()` | Cek apakah PUT diizinkan |
| B080 | `cek_delete_aktif` | `scanner::method::delete()` | Cek DELETE |
| B081 | `cek_trace_aktif` | `scanner::method::trace()` | Cek TRACE (berbahaya) |
| B082 | `cek_options` | `scanner::method::options()` | Cek OPTIONS response |
| B083 | `pindai_websocket` | `scanner::ws::scan()` | WebSocket endpoint |
| B084 | `pindai_graphql` | `scanner::gql::scan()` | GraphQL endpoint |
| B085 | `introspeksi_graphql` | `scanner::gql::introspect()` | GraphQL introspection |
| B086 | `pindai_swagger` | `scanner::swagger::scan()` | Swagger/OpenAPI UI |
| B087 | `pindai_jenkins` | `scanner::cms::jenkins()` | Jenkins endpoint |
| B088 | `pindai_phpmyadmin` | `scanner::cms::phpmyadmin()` | phpMyAdmin |
| B089 | `pindai_wordpress` | `scanner::cms::wordpress()` | WordPress paths |
| B090 | `pindai_joomla` | `scanner::cms::joomla()` | Joomla paths |
| B091 | `pindai_drupal` | `scanner::cms::drupal()` | Drupal paths |
| B092 | `pindai_laravel` | `scanner::cms::laravel()` | Laravel debug/paths |
| B093 | `pindai_django` | `scanner::cms::django()` | Django admin/paths |
| B094 | `pindai_flask` | `scanner::cms::flask()` | Flask debug mode |
| B095 | `pindai_spring` | `scanner::cms::spring()` | Spring Actuator |
| B096 | `pindai_actuator` | `scanner::cms::actuator()` | /actuator/* |
| B097 | `cek_server_status` | `scanner::cms::server_status()` | /server-status Apache |
| B098 | `cek_nginx_status` | `scanner::cms::nginx_status()` | /nginx_status |
| B099 | `ringkasan_pindai` | `scanner::ringkasan()` | Ringkasan semua scan |
| B100 | `bantu_pindai` | `scanner::bantuan()` | Help modul scanner |
| B101 | `pindai_s3` | `scanner::cloud::s3()` | AWS S3 bucket |
| B102 | `pindai_gcs` | `scanner::cloud::gcs()` | Google Cloud Storage |
| B103 | `pindai_azure_blob` | `scanner::cloud::azure()` | Azure Blob Storage |
| B104 | `cek_cors_wildcard` | `scanner::cors::wildcard()` | CORS * misconfiguration |
| B105 | `pindai_path_traversal` | `scanner::vuln::path_traversal()` | Path traversal test |
| B106 | `pindai_ssrf` | `scanner::vuln::ssrf()` | SSRF test |
| B107 | `pindai_open_redirect` | `scanner::vuln::open_redirect()` | Open redirect test |
| B108 | `pindai_crlf` | `scanner::vuln::crlf()` | CRLF injection |
| B109 | `pindai_host_injection` | `scanner::vuln::host_header()` | Host header injection |
| B110 | `pindai_clickjacking` | `scanner::vuln::clickjacking()` | Clickjacking check |
| B111 | `pindai_cache_poisoning` | `scanner::vuln::cache_poison()` | Cache poisoning |
| B112 | `pindai_request_smuggling` | `scanner::vuln::smuggling()` | Request smuggling |
| B113 | `pindai_blueprint` | `scanner::blueprint::scan()` | Blueprint/sitemap scan |
| B114 | `pindai_kompresi` | `scanner::kompresi::scan()` | Compression test (gzip/br) |
| B115 | `cek_waf_bypass` | `scanner::waf::bypass_test()` | WAF bypass attemps |
| B116 | `ukur_bandwidth` | `scanner::perf::bandwidth()` | Estimasi bandwidth respons |
| B117 | `cek_cache_header` | `scanner::cache::header()` | Cache-Control analysis |
| B118 | `cek_etag` | `scanner::cache::etag()` | ETag fingerprint |
| B119 | `skor_pindai` | `scanner::skor()` | Scoring hasil scan |
| B120 | `laporan_pindai` | `scanner::laporan()` | Laporan lengkap scan |

---

## 5. MODUL C – UJI FORM & INPUT

**Crate:** `indosec-form`

### 5.1 Submodul

#### C1 – Deteksi Form
- Deteksi semua form (GET/POST)
- Analisis field: name, type, placeholder, required, autocomplete
- Klasifikasi form: login, register, search, komentar, kontak, pembayaran
- Deteksi hidden field
- Deteksi file upload
- Deteksi CSRF token
- Deteksi honeypot field

#### C2 – Pengujian Input (Safe Mode)
- XSS reflection test (safe payload: `<script>alert(1)</script>`)
- SQL injection test (safe payload: `'`, `"`, `1=1`)
- Special character test
- Input length boundary test
- Null byte injection test
- Unicode bypass test

### 5.2 Shortcut Perintah Modul C (80 shortcut)

| # | Perintah Indonesia | Fungsi Rust | Keterangan |
|---|---|---|---|
| C001 | `uji_form` | `form::uji_semua()` | Uji semua form |
| C002 | `deteksi_form` | `form::deteksi()` | Temukan semua form |
| C003 | `analisis_form` | `form::analisis()` | Analisis lengkap form |
| C004 | `deteksi_form_login` | `form::jenis::login()` | Form login |
| C005 | `deteksi_form_daftar` | `form::jenis::daftar()` | Form register |
| C006 | `deteksi_form_cari` | `form::jenis::cari()` | Form pencarian |
| C007 | `deteksi_form_komentar` | `form::jenis::komentar()` | Form komentar |
| C008 | `deteksi_form_kontak` | `form::jenis::kontak()` | Form kontak |
| C009 | `deteksi_form_bayar` | `form::jenis::bayar()` | Form pembayaran |
| C010 | `deteksi_field_tersembunyi` | `form::field::tersembunyi()` | Hidden fields |
| C011 | `deteksi_upload` | `form::field::upload()` | File upload |
| C012 | `cek_csrf_token` | `form::keamanan::csrf()` | CSRF token |
| C013 | `cek_honeypot` | `form::keamanan::honeypot()` | Honeypot field |
| C014 | `analisis_placeholder` | `form::analisis_placeholder()` | Semua placeholder |
| C015 | `analisis_autocomplete` | `form::analisis_autocomplete()` | Autocomplete attr |
| C016 | `analisis_required` | `form::analisis_required()` | Required fields |
| C017 | `uji_input` | `form::uji::input()` | Uji semua input |
| C018 | `uji_xss` | `form::uji::xss()` | XSS reflection test |
| C019 | `uji_sqli` | `form::uji::sqli()` | SQL injection test |
| C020 | `uji_karakter_spesial` | `form::uji::special_char()` | Karakter spesial |
| C021 | `uji_panjang_max` | `form::uji::panjang_max()` | Batas panjang input |
| C022 | `uji_panjang_min` | `form::uji::panjang_min()` | Min input length |
| C023 | `uji_null_byte` | `form::uji::null_byte()` | Null byte injection |
| C024 | `uji_unicode` | `form::uji::unicode()` | Unicode bypass |
| C025 | `uji_encoding` | `form::uji::encoding()` | URL encoding bypass |
| C026 | `uji_double_encoding` | `form::uji::double_encoding()` | Double URL encoding |
| C027 | `uji_html_entity` | `form::uji::html_entity()` | HTML entity bypass |
| C028 | `uji_payload_custom` | `form::uji::payload(p)` | Custom payload |
| C029 | `mode_aman` | `form::mode::aman()` | Safe mode (default) |
| C030 | `mode_agresif` | `form::mode::agresif()` | Aggressive (lab only) |
| C031 | `isi_form_dummy` | `form::isi_dummy()` | Isi otomatis data palsu |
| C032 | `isi_form_random` | `form::isi_random()` | Random data |
| C033 | `kirim_form` | `form::kirim()` | Submit form (POST) |
| C034 | `simulasi_login` | `form::simulasi::login(u,p)` | Test login dummy |
| C035 | `cek_respons_form` | `form::cek_respons()` | Analisis respons submit |
| C036 | `cek_error_message` | `form::cek_error()` | Pesan error bocor |
| C037 | `cek_stack_trace` | `form::cek_stack()` | Stack trace di respons |
| C038 | `cek_waktu_respons_form` | `form::waktu_respons()` | Timing-based analysis |
| C039 | `cek_blind_sqli` | `form::uji::blind_sqli()` | Blind SQLi (time-based, safe) |
| C040 | `cek_header_inject` | `form::uji::header_inject()` | Header injection via form |
| C041 | `cek_template_inject` | `form::uji::ssti()` | SSTI (safe payload) |
| C042 | `cek_redirect_form` | `form::uji::open_redirect()` | Open redirect via form |
| C043 | `cek_email_form` | `form::uji::email_format()` | Email format validation |
| C044 | `cek_regex_dos` | `form::uji::redos()` | ReDoS (safe) |
| C045 | `highlight_form` | `form::highlight()` | Highlight form penting |
| C046 | `skor_keamanan_form` | `form::skor()` | Scoring form 0-100 |
| C047 | `rekomendasi_form` | `form::rekomendasi()` | Saran perbaikan |
| C048 | `simpan_hasil_form` | `form::simpan()` | Simpan ke log |
| C049 | `export_json_form` | `form::export_json()` | Export JSON |
| C050 | `export_csv_form` | `form::export_csv()` | Export CSV |
| C051 | `bandingkan_form` | `form::bandingkan(u1,u2)` | Bandingkan dua URL |
| C052 | `uji_rate_limit` | `form::uji::rate_limit()` | Test rate limiting |
| C053 | `uji_brute_login` | `form::uji::brute_login()` | Brute force protection test |
| C054 | `cek_lockout` | `form::uji::lockout()` | Account lockout check |
| C055 | `cek_captcha` | `form::keamanan::captcha()` | Deteksi CAPTCHA |
| C056 | `cek_2fa` | `form::keamanan::dua_faktor()` | Deteksi 2FA form |
| C057 | `cek_password_policy` | `form::keamanan::password()` | Policy password |
| C058 | `cek_clipboard_access` | `form::uji::clipboard()` | Clipboard hijacking |
| C059 | `cek_form_method` | `form::analisis_method()` | GET vs POST check |
| C060 | `uji_form_multi` | `form::multi::uji()` | Uji form di semua halaman |
| C061 | `cek_input_type` | `form::analisis_type()` | Type attribute semua input |
| C062 | `cek_label_form` | `form::analisis_label()` | Label accessibility |
| C063 | `deteksi_form_api` | `form::api::deteksi()` | Form yang menuju API |
| C064 | `cek_json_submit` | `form::api::json_submit()` | Form submit JSON |
| C065 | `cek_multipart` | `form::api::multipart()` | Multipart form-data |
| C066 | `uji_file_upload_type` | `form::upload::jenis()` | File type restriction test |
| C067 | `uji_upload_ekstensi` | `form::upload::ekstensi()` | Ekstensi file bypass |
| C068 | `uji_upload_ukuran` | `form::upload::ukuran()` | Max size test |
| C069 | `uji_upload_mime` | `form::upload::mime()` | MIME type validation |
| C070 | `cek_path_upload` | `form::upload::path()` | Upload destination path |
| C071 | `cek_overwrite` | `form::upload::overwrite()` | File overwrite test |
| C072 | `uji_iframe_injection` | `form::uji::iframe()` | iFrame injection |
| C073 | `uji_dom_xss` | `form::uji::dom_xss()` | DOM-based XSS (safe) |
| C074 | `cek_stored_xss` | `form::uji::stored_xss()` | Stored XSS potential |
| C075 | `analisis_form_lengkap` | `form::lengkap()` | Full form analysis |
| C076 | `cetak_form` | `form::cetak()` | Print form tree |
| C077 | `filter_form_penting` | `form::filter_penting()` | Hanya form berisiko |
| C078 | `ringkasan_form` | `form::ringkasan()` | Ringkasan uji form |
| C079 | `ulangi_uji_form` | `form::refresh()` | Re-run form test |
| C080 | `bantu_form` | `form::bantuan()` | Help modul form |

---

## 6. MODUL D – ANALISIS KONTEN & NLP

**Crate:** `indosec-nlp`

### 6.1 Shortcut Perintah Modul D (60 shortcut)

| # | Perintah Indonesia | Fungsi Rust | Keterangan |
|---|---|---|---|
| D001 | `analisis_teks` | `nlp::analisis_teks()` | Analisis teks halaman |
| D002 | `deteksi_bahasa_konten` | `nlp::deteksi_bahasa()` | Deteksi bahasa konten |
| D003 | `ekstrak_kata_kunci` | `nlp::keyword()` | Keyword extraction |
| D004 | `cek_kata_sensitif` | `nlp::sensitif()` | Kata sensitif keamanan |
| D005 | `cek_kata_login` | `nlp::kata_login()` | Login/password mentions |
| D006 | `cek_kata_admin` | `nlp::kata_admin()` | Admin mentions |
| D007 | `cek_kata_api_key` | `nlp::kata_apikey()` | API key exposure |
| D008 | `cek_kata_token` | `nlp::kata_token()` | Token/secret mentions |
| D009 | `cek_kata_database` | `nlp::kata_db()` | DB connection strings |
| D010 | `cek_email_konten` | `nlp::email()` | Email dalam konten |
| D011 | `cek_nomor_konten` | `nlp::nomor()` | Nomor telepon/ID |
| D012 | `cek_nik` | `nlp::nik()` | Pola NIK Indonesia |
| D013 | `cek_npwp` | `nlp::npwp()` | Pola NPWP |
| D014 | `cek_ktp` | `nlp::ktp()` | Nomor KTP |
| D015 | `cek_rekening` | `nlp::rekening()` | Nomor rekening bank |
| D016 | `cek_koordinat` | `nlp::koordinat()` | GPS coordinates |
| D017 | `hitung_kata` | `nlp::hitung_kata()` | Word count |
| D018 | `hitung_kalimat` | `nlp::hitung_kalimat()` | Sentence count |
| D019 | `hitung_paragraf` | `nlp::hitung_paragraf()` | Paragraph count |
| D020 | `analisis_sentimen` | `nlp::sentimen()` | Sentiment basic |
| D021 | `deteksi_spam` | `nlp::deteksi_spam()` | Spam pattern |
| D022 | `deteksi_phishing_teks` | `nlp::phishing()` | Phishing keyword |
| D023 | `cek_copyright` | `nlp::copyright()` | Copyright notice |
| D024 | `cek_privasi_policy` | `nlp::privasi()` | Privacy policy detected |
| D025 | `cek_tos` | `nlp::syarat()` | Terms of Service |
| D026 | `ekstrak_tanggal` | `nlp::tanggal()` | Tanggal dalam teks |
| D027 | `ekstrak_harga` | `nlp::harga()` | Harga/currency |
| D028 | `ekstrak_nama_orang` | `nlp::nama()` | Named entity (orang) |
| D029 | `ekstrak_nama_org` | `nlp::organisasi()` | Named entity (org) |
| D030 | `ekstrak_nama_tempat` | `nlp::tempat()` | Named entity (tempat) |
| D031 | `cek_konten_duplikat` | `nlp::duplikat()` | Duplicate content |
| D032 | `cek_readability` | `nlp::readability()` | Readability score |
| D033 | `cek_kata_kasar` | `nlp::kata_kasar()` | Profanity detection |
| D034 | `cek_konten_sensitif_id` | `nlp::sensitif_id()` | Konten sensitif konteks Indonesia |
| D035 | `cek_kode_dalam_html` | `nlp::kode_html()` | Code snippets in HTML |
| D036 | `ekstrak_json_tersembunyi` | `nlp::json_tersembunyi()` | JSON data dalam halaman |
| D037 | `ekstrak_base64` | `nlp::base64_detect()` | Base64 strings |
| D038 | `cek_path_dalam_js` | `nlp::js_path()` | Path dalam JavaScript |
| D039 | `ekstrak_url_dari_js` | `nlp::js_url()` | URL dalam file JS |
| D040 | `analisis_komentar_html` | `nlp::komentar_html()` | Info di komentar HTML |
| D041 | `cek_versi_dalam_kode` | `nlp::versi_kode()` | Version numbers |
| D042 | `deteksi_framework_js` | `nlp::js_framework()` | JS framework detection |
| D043 | `cek_inline_script` | `nlp::inline_script()` | Inline JS analysis |
| D044 | `cek_eval_dalam_js` | `nlp::eval_js()` | eval() usage |
| D045 | `cek_document_write` | `nlp::document_write()` | document.write() |
| D046 | `cek_innerHTML` | `nlp::inner_html()` | innerHTML assignments |
| D047 | `cek_location_href` | `nlp::location_href()` | location.href manipulations |
| D048 | `skor_konten` | `nlp::skor()` | Content security score |
| D049 | `ringkasan_konten` | `nlp::ringkasan()` | Summary analisis konten |
| D050 | `simpan_analisis_teks` | `nlp::simpan()` | Simpan ke log |
| D051 | `export_json_nlp` | `nlp::export_json()` | Export JSON |
| D052 | `export_csv_nlp` | `nlp::export_csv()` | Export CSV |
| D053 | `deteksi_obfuskasi` | `nlp::obfuskasi()` | Obfuscated code |
| D054 | `cek_minified_code` | `nlp::minified()` | Minified code |
| D055 | `cek_source_map` | `nlp::source_map()` | Source map exposed |
| D056 | `analisis_css` | `nlp::analisis_css()` | CSS security analysis |
| D057 | `cek_css_inject` | `nlp::css_inject()` | CSS injection pattern |
| D058 | `cek_import_css` | `nlp::css_import()` | @import external CSS |
| D059 | `ringkasan_nlp` | `nlp::ringkasan_penuh()` | Full NLP summary |
| D060 | `bantu_nlp` | `nlp::bantuan()` | Help modul NLP |

---

## 7. MODUL E – KEAMANAN JARINGAN

**Crate:** `indosec-network`

### 7.1 Shortcut Perintah Modul E (80 shortcut)

| # | Perintah Indonesia | Fungsi Rust | Keterangan |
|---|---|---|---|
| E001 | `analisis_jaringan` | `network::analisis()` | Analisis jaringan target |
| E002 | `ping_target` | `network::ping()` | ICMP ping |
| E003 | `traceroute_target` | `network::traceroute()` | Traceroute |
| E004 | `ukur_latensi` | `network::latensi()` | Latency measurement |
| E005 | `ukur_jitter` | `network::jitter()` | Jitter test |
| E006 | `ukur_bandwidth_net` | `network::bandwidth()` | Bandwidth estimation |
| E007 | `cek_paket_loss` | `network::packet_loss()` | Packet loss % |
| E008 | `tes_koneksi` | `network::tes_koneksi()` | Connectivity test |
| E009 | `cek_firewall` | `network::firewall()` | Firewall detection |
| E010 | `cek_ids` | `network::ids()` | IDS/IPS detection |
| E011 | `cek_proxy` | `network::proxy()` | Proxy detection |
| E012 | `cek_ip_leak` | `network::ip_leak()` | IP leak test |
| E013 | `cek_ipv6` | `network::ipv6()` | IPv6 support |
| E014 | `cek_dual_stack` | `network::dual_stack()` | IPv4/IPv6 dual stack |
| E015 | `analisis_dns_timing` | `network::dns_timing()` | DNS response time |
| E016 | `cek_dns_cache` | `network::dns_cache()` | DNS caching |
| E017 | `cek_dnssec` | `network::dnssec()` | DNSSEC validation |
| E018 | `cek_dns_rebinding` | `network::dns_rebinding()` | DNS rebinding check |
| E019 | `cek_rpz` | `network::rpz()` | Response Policy Zone |
| E020 | `analisis_bgp` | `network::bgp()` | BGP route analysis |
| E021 | `cek_anycast` | `network::anycast()` | Anycast detection |
| E022 | `tes_http2` | `network::http2()` | HTTP/2 support |
| E023 | `tes_http3` | `network::http3()` | HTTP/3 / QUIC |
| E024 | `tes_websocket` | `network::websocket()` | WebSocket connectivity |
| E025 | `tes_grpc` | `network::grpc()` | gRPC endpoint |
| E026 | `analisis_tls_handshake` | `network::tls_handshake()` | TLS handshake timing |
| E027 | `cek_tcp_keep_alive` | `network::keepalive()` | TCP keep-alive |
| E028 | `cek_server_push` | `network::server_push()` | HTTP/2 server push |
| E029 | `cek_early_hints` | `network::early_hints()` | 103 Early Hints |
| E030 | `tes_koneksi_ssl` | `network::ssl_koneksi()` | Full SSL connection test |
| E031 | `cek_heartbleed` | `network::heartbleed()` | Heartbleed (informational) |
| E032 | `cek_poodle` | `network::poodle()` | POODLE vulnerability |
| E033 | `cek_beast` | `network::beast()` | BEAST check |
| E034 | `cek_crime` | `network::crime()` | CRIME attack |
| E035 | `cek_breach` | `network::breach()` | BREACH attack |
| E036 | `cek_lucky13` | `network::lucky13()` | Lucky13 |
| E037 | `cek_robot` | `network::robot()` | ROBOT attack |
| E038 | `cek_weak_cipher` | `network::weak_cipher()` | Weak cipher suites |
| E039 | `cek_null_cipher` | `network::null_cipher()` | NULL cipher |
| E040 | `cek_export_cipher` | `network::export_cipher()` | Export grade cipher |
| E041 | `cek_rc4` | `network::rc4()` | RC4 (deprecated) |
| E042 | `cek_3des` | `network::triple_des()` | 3DES (deprecated) |
| E043 | `skor_tls` | `network::skor_tls()` | TLS grade (A+/A/B/C/F) |
| E044 | `rekomendasi_tls` | `network::rekomendasi_tls()` | TLS recommendations |
| E045 | `analisis_http_version` | `network::http_versi()` | HTTP version analysis |
| E046 | `cek_compression` | `network::kompresi()` | Content compression |
| E047 | `cek_gzip` | `network::gzip()` | Gzip support |
| E048 | `cek_brotli` | `network::brotli()` | Brotli support |
| E049 | `cek_zstd` | `network::zstd()` | Zstandard |
| E050 | `cek_cache_control` | `network::cache_kontrol()` | Cache-Control header |
| E051 | `cek_etag_weak` | `network::etag_weak()` | Weak ETag |
| E052 | `tes_dos_mini` | `network::dos_mini()` | Mini DoS resilience (safe) |
| E053 | `tes_slowloris` | `network::slowloris()` | Slowloris (informational) |
| E054 | `tes_http_flood` | `network::flood_test()` | HTTP flood (safe, 10 req) |
| E055 | `tes_concurrent` | `network::concurrent(n)` | N concurrent requests |
| E056 | `ukur_throughput` | `network::throughput()` | Requests per second |
| E057 | `cek_rate_limit_global` | `network::rate_limit()` | Global rate limiting |
| E058 | `cek_ip_blacklist` | `network::blacklist()` | IP blacklist check |
| E059 | `cek_ip_reputation` | `network::reputasi_ip()` | IP reputation |
| E060 | `cek_tor_exit` | `network::tor_exit()` | Tor exit node check |
| E061 | `cek_vpn_ip` | `network::vpn_ip()` | VPN IP detection |
| E062 | `cek_datacenter_ip` | `network::dc_ip()` | Datacenter IP |
| E063 | `analisis_header_request` | `network::req_header()` | Analisis request headers |
| E064 | `cek_proxy_header` | `network::proxy_header()` | X-Forwarded-For, etc. |
| E065 | `cek_real_ip` | `network::real_ip()` | Real IP behind proxy |
| E066 | `tes_timeout` | `network::timeout_test()` | Timeout behavior |
| E067 | `tes_large_request` | `network::large_req()` | Large payload handling |
| E068 | `tes_header_overflow` | `network::header_overflow()` | Header size limit |
| E069 | `tes_body_overflow` | `network::body_overflow()` | Body size limit |
| E070 | `tes_chunked` | `network::chunked()` | Chunked transfer |
| E071 | `simpan_analisis_jaringan` | `network::simpan()` | Simpan ke log |
| E072 | `export_json_jaringan` | `network::export_json()` | Export JSON |
| E073 | `export_csv_jaringan` | `network::export_csv()` | Export CSV |
| E074 | `skor_jaringan` | `network::skor()` | Scoring jaringan |
| E075 | `laporan_jaringan` | `network::laporan()` | Laporan jaringan |
| E076 | `cek_cdn_cache_hit` | `network::cdn_cache()` | CDN cache hit/miss |
| E077 | `cek_origin_ip` | `network::origin_ip()` | Origin IP behind CDN |
| E078 | `tes_multi_region` | `network::multi_region()` | Test dari berbagai region |
| E079 | `ringkasan_jaringan` | `network::ringkasan()` | Summary jaringan |
| E080 | `bantu_jaringan` | `network::bantuan()` | Help modul jaringan |

---

## 8. MODUL F – ANALISIS KRIPTOGRAFI & TLS

**Crate:** `indosec-crypto`

### 8.1 Shortcut Perintah Modul F (60 shortcut)

| # | Perintah Indonesia | Fungsi Rust | Keterangan |
|---|---|---|---|
| F001 | `analisis_kriptografi` | `crypto::analisis()` | Analisis lengkap crypto |
| F002 | `hash_md5` | `crypto::hash::md5(s)` | MD5 hash string |
| F003 | `hash_sha1` | `crypto::hash::sha1(s)` | SHA-1 hash |
| F004 | `hash_sha256` | `crypto::hash::sha256(s)` | SHA-256 hash |
| F005 | `hash_sha512` | `crypto::hash::sha512(s)` | SHA-512 hash |
| F006 | `hash_bcrypt` | `crypto::hash::bcrypt_info()` | Bcrypt info |
| F007 | `enkode_base64` | `crypto::encode::base64(s)` | Base64 encode |
| F008 | `dekode_base64` | `crypto::decode::base64(s)` | Base64 decode |
| F009 | `enkode_hex` | `crypto::encode::hex(s)` | Hex encode |
| F010 | `dekode_hex` | `crypto::decode::hex(s)` | Hex decode |
| F011 | `enkode_url` | `crypto::encode::url(s)` | URL encode |
| F012 | `dekode_url` | `crypto::decode::url(s)` | URL decode |
| F013 | `enkode_html_entity` | `crypto::encode::html(s)` | HTML entity encode |
| F014 | `dekode_html_entity` | `crypto::decode::html(s)` | HTML entity decode |
| F015 | `enkode_base32` | `crypto::encode::base32(s)` | Base32 encode |
| F016 | `dekode_base32` | `crypto::decode::base32(s)` | Base32 decode |
| F017 | `enkode_rot13` | `crypto::encode::rot13(s)` | ROT13 |
| F018 | `enkode_morse` | `crypto::encode::morse(s)` | Morse code |
| F019 | `enkode_binary` | `crypto::encode::binary(s)` | Binary |
| F020 | `dekode_jwt` | `crypto::jwt::decode(token)` | JWT decode (no verify) |
| F021 | `analisis_jwt` | `crypto::jwt::analisis()` | JWT header+payload |
| F022 | `cek_jwt_alg` | `crypto::jwt::algoritma()` | JWT algorithm |
| F023 | `cek_jwt_exp` | `crypto::jwt::expiry()` | JWT expiry |
| F024 | `cek_jwt_none` | `crypto::jwt::none_alg()` | JWT none algorithm |
| F025 | `cek_jwt_weak` | `crypto::jwt::weak_secret()` | JWT weak secret |
| F026 | `generate_uuid` | `crypto::generate::uuid()` | Generate UUID v4 |
| F027 | `generate_token` | `crypto::generate::token(n)` | Random token n bytes |
| F028 | `generate_password` | `crypto::generate::password(n)` | Strong password |
| F029 | `cek_entropi` | `crypto::entropi(s)` | Shannon entropy |
| F030 | `cek_randomness` | `crypto::randomness(s)` | Randomness analysis |
| F031 | `cek_ssl_grade` | `crypto::ssl_grade()` | SSL Labs-style grade |
| F032 | `cek_perfect_forward` | `crypto::pfs()` | Perfect Forward Secrecy |
| F033 | `cek_ecdhe` | `crypto::ecdhe()` | ECDHE key exchange |
| F034 | `cek_rsa_ukuran` | `crypto::rsa_size()` | RSA key size |
| F035 | `cek_ec_curve` | `crypto::ec_curve()` | Elliptic curve used |
| F036 | `cek_dh_param` | `crypto::dh_param()` | DH parameter size |
| F037 | `cek_weak_rng` | `crypto::weak_rng()` | Weak RNG patterns |
| F038 | `bandingkan_hash` | `crypto::bandingkan_hash(a,b)` | Compare two hashes |
| F039 | `cek_hash_bocor` | `crypto::hash_bocor()` | Hash in page source |
| F040 | `cek_password_bocor` | `crypto::password_bocor()` | Password in source |
| F041 | `cek_key_bocor` | `crypto::key_bocor()` | Crypto key in source |
| F042 | `cek_iv_bocor` | `crypto::iv_bocor()` | IV/nonce in source |
| F043 | `analisis_cookie_crypto` | `crypto::cookie_crypto()` | Cookie crypto analysis |
| F044 | `cek_session_id` | `crypto::session_id()` | Session ID entropy |
| F045 | `cek_csrf_token_kualitas` | `crypto::csrf_kualitas()` | CSRF token quality |
| F046 | `cek_oauth_state` | `crypto::oauth_state()` | OAuth state parameter |
| F047 | `analisis_token_umum` | `crypto::token_umum()` | Generic token analysis |
| F048 | `identifikasi_encoding` | `crypto::identify_encoding(s)` | Auto-detect encoding |
| F049 | `dekode_otomatis` | `crypto::dekode_otomatis(s)` | Auto-decode |
| F050 | `enkode_ganda` | `crypto::enkode_ganda(s)` | Double encode |
| F051 | `dekode_ganda` | `crypto::dekode_ganda(s)` | Double decode |
| F052 | `cek_tls_fingerprint` | `crypto::tls_fingerprint()` | JA3/JA4 fingerprint |
| F053 | `simpan_analisis_crypto` | `crypto::simpan()` | Simpan ke log |
| F054 | `export_json_crypto` | `crypto::export_json()` | Export JSON |
| F055 | `skor_crypto` | `crypto::skor()` | Crypto security score |
| F056 | `rekomendasi_crypto` | `crypto::rekomendasi()` | Crypto recommendations |
| F057 | `cek_padding_oracle` | `crypto::padding_oracle()` | Padding oracle check |
| F058 | `cek_length_extension` | `crypto::length_ext()` | Length extension attack |
| F059 | `ringkasan_crypto` | `crypto::ringkasan()` | Crypto summary |
| F060 | `bantu_crypto` | `crypto::bantuan()` | Help modul crypto |

---

## 9. MODUL G – OSINT & FOOTPRINTING

**Crate:** `indosec-osint`

### 9.1 Shortcut Perintah Modul G (80 shortcut)

| # | Perintah Indonesia | Fungsi Rust | Keterangan |
|---|---|---|---|
| G001 | `osint` | `osint::penuh()` | Full OSINT gathering |
| G002 | `cek_wayback` | `osint::wayback()` | Wayback Machine check |
| G003 | `cek_cache_google` | `osint::google_cache()` | Google cache |
| G004 | `cek_shodan` | `osint::shodan()` | Shodan data (API) |
| G005 | `cek_censys` | `osint::censys()` | Censys data |
| G006 | `cek_virustotal` | `osint::virustotal()` | VirusTotal check |
| G007 | `cek_urlscan` | `osint::urlscan()` | urlscan.io |
| G008 | `cek_securitytrails` | `osint::securitytrails()` | SecurityTrails |
| G009 | `cek_hunter` | `osint::hunter()` | Hunter.io email |
| G010 | `cek_pastebin` | `osint::pastebin()` | Pastebin mentions |
| G011 | `cek_github_leak` | `osint::github()` | GitHub secret leak |
| G012 | `cek_gitlab_leak` | `osint::gitlab()` | GitLab exposure |
| G013 | `cek_bitbucket_leak` | `osint::bitbucket()` | Bitbucket exposure |
| G014 | `cek_npm_leak` | `osint::npm()` | NPM package secrets |
| G015 | `cek_pypi_leak` | `osint::pypi()` | PyPI package secrets |
| G016 | `cek_dockerhub` | `osint::dockerhub()` | DockerHub public images |
| G017 | `cek_s3_publik` | `osint::s3_publik()` | S3 public bucket |
| G018 | `cek_gcs_publik` | `osint::gcs_publik()` | GCS public bucket |
| G019 | `cek_azure_publik` | `osint::azure_publik()` | Azure public storage |
| G020 | `cek_breach_email` | `osint::breach_email()` | HaveIBeenPwned check |
| G021 | `cek_breach_domain` | `osint::breach_domain()` | Domain in breaches |
| G022 | `cek_dark_web_mention` | `osint::dark_web()` | Dark web mention check |
| G023 | `cari_subdomain_pasif` | `osint::subdomain_pasif()` | Passive subdomain |
| G024 | `cek_cert_transparency` | `osint::cert_transparency()` | CT logs |
| G025 | `cek_amass` | `osint::amass()` | Domain enumeration |
| G026 | `cek_reverse_ip` | `osint::reverse_ip()` | Reverse IP lookup |
| G027 | `cek_shared_hosting` | `osint::shared_hosting()` | Shared hosting domains |
| G028 | `cek_history_dns` | `osint::dns_history()` | DNS history |
| G029 | `cek_history_ip` | `osint::ip_history()` | IP history |
| G030 | `cek_mx_history` | `osint::mx_history()` | MX history |
| G031 | `fingerprint_header_pasif` | `osint::header_pasif()` | Passive header FP |
| G032 | `cek_error_page` | `osint::error_page()` | Custom error pages |
| G033 | `cek_favicon_hash` | `osint::favicon_hash()` | Favicon Shodan hash |
| G034 | `cari_similar_domain` | `osint::similar_domain()` | Similar domains |
| G035 | `cek_domain_typosquat` | `osint::typosquat()` | Typosquatting |
| G036 | `cek_homoglyph` | `osint::homoglyph()` | Homoglyph domains |
| G037 | `cek_brand_impersonation` | `osint::brand_imp()` | Brand impersonation |
| G038 | `cek_linkedin` | `osint::linkedin()` | LinkedIn presence |
| G039 | `cek_twitter` | `osint::twitter()` | Twitter/X presence |
| G040 | `cek_facebook` | `osint::facebook()` | Facebook page |
| G041 | `cek_instagram` | `osint::instagram()` | Instagram |
| G042 | `cek_tiktok` | `osint::tiktok()` | TikTok |
| G043 | `cek_youtube` | `osint::youtube()` | YouTube channel |
| G044 | `cek_telegram` | `osint::telegram()` | Telegram group/channel |
| G045 | `cek_whatsapp_business` | `osint::whatsapp()` | WhatsApp Business |
| G046 | `cek_play_store` | `osint::play_store()` | Google Play app |
| G047 | `cek_app_store` | `osint::app_store()` | Apple App Store |
| G048 | `cek_alexa_rank` | `osint::alexa_rank()` | Alexa/traffic rank |
| G049 | `cek_similarweb` | `osint::similarweb()` | SimilarWeb data |
| G050 | `cek_builtwith` | `osint::builtwith()` | BuiltWith tech stack |
| G051 | `cek_wappalyzer` | `osint::wappalyzer()` | Wappalyzer |
| G052 | `cek_crunchbase` | `osint::crunchbase()` | Company info |
| G053 | `cek_cve_domain` | `osint::cve_domain()` | CVE for detected tech |
| G054 | `cek_exploit_db` | `osint::exploit_db()` | Exploit-DB search |
| G055 | `cek_nvd` | `osint::nvd()` | NVD database |
| G056 | `cek_rapid7` | `osint::rapid7()` | Rapid7 data |
| G057 | `cek_threatfox` | `osint::threatfox()` | ThreatFox IOC |
| G058 | `cek_abuseipdb` | `osint::abuseipdb()` | AbuseIPDB check |
| G059 | `cek_greynoise` | `osint::greynoise()` | GreyNoise scan |
| G060 | `cek_fofa` | `osint::fofa()` | FOFA search |
| G061 | `cek_zoomeye` | `osint::zoomeye()` | ZoomEye |
| G062 | `cek_binaryedge` | `osint::binaryedge()` | BinaryEdge |
| G063 | `cek_spamhaus` | `osint::spamhaus()` | Spamhaus DNSBL |
| G064 | `cek_sorbs` | `osint::sorbs()` | SORBS blacklist |
| G065 | `cek_barracuda` | `osint::barracuda()` | Barracuda reputation |
| G066 | `osint_ringkas` | `osint::ringkas()` | Quick OSINT summary |
| G067 | `osint_mendalam` | `osint::mendalam()` | Deep OSINT |
| G068 | `simpan_osint` | `osint::simpan()` | Simpan ke log |
| G069 | `export_json_osint` | `osint::export_json()` | Export JSON |
| G070 | `export_csv_osint` | `osint::export_csv()` | Export CSV |
| G071 | `skor_risiko_osint` | `osint::skor_risiko()` | Risk score dari OSINT |
| G072 | `laporan_osint` | `osint::laporan()` | Laporan OSINT |
| G073 | `cek_google_dork` | `osint::google_dork()` | Google dork suggestions |
| G074 | `cek_bing_dork` | `osint::bing_dork()` | Bing dork |
| G075 | `cek_metadata_file` | `osint::metadata_file()` | File metadata |
| G076 | `cek_exif` | `osint::exif()` | EXIF data gambar |
| G077 | `cek_pdf_metadata` | `osint::pdf_meta()` | PDF metadata |
| G078 | `cek_docx_metadata` | `osint::docx_meta()` | DOCX metadata |
| G079 | `ringkasan_osint` | `osint::ringkasan()` | Summary OSINT |
| G080 | `bantu_osint` | `osint::bantuan()` | Help modul OSINT |

---

## 10. MODUL H – LOGGING & REPORTING

**Crate:** `indosec-report`

### 10.1 Shortcut Perintah Modul H (80 shortcut)

| # | Perintah Indonesia | Fungsi Rust | Keterangan |
|---|---|---|---|
| H001 | `laporan` | `report::tampilkan()` | Tampilkan laporan terkini |
| H002 | `lihat_riwayat` | `report::riwayat()` | Semua aksi sebelumnya |
| H003 | `lihat_log_recon` | `report::log_recon()` | Log recon |
| H004 | `lihat_log_scan` | `report::log_scan()` | Log scan |
| H005 | `lihat_log_form` | `report::log_form()` | Log form |
| H006 | `lihat_log_hari_ini` | `report::log_hari_ini()` | Log hari ini |
| H007 | `lihat_log_minggu_ini` | `report::log_minggu()` | Log minggu ini |
| H008 | `lihat_log_bulan_ini` | `report::log_bulan()` | Log bulan ini |
| H009 | `cari_log` | `report::cari(q)` | Cari dalam log |
| H010 | `filter_log_sukses` | `report::filter_sukses()` | Hanya sukses |
| H011 | `filter_log_error` | `report::filter_error()` | Hanya error |
| H012 | `filter_log_warning` | `report::filter_warning()` | Hanya warning |
| H013 | `filter_log_target` | `report::filter_target(url)` | Filter per target |
| H014 | `hitung_baris_log` | `report::hitung_log()` | Jumlah entri log |
| H015 | `hapus_log` | `report::hapus_log()` | Hapus semua log |
| H016 | `hapus_log_lama` | `report::hapus_lama(n)` | Hapus log lebih dari n hari |
| H017 | `arsip_log` | `report::arsip()` | Arsipkan log |
| H018 | `backup_log` | `report::backup()` | Backup file log |
| H019 | `restore_log` | `report::restore(f)` | Restore log dari backup |
| H020 | `kompres_log` | `report::kompres()` | Kompres log (gzip) |
| H021 | `dekompress_log` | `report::dekompress(f)` | Dekompress log |
| H022 | `rotasi_log` | `report::rotasi()` | Rotasi log otomatis |
| H023 | `atur_rotasi` | `report::atur_rotasi(mb)` | Set ukuran rotasi |
| H024 | `merge_log` | `report::merge(f1,f2)` | Gabung dua file log |
| H025 | `bagi_log` | `report::bagi(n)` | Bagi log per n baris |
| H026 | `ekspor_laporan_json` | `report::ekspor_json()` | Export JSON |
| H027 | `ekspor_laporan_csv` | `report::ekspor_csv()` | Export CSV |
| H028 | `ekspor_laporan_xml` | `report::ekspor_xml()` | Export XML |
| H029 | `ekspor_laporan_html` | `report::ekspor_html()` | Export HTML |
| H030 | `ekspor_laporan_txt` | `report::ekspor_txt()` | Export TXT |
| H031 | `ekspor_laporan_md` | `report::ekspor_md()` | Export Markdown |
| H032 | `buat_laporan_eksekutif` | `report::eksekutif()` | Executive summary |
| H033 | `buat_laporan_teknis` | `report::teknis()` | Technical report |
| H034 | `buat_laporan_penuh` | `report::penuh()` | Full report |
| H035 | `buat_laporan_ringkas` | `report::ringkas()` | Summary report |
| H036 | `buat_laporan_kerentanan` | `report::kerentanan()` | Vulnerability report |
| H037 | `buat_laporan_kepatuhan` | `report::kepatuhan()` | Compliance report |
| H038 | `cetak_laporan` | `report::cetak()` | Print ke terminal |
| H039 | `kirim_laporan_email` | `report::kirim_email(addr)` | Email laporan |
| H040 | `bagikan_laporan` | `report::bagikan()` | Generate share link |
| H041 | `timestamp_log` | `report::timestamp()` | Cek timestamp terakhir |
| H042 | `skor_risiko_global` | `report::skor_global()` | Global risk score |
| H043 | `statistik_scan` | `report::statistik()` | Scan statistics |
| H044 | `grafik_ascii` | `report::grafik()` | ASCII chart |
| H045 | `tabel_kerentanan` | `report::tabel_vuln()` | Vulnerability table |
| H046 | `ringkasan_temuan` | `report::temuan()` | Findings summary |
| H047 | `daftar_rekomendasi` | `report::rekomendasi()` | All recommendations |
| H048 | `prioritas_perbaikan` | `report::prioritas()` | Fix priority order |
| H049 | `cek_regresi` | `report::regresi()` | Compare with prev scan |
| H050 | `bandingkan_laporan` | `report::bandingkan(f1,f2)` | Compare two reports |
| H051 | `tren_keamanan` | `report::tren()` | Security trend |
| H052 | `kalender_scan` | `report::kalender()` | Scan history calendar |
| H053 | `notif_temuan_baru` | `report::notif_baru()` | Notify new findings |
| H054 | `atur_tingkat_log` | `report::atur_level(l)` | Set log level |
| H055 | `lihat_ukuran_log` | `report::ukuran_log()` | File size log |
| H056 | `validasi_log` | `report::validasi()` | Validate log integrity |
| H057 | `enkripsi_log` | `report::enkripsi()` | Encrypt log file |
| H058 | `dekripsi_log` | `report::dekripsi()` | Decrypt log |
| H059 | `cek_integritas_log` | `report::integritas()` | SHA-256 checksum log |
| H060 | `tanda_tangani_laporan` | `report::tanda_tangan()` | Sign report |
| H061 | `verifikasi_laporan` | `report::verifikasi()` | Verify signature |
| H062 | `log_realtime` | `report::realtime()` | Live log stream |
| H063 | `tail_log` | `report::tail(n)` | Last n lines |
| H064 | `head_log` | `report::head(n)` | First n lines |
| H065 | `grep_log` | `report::grep(pola)` | Pattern search |
| H066 | `sort_log_waktu` | `report::sort_waktu()` | Sort by time |
| H067 | `sort_log_skor` | `report::sort_skor()` | Sort by score |
| H068 | `sort_log_target` | `report::sort_target()` | Sort by target |
| H069 | `dedup_log` | `report::dedup()` | Remove duplicates |
| H070 | `format_log_json` | `report::format_json()` | Reformat ke JSON |
| H071 | `format_log_csv` | `report::format_csv()` | Reformat ke CSV |
| H072 | `bersihkan_log` | `report::bersihkan()` | Clean/normalize log |
| H073 | `impor_log` | `report::impor(f)` | Import external log |
| H074 | `jadwal_backup_log` | `report::jadwal_backup()` | Schedule backup |
| H075 | `lokasi_log` | `report::lokasi()` | Show log directory |
| H076 | `buka_folder_log` | `report::buka_folder()` | Open log folder |
| H077 | `salin_log` | `report::salin(dest)` | Copy log ke path |
| H078 | `pindah_log` | `report::pindah(dest)` | Move log ke path |
| H079 | `ringkasan_log` | `report::ringkasan()` | Log summary |
| H080 | `bantu_laporan` | `report::bantuan()` | Help modul report |

---

## 11. MODUL I – KONFIGURASI & SISTEM

**Crate:** `indosec-core` (config module)

### 11.1 Shortcut Perintah Modul I (80 shortcut)

| # | Perintah Indonesia | Fungsi Rust | Keterangan |
|---|---|---|---|
| I001 | `konfigurasi` | `config::tampilkan()` | Tampilkan konfigurasi |
| I002 | `atur_target` | `config::set_target(url)` | Set target aktif |
| I003 | `lihat_target` | `config::lihat_target()` | Target aktif |
| I004 | `tambah_target` | `config::tambah_target(url)` | Tambah target |
| I005 | `hapus_target` | `config::hapus_target(i)` | Hapus target ke-i |
| I006 | `hapus_semua_target` | `config::hapus_semua()` | Hapus semua target |
| I007 | `daftar_target` | `config::daftar_target()` | List semua target |
| I008 | `pilih_target` | `config::pilih_target(i)` | Pilih target ke-i |
| I009 | `validasi_target` | `config::validasi_url(url)` | Validasi URL target |
| I010 | `catatan_target` | `config::catatan(i, teks)` | Beri catatan pada target |
| I011 | `ekspor_target` | `config::ekspor_target(f)` | Export targets.json |
| I012 | `impor_target` | `config::impor_target(f)` | Import targets.json |
| I013 | `atur_timeout` | `config::set_timeout(ms)` | Global request timeout |
| I014 | `atur_thread` | `config::set_threads(n)` | Max concurrent threads |
| I015 | `atur_delay` | `config::set_delay(ms)` | Delay antar request |
| I016 | `atur_retries` | `config::set_retries(n)` | Max retries |
| I017 | `atur_user_agent_global` | `config::set_ua(ua)` | User-Agent global |
| I018 | `atur_ua_random` | `config::ua_random()` | Rotasi User-Agent |
| I019 | `atur_proxy_global` | `config::set_proxy(url)` | Set proxy |
| I020 | `hapus_proxy` | `config::hapus_proxy()` | Hapus proxy |
| I021 | `atur_header_global` | `config::set_header(k,v)` | Tambah header global |
| I022 | `hapus_header_global` | `config::hapus_header(k)` | Hapus header |
| I023 | `atur_cookie_global` | `config::set_cookie(c)` | Set cookie |
| I024 | `atur_auth_basic` | `config::auth_basic(u,p)` | HTTP Basic Auth |
| I025 | `atur_auth_bearer` | `config::auth_bearer(t)` | Bearer token |
| I026 | `atur_api_key` | `config::api_key(k,v)` | API key header |
| I027 | `hapus_auth` | `config::hapus_auth()` | Clear auth |
| I028 | `atur_bahasa_cli` | `config::bahasa(lang)` | Bahasa CLI (id/en) |
| I029 | `atur_warna` | `config::warna(on/off)` | Toggle terminal color |
| I030 | `atur_tema` | `config::tema(nama)` | Color theme |
| I031 | `atur_log_level` | `config::log_level(l)` | info/warn/error/debug |
| I032 | `atur_log_dir` | `config::log_dir(path)` | Set log directory |
| I033 | `atur_output_dir` | `config::output_dir(path)` | Set output directory |
| I034 | `aktifkan_fitur` | `config::aktifkan(fitur)` | Enable feature |
| I035 | `nonaktifkan_fitur` | `config::nonaktifkan(fitur)` | Disable feature |
| I036 | `lihat_fitur` | `config::lihat_fitur()` | List enabled features |
| I037 | `mode_debug_global` | `config::debug_mode(on/off)` | Debug mode |
| I038 | `mode_verbose` | `config::verbose(on/off)` | Verbose output |
| I039 | `mode_senyap` | `config::senyap()` | Silent mode |
| I040 | `mode_interaktif` | `config::interaktif()` | Interactive mode |
| I041 | `mode_batch` | `config::batch()` | Batch mode (no prompt) |
| I042 | `simpan_konfigurasi` | `config::simpan()` | Simpan ke settings.toml |
| I043 | `muat_konfigurasi` | `config::muat(f)` | Load config dari file |
| I044 | `reset_konfigurasi` | `config::reset()` | Reset ke default |
| I045 | `ekspor_konfigurasi` | `config::ekspor(f)` | Export config |
| I046 | `impor_konfigurasi` | `config::impor(f)` | Import config |
| I047 | `backup_konfigurasi` | `config::backup()` | Backup config |
| I048 | `restore_konfigurasi` | `config::restore(f)` | Restore config |
| I049 | `info_sistem` | `config::info_sistem()` | System info |
| I050 | `info_versi` | `config::versi()` | Version info |
| I051 | `cek_update` | `config::cek_update()` | Check update |
| I052 | `update_tools` | `config::update()` | Self-update |
| I053 | `rollback_versi` | `config::rollback()` | Rollback version |
| I054 | `cek_dependensi` | `config::cek_dep()` | Check dependencies |
| I055 | `install_dependensi` | `config::install_dep()` | Install deps |
| I056 | `cek_ruang_disk` | `config::cek_disk()` | Disk space |
| I057 | `cek_memori` | `config::cek_ram()` | RAM usage |
| I058 | `cek_cpu` | `config::cek_cpu()` | CPU usage |
| I059 | `reset_session` | `config::reset_session()` | Reset CLI state |
| I060 | `bersihkan_cache` | `config::bersihkan_cache()` | Clear cache |
| I061 | `bersihkan_temp` | `config::bersihkan_temp()` | Clear temp files |
| I062 | `atur_wordlist` | `config::set_wordlist(f)` | Custom wordlist |
| I063 | `atur_payload` | `config::set_payload(f)` | Custom payload |
| I064 | `atur_template` | `config::set_template(f)` | Report template |
| I065 | `lihat_shortcut` | `config::shortcut()` | Semua shortcut |
| I066 | `cari_shortcut` | `config::cari_shortcut(q)` | Cari shortcut |
| I067 | `tambah_alias` | `config::tambah_alias(a,c)` | Custom alias |
| I068 | `hapus_alias` | `config::hapus_alias(a)` | Hapus alias |
| I069 | `daftar_alias` | `config::daftar_alias()` | List alias |
| I070 | `ekspor_alias` | `config::ekspor_alias(f)` | Export alias |
| I071 | `impor_alias` | `config::impor_alias(f)` | Import alias |
| I072 | `atur_enkripsi_config` | `config::enkripsi_config()` | Encrypt config |
| I073 | `atur_pin` | `config::set_pin()` | Set PIN lock |
| I074 | `hapus_pin` | `config::hapus_pin()` | Remove PIN |
| I075 | `kunci_cli` | `config::kunci()` | Lock CLI |
| I076 | `buka_kunci_cli` | `config::buka_kunci(pin)` | Unlock CLI |
| I077 | `izin_file` | `config::cek_izin()` | Check file permissions |
| I078 | `perbaiki_izin` | `config::perbaiki_izin()` | Fix permissions |
| I079 | `ringkasan_konfigurasi` | `config::ringkasan()` | Config summary |
| I080 | `bantu_konfigurasi` | `config::bantuan()` | Help konfigurasi |

---

## 12. MODUL J – CLI INTERAKTIF & UX

**Crate:** `indosec` (binary, `src/cli/`)

### 12.1 Shortcut Perintah Modul J (60 shortcut)

| # | Perintah Indonesia | Fungsi Rust | Keterangan |
|---|---|---|---|
| J001 | `dashboard` | `cli::dashboard()` | Tampilkan dashboard utama |
| J002 | `menu` | `cli::menu()` | Menu interaktif |
| J003 | `menu_recon` | `cli::menu_recon()` | Submenu recon |
| J004 | `menu_scan` | `cli::menu_scan()` | Submenu scan |
| J005 | `menu_form` | `cli::menu_form()` | Submenu form |
| J006 | `menu_laporan` | `cli::menu_laporan()` | Submenu laporan |
| J007 | `menu_konfigurasi` | `cli::menu_config()` | Submenu config |
| J008 | `menu_osint` | `cli::menu_osint()` | Submenu OSINT |
| J009 | `menu_crypto` | `cli::menu_crypto()` | Submenu crypto |
| J010 | `menu_jaringan` | `cli::menu_jaringan()` | Submenu jaringan |
| J011 | `bantu` | `cli::help()` | Bantuan umum |
| J012 | `bantu_lengkap` | `cli::help_lengkap()` | Bantuan komprehensif |
| J013 | `tutorial` | `cli::tutorial()` | Tutorial interaktif |
| J014 | `contoh_perintah` | `cli::contoh()` | Contoh penggunaan |
| J015 | `keluar` | `cli::keluar()` | Keluar dari CLI |
| J016 | `keluar_paksa` | `cli::exit_force()` | Force exit |
| J017 | `bersih` | `cli::clear()` | Clear terminal |
| J018 | `bersihkan_layar` | `cli::clear_screen()` | Alias bersih |
| J019 | `atas` | `cli::scroll_top()` | Scroll ke atas |
| J020 | `bawah` | `cli::scroll_bottom()` | Scroll ke bawah |
| J021 | `aktifkan_warna` | `cli::color_on()` | Aktifkan warna |
| J022 | `nonaktifkan_warna` | `cli::color_off()` | Nonaktifkan warna |
| J023 | `atur_lebar` | `cli::set_width(n)` | Terminal width |
| J024 | `mode_compact` | `cli::compact()` | Output compact |
| J025 | `mode_lebar` | `cli::wide()` | Output wide |
| J026 | `animasi_on` | `cli::animasi_on()` | Spinner/progress on |
| J027 | `animasi_off` | `cli::animasi_off()` | Spinner off |
| J028 | `aktifkan_autocomplete` | `cli::autocomplete_on()` | Tab completion |
| J029 | `nonaktifkan_autocomplete` | `cli::autocomplete_off()` | Disable autocomplete |
| J030 | `riwayat_perintah` | `cli::riwayat()` | Command history |
| J031 | `hapus_riwayat` | `cli::hapus_riwayat()` | Clear history |
| J032 | `cari_riwayat` | `cli::cari_riwayat(q)` | Search history |
| J033 | `ulangi_perintah` | `cli::ulangi(n)` | Repeat perintah ke-n |
| J034 | `jalankan_skrip` | `cli::jalankan_skrip(f)` | Run script file |
| J035 | `rekam_sesi` | `cli::rekam()` | Record session |
| J036 | `putar_sesi` | `cli::putar(f)` | Replay session |
| J037 | `ekspor_sesi` | `cli::ekspor_sesi(f)` | Export session |
| J038 | `impor_sesi` | `cli::impor_sesi(f)` | Import session |
| J039 | `bookmark` | `cli::bookmark_add()` | Bookmark perintah |
| J040 | `lihat_bookmark` | `cli::bookmark_list()` | List bookmarks |
| J041 | `hapus_bookmark` | `cli::bookmark_del(i)` | Hapus bookmark |
| J042 | `mode_gelap` | `cli::dark_mode()` | Dark theme |
| J043 | `mode_terang` | `cli::light_mode()` | Light theme |
| J044 | `mode_matrix` | `cli::matrix_mode()` | Matrix theme |
| J045 | `mode_minimal` | `cli::minimal_mode()` | Minimal output |
| J046 | `mode_verbose` | `cli::verbose_mode()` | Verbose output |
| J047 | `mode_json` | `cli::json_mode()` | Output as JSON |
| J048 | `tampilkan_statistik` | `cli::statistik()` | Session statistics |
| J049 | `tampilkan_waktu` | `cli::waktu()` | Current time |
| J050 | `tampilkan_tanggal` | `cli::tanggal()` | Current date |
| J051 | `hitung_mundur` | `cli::hitung_mundur(s)` | Countdown timer |
| J052 | `notif_selesai` | `cli::notif_selesai()` | Completion notif |
| J053 | `aktifkan_konfirmasi` | `cli::konfirmasi_on()` | Ask before action |
| J054 | `nonaktifkan_konfirmasi` | `cli::konfirmasi_off()` | No confirmation |
| J055 | `aktifkan_undo` | `cli::undo_on()` | Enable undo |
| J056 | `undo` | `cli::undo()` | Undo last action |
| J057 | `redo` | `cli::redo()` | Redo |
| J058 | `panduan_cepat` | `cli::panduan_cepat()` | Quick start guide |
| J059 | `ringkasan_sesi` | `cli::ringkasan_sesi()` | Session summary |
| J060 | `bantu_cli` | `cli::bantuan()` | Help CLI |

---

## 13. MODUL K – OTOMASI & SCRIPTING

**Crate:** `indosec-automation`

### 13.1 Shortcut Perintah Modul K (60 shortcut)

| # | Perintah Indonesia | Fungsi Rust | Keterangan |
|---|---|---|---|
| K001 | `otomasi` | `auto::tampilkan()` | Automation dashboard |
| K002 | `buat_skrip` | `auto::buat_skrip(nama)` | Buat script baru |
| K003 | `edit_skrip` | `auto::edit_skrip(f)` | Edit script |
| K004 | `hapus_skrip` | `auto::hapus_skrip(f)` | Hapus script |
| K005 | `daftar_skrip` | `auto::daftar_skrip()` | List scripts |
| K006 | `jalankan_skrip_auto` | `auto::jalankan(f)` | Run script |
| K007 | `jadwalkan_scan` | `auto::jadwal(waktu, cmd)` | Schedule cron |
| K008 | `lihat_jadwal` | `auto::lihat_jadwal()` | List scheduled |
| K009 | `hapus_jadwal` | `auto::hapus_jadwal(i)` | Hapus schedule |
| K010 | `aktifkan_jadwal` | `auto::aktifkan_jadwal(i)` | Enable schedule |
| K011 | `nonaktifkan_jadwal` | `auto::nonaktifkan_jadwal(i)` | Disable schedule |
| K012 | `scan_terjadwal_harian` | `auto::harian(waktu, target)` | Daily scheduled scan |
| K013 | `scan_terjadwal_mingguan` | `auto::mingguan(hari, waktu)` | Weekly scan |
| K014 | `scan_terjadwal_bulanan` | `auto::bulanan(tgl, waktu)` | Monthly scan |
| K015 | `scan_terjadwal_setiap` | `auto::setiap(interval)` | Interval scan |
| K016 | `notif_email_auto` | `auto::notif_email(addr)` | Auto email alert |
| K017 | `notif_webhook` | `auto::webhook(url)` | Webhook notification |
| K018 | `notif_telegram_bot` | `auto::telegram(token,id)` | Telegram bot alert |
| K019 | `notif_discord` | `auto::discord(webhook)` | Discord webhook |
| K020 | `notif_slack` | `auto::slack(webhook)` | Slack webhook |
| K021 | `kondisi_alert` | `auto::kondisi(expr)` | Alert condition |
| K022 | `ambang_skor` | `auto::ambang_skor(n)` | Score threshold alert |
| K023 | `alur_kerja` | `auto::workflow()` | Create workflow |
| K024 | `lihat_alur_kerja` | `auto::lihat_workflow()` | List workflows |
| K025 | `jalankan_alur_kerja` | `auto::run_workflow(nama)` | Run workflow |
| K026 | `hentikan_alur_kerja` | `auto::stop_workflow()` | Stop workflow |
| K027 | `template_alur` | `auto::template_workflow()` | Workflow templates |
| K028 | `alur_recon_penuh` | `auto::alur_recon_penuh()` | Full recon workflow |
| K029 | `alur_audit_cepat` | `auto::alur_audit_cepat()` | Quick audit workflow |
| K030 | `alur_pemantauan` | `auto::alur_monitor()` | Monitoring workflow |
| K031 | `monitor_perubahan` | `auto::monitor(interval)` | Monitor target changes |
| K032 | `deteksi_perubahan` | `auto::diff()` | Detect changes vs last |
| K033 | `alert_perubahan` | `auto::alert_perubahan()` | Alert on change |
| K034 | `monitor_ssl_expired` | `auto::monitor_ssl()` | Monitor SSL expiry |
| K035 | `monitor_uptime` | `auto::monitor_uptime()` | Uptime monitoring |
| K036 | `monitor_port` | `auto::monitor_port()` | Port change monitoring |
| K037 | `monitor_dns` | `auto::monitor_dns()` | DNS change detection |
| K038 | `monitor_konten` | `auto::monitor_konten()` | Content change detection |
| K039 | `simpan_baseline` | `auto::simpan_baseline()` | Save baseline |
| K040 | `bandingkan_baseline` | `auto::bandingkan_baseline()` | Compare with baseline |
| K041 | `impor_skrip_bash` | `auto::impor_bash(f)` | Import bash script |
| K042 | `ekspor_ke_bash` | `auto::ekspor_bash(f)` | Export as bash |
| K043 | `ekspor_ke_python` | `auto::ekspor_python(f)` | Export as Python |
| K044 | `tes_skrip` | `auto::tes_skrip(f)` | Dry-run script |
| K045 | `validasi_skrip` | `auto::validasi(f)` | Syntax validate |
| K046 | `debug_skrip` | `auto::debug(f)` | Debug mode script |
| K047 | `profil_waktu` | `auto::profil_waktu(f)` | Time profiling |
| K048 | `antrian_perintah` | `auto::antrian()` | Command queue |
| K049 | `tambah_antrian_auto` | `auto::tambah_antrian(cmd)` | Queue command |
| K050 | `jalankan_antrian` | `auto::jalankan_antrian()` | Process queue |
| K051 | `kosongkan_antrian_auto` | `auto::kosongkan_antrian()` | Clear queue |
| K052 | `ekspor_antrian_auto` | `auto::ekspor_antrian(f)` | Export queue |
| K053 | `paralel` | `auto::paralel(cmds)` | Run commands parallel |
| K054 | `berurutan` | `auto::berurutan(cmds)` | Sequential execution |
| K055 | `jika_berhasil` | `auto::jika_berhasil(c)` | If success then |
| K056 | `jika_gagal` | `auto::jika_gagal(c)` | If fail then |
| K057 | `coba_lagi` | `auto::coba_lagi(n)` | Retry n times |
| K058 | `simpan_hasil_otomasi` | `auto::simpan()` | Save automation results |
| K059 | `ringkasan_otomasi` | `auto::ringkasan()` | Automation summary |
| K060 | `bantu_otomasi` | `auto::bantuan()` | Help otomasi |

---

## 14. MODUL L – ANALISIS API & REST

**Crate:** `indosec-api`

### 14.1 Shortcut Perintah Modul L (60 shortcut)

| # | Perintah Indonesia | Fungsi Rust | Keterangan |
|---|---|---|---|
| L001 | `analisis_api` | `api::analisis()` | Full API analysis |
| L002 | `temukan_api` | `api::temukan()` | Discover API endpoints |
| L003 | `muat_openapi` | `api::muat_openapi(url)` | Load OpenAPI spec |
| L004 | `muat_swagger` | `api::muat_swagger(url)` | Load Swagger UI |
| L005 | `muat_postman` | `api::muat_postman(f)` | Import Postman collection |
| L006 | `kirim_get` | `api::get(url)` | HTTP GET request |
| L007 | `kirim_post` | `api::post(url, data)` | HTTP POST request |
| L008 | `kirim_put` | `api::put(url, data)` | HTTP PUT request |
| L009 | `kirim_patch` | `api::patch(url, data)` | HTTP PATCH request |
| L010 | `kirim_delete` | `api::delete(url)` | HTTP DELETE request |
| L011 | `kirim_head` | `api::head(url)` | HTTP HEAD request |
| L012 | `kirim_options` | `api::options(url)` | HTTP OPTIONS request |
| L013 | `atur_body_json` | `api::body_json(json)` | Set JSON body |
| L014 | `atur_body_form` | `api::body_form(data)` | Set form body |
| L015 | `atur_body_xml` | `api::body_xml(xml)` | Set XML body |
| L016 | `atur_header_api` | `api::header(k,v)` | Set request header |
| L017 | `atur_auth_api` | `api::auth(jenis, val)` | Set auth |
| L018 | `cek_cors_api` | `api::cors()` | CORS test |
| L019 | `cek_rate_limit_api` | `api::rate_limit()` | Rate limit test |
| L020 | `cek_versi_api` | `api::versi()` | API versioning |
| L021 | `cek_dokumentasi_api` | `api::dokumentasi()` | Find API docs |
| L022 | `fuzz_api_endpoint` | `api::fuzz_endpoint()` | Endpoint fuzzing |
| L023 | `fuzz_api_parameter` | `api::fuzz_param()` | Parameter fuzzing |
| L024 | `fuzz_api_header` | `api::fuzz_header()` | Header fuzzing |
| L025 | `fuzz_api_body` | `api::fuzz_body()` | Body fuzzing |
| L026 | `uji_otorisasi` | `api::otorisasi()` | Authorization test |
| L027 | `uji_broken_auth` | `api::broken_auth()` | Broken auth test |
| L028 | `uji_idor` | `api::idor()` | IDOR test |
| L029 | `uji_mass_assign` | `api::mass_assign()` | Mass assignment |
| L030 | `uji_injection_api` | `api::injection()` | Injection tests |
| L031 | `uji_graphql_inject` | `api::gql_inject()` | GraphQL injection |
| L032 | `uji_websocket_sec` | `api::ws_sec()` | WebSocket security |
| L033 | `analisis_respons_api` | `api::analisis_respons()` | Response analysis |
| L034 | `cek_data_bocor_api` | `api::data_bocor()` | Data exposure in API |
| L035 | `cek_error_api` | `api::error_handling()` | Error message leakage |
| L036 | `cek_metadata_api` | `api::metadata()` | API metadata exposure |
| L037 | `ukur_waktu_api` | `api::waktu_respons()` | API timing |
| L038 | `bandingkan_endpoint` | `api::bandingkan(e1,e2)` | Compare endpoints |
| L039 | `rekam_trafik_api` | `api::rekam_trafik()` | Record API traffic |
| L040 | `putar_trafik_api` | `api::putar_trafik(f)` | Replay traffic |
| L041 | `ekspor_koleksi` | `api::ekspor_koleksi()` | Export as collection |
| L042 | `impor_koleksi` | `api::impor_koleksi(f)` | Import collection |
| L043 | `uji_koleksi` | `api::uji_koleksi(f)` | Test collection |
| L044 | `generate_curl` | `api::generate_curl()` | Generate curl command |
| L045 | `generate_python_req` | `api::gen_python()` | Generate Python requests |
| L046 | `generate_js_fetch` | `api::gen_js()` | Generate JS fetch |
| L047 | `skor_keamanan_api` | `api::skor()` | API security score |
| L048 | `laporan_api` | `api::laporan()` | API report |
| L049 | `simpan_api` | `api::simpan()` | Save API results |
| L050 | `export_json_api` | `api::export_json()` | Export JSON |
| L051 | `cek_jwt_api` | `api::jwt()` | JWT in API |
| L052 | `cek_oauth_api` | `api::oauth()` | OAuth flow test |
| L053 | `cek_apikey_header` | `api::apikey_header()` | API key in header |
| L054 | `cek_apikey_query` | `api::apikey_query()` | API key in query |
| L055 | `cek_apikey_bocor` | `api::apikey_bocor()` | API key leaked |
| L056 | `cek_ssl_pinning` | `api::ssl_pinning()` | SSL pinning check |
| L057 | `cek_content_type` | `api::content_type()` | Content-Type validation |
| L058 | `cek_accept_header` | `api::accept()` | Accept header test |
| L059 | `ringkasan_api` | `api::ringkasan()` | API summary |
| L060 | `bantu_api` | `api::bantuan()` | Help modul API |

---

## 15. MODUL M – FORENSIK DIGITAL

**Crate:** `indosec-forensic`

### 15.1 Shortcut Perintah Modul M (60 shortcut)

| # | Perintah Indonesia | Fungsi Rust | Keterangan |
|---|---|---|---|
| M001 | `forensik` | `forensic::mulai()` | Forensik digital |
| M002 | `tangkap_snapshot` | `forensic::snapshot()` | Snapshot halaman saat ini |
| M003 | `bandingkan_snapshot` | `forensic::diff_snapshot(s1,s2)` | Diff dua snapshot |
| M004 | `deteksi_perubahan_konten` | `forensic::perubahan_konten()` | Detect content changes |
| M005 | `hash_halaman` | `forensic::hash_halaman()` | SHA-256 halaman |
| M006 | `hash_file_remote` | `forensic::hash_remote(url)` | Hash file remote |
| M007 | `verifikasi_integritas` | `forensic::verifikasi()` | Verify integrity |
| M008 | `timeline_scan` | `forensic::timeline()` | Scan timeline |
| M009 | `rekonstruksi_sesi` | `forensic::rekonstruksi()` | Session reconstruction |
| M010 | `analisis_trafik_log` | `forensic::analisis_log()` | Traffic log analysis |
| M011 | `parse_access_log` | `forensic::parse_access_log(f)` | Parse Apache/Nginx log |
| M012 | `parse_error_log` | `forensic::parse_error_log(f)` | Parse error log |
| M013 | `cari_anomali_log` | `forensic::anomali_log()` | Anomaly detection |
| M014 | `deteksi_serangan_log` | `forensic::serangan_log()` | Attack pattern in log |
| M015 | `hitung_ip_log` | `forensic::hitung_ip()` | IP frequency |
| M016 | `top_ip` | `forensic::top_ip(n)` | Top N IP |
| M017 | `top_url` | `forensic::top_url(n)` | Top N URL accessed |
| M018 | `top_user_agent` | `forensic::top_ua(n)` | Top N User-Agent |
| M019 | `cari_bot_log` | `forensic::cari_bot()` | Bot detection in log |
| M020 | `cari_scanner_log` | `forensic::cari_scanner()` | Scanner in log |
| M021 | `cari_bruteforce_log` | `forensic::bruteforce_log()` | Brute force in log |
| M022 | `cari_404_log` | `forensic::log_404()` | 404 pattern |
| M023 | `cari_500_log` | `forensic::log_500()` | 500 error pattern |
| M024 | `cari_injection_log` | `forensic::injection_log()` | Injection attempt |
| M025 | `cari_xss_log` | `forensic::xss_log()` | XSS attempt in log |
| M026 | `cari_sqli_log` | `forensic::sqli_log()` | SQLi attempt |
| M027 | `cari_path_traversal_log` | `forensic::traversal_log()` | Path traversal in log |
| M028 | `analisis_waktu_log` | `forensic::waktu_log()` | Temporal analysis |
| M029 | `peta_panas_akses` | `forensic::heatmap()` | Access heatmap |
| M030 | `geografis_ip_log` | `forensic::geo_log()` | Geo map dari log |
| M031 | `rekam_sesi_lengkap` | `forensic::rekam_lengkap()` | Full session recording |
| M032 | `analisis_javascript` | `forensic::analisis_js()` | JS static analysis |
| M033 | `analisis_deobfuscasi` | `forensic::deobfuscasi()` | Deobfuscate JS |
| M034 | `analisis_wasm` | `forensic::wasm()` | WebAssembly analysis |
| M035 | `ekstrak_string_binary` | `forensic::strings_binary(f)` | Strings from binary |
| M036 | `analisis_header_forensik` | `forensic::header_forensik()` | Forensic header analysis |
| M037 | `timeline_sertifikat` | `forensic::timeline_ssl()` | SSL certificate timeline |
| M038 | `riwayat_dns` | `forensic::riwayat_dns()` | DNS history |
| M039 | `cek_cache_poisoning_evid` | `forensic::cache_poison_evid()` | Cache poisoning evidence |
| M040 | `analisis_redirect_forensik` | `forensic::redirect_forensik()` | Redirect chain forensic |
| M041 | `ekstrak_ioc` | `forensic::ioc()` | Extract IOCs |
| M042 | `korelasi_ioc` | `forensic::korelasi_ioc()` | Correlate IOCs |
| M043 | `laporan_insiden` | `forensic::laporan_insiden()` | Incident report |
| M044 | `ekspor_ioc_json` | `forensic::ekspor_ioc()` | Export IOC JSON |
| M045 | `ekspor_stix` | `forensic::stix()` | STIX 2.1 export |
| M046 | `analisis_metadata_gambar` | `forensic::img_meta()` | Image metadata |
| M047 | `ekstrak_exif_url` | `forensic::exif_url(url)` | EXIF from URL |
| M048 | `analisis_pdf_forensik` | `forensic::pdf_forensik(url)` | PDF forensic |
| M049 | `cek_steganografi` | `forensic::stegano()` | Steganography check |
| M050 | `hash_md5_file` | `forensic::hash_md5_file(f)` | MD5 local file |
| M051 | `hash_sha256_file` | `forensic::hash_sha256_file(f)` | SHA-256 local file |
| M052 | `bandingkan_file` | `forensic::bandingkan_file(f1,f2)` | Compare two files |
| M053 | `cek_magic_bytes` | `forensic::magic_bytes(f)` | File magic bytes |
| M054 | `identifikasi_jenis_file` | `forensic::jenis_file(f)` | Identify file type |
| M055 | `simpan_forensik` | `forensic::simpan()` | Save forensic data |
| M056 | `ekspor_forensik_json` | `forensic::ekspor_json()` | Export forensic JSON |
| M057 | `skor_insiden` | `forensic::skor_insiden()` | Incident severity score |
| M058 | `preservasi_bukti` | `forensic::preservasi()` | Evidence preservation |
| M059 | `ringkasan_forensik` | `forensic::ringkasan()` | Forensic summary |
| M060 | `bantu_forensik` | `forensic::bantuan()` | Help modul forensik |

---

## 16. MODUL N – TERMUX INTEGRATION

**Crate:** `indosec-termux`

### 16.1 Shortcut Perintah Modul N (60 shortcut)

| # | Perintah Indonesia | Fungsi Rust | Keterangan |
|---|---|---|---|
| N001 | `termux_info` | `termux::info()` | Info Termux environment |
| N002 | `notif_termux` | `termux::notif(msg)` | Termux notification |
| N003 | `notif_selesai_termux` | `termux::notif_selesai()` | Notif saat scan selesai |
| N004 | `getar_termux` | `termux::vibrate()` | Vibrate device |
| N005 | `getar_selesai` | `termux::getar_selesai()` | Vibrate on complete |
| N006 | `suara_termux` | `termux::suara(file)` | Play sound |
| N007 | `suara_alert` | `termux::suara_alert()` | Alert sound |
| N008 | `buka_url_browser` | `termux::buka_url(url)` | Open URL in browser |
| N009 | `bagikan_laporan_termux` | `termux::bagikan(f)` | Share file via Termux |
| N010 | `salin_ke_clipboard` | `termux::clipboard(teks)` | Copy to clipboard |
| N011 | `tempel_dari_clipboard` | `termux::tempel()` | Paste from clipboard |
| N012 | `cek_baterai` | `termux::baterai()` | Battery status |
| N013 | `cek_wifi` | `termux::wifi()` | WiFi info |
| N014 | `cek_jaringan_hp` | `termux::jaringan_hp()` | Mobile network info |
| N015 | `cek_lokasi` | `termux::lokasi()` | GPS location |
| N016 | `cek_sensor` | `termux::sensor()` | Device sensors |
| N017 | `foto_kamera` | `termux::foto()` | Take photo (evidence) |
| N018 | `simpan_di_storage` | `termux::simpan_storage(f)` | Save to Android storage |
| N019 | `muat_dari_storage` | `termux::muat_storage(f)` | Load from storage |
| N020 | `buka_file_manager` | `termux::file_manager()` | Open file manager |
| N021 | `cek_izin_termux` | `termux::cek_izin()` | Termux permissions |
| N022 | `minta_izin` | `termux::minta_izin(p)` | Request permission |
| N023 | `mode_hemat_baterai` | `termux::hemat_baterai()` | Battery saver mode |
| N024 | `mode_layar_nyala` | `termux::layar_nyala()` | Keep screen on |
| N025 | `cek_suhu_device` | `termux::suhu()` | Device temperature |
| N026 | `widget_termux` | `termux::widget()` | Termux widget shortcut |
| N027 | `task_termux` | `termux::task()` | Background task |
| N028 | `sinkronisasi_waktu` | `termux::sinkron_waktu()` | Time sync NTP |
| N029 | `cek_ntp` | `termux::ntp()` | NTP server check |
| N030 | `timezone_info` | `termux::timezone()` | Timezone info |
| N031 | `aktifkan_ssh` | `termux::ssh_on()` | Enable SSH server |
| N032 | `nonaktifkan_ssh` | `termux::ssh_off()` | Disable SSH |
| N033 | `cek_port_termux` | `termux::port()` | Check open ports |
| N034 | `install_tool` | `termux::install(pkg)` | Install Termux package |
| N035 | `update_termux` | `termux::update()` | Update packages |
| N036 | `cek_ruang_termux` | `termux::ruang()` | Storage space |
| N037 | `cek_versi_termux` | `termux::versi()` | Termux version |
| N038 | `log_termux` | `termux::log()` | Termux log |
| N039 | `monitor_resource` | `termux::monitor_res()` | Resource monitoring |
| N040 | `otomasi_wake` | `termux::otomasi_wake()` | Wake on scan |
| N041 | `jadwal_termux` | `termux::jadwal(waktu, cmd)` | Schedule with Termux |
| N042 | `boot_script` | `termux::boot_script(f)` | Run on boot |
| N043 | `backup_termux` | `termux::backup()` | Backup Termux |
| N044 | `restore_termux` | `termux::restore(f)` | Restore Termux |
| N045 | `profil_daya` | `termux::profil_daya()` | Power profile |
| N046 | `mode_pesawat` | `termux::mode_pesawat()` | Airplane mode check |
| N047 | `cek_hotspot` | `termux::hotspot()` | Mobile hotspot |
| N048 | `info_sim` | `termux::sim_info()` | SIM card info |
| N049 | `kecepatan_internet` | `termux::speed_test()` | Internet speed test |
| N050 | `ping_gateway` | `termux::ping_gateway()` | Ping default gateway |
| N051 | `cek_dns_termux` | `termux::dns_termux()` | DNS server check |
| N052 | `flush_dns_termux` | `termux::flush_dns()` | Flush DNS cache |
| N053 | `restart_wifi` | `termux::restart_wifi()` | Restart WiFi |
| N054 | `mode_offline` | `termux::offline()` | Offline mode check |
| N055 | `cek_vpn_termux` | `termux::vpn()` | VPN status |
| N056 | `info_android` | `termux::android_info()` | Android version info |
| N057 | `cek_root` | `termux::cek_root()` | Check root access |
| N058 | `info_build` | `termux::build_info()` | Build properties |
| N059 | `ringkasan_termux` | `termux::ringkasan()` | Termux summary |
| N060 | `bantu_termux` | `termux::bantuan()` | Help Termux |

---

## 17. TABEL SHORTCUT LENGKAP (1000 FITUR)

### Ringkasan Per Modul

| Modul | Nama | Jumlah Shortcut |
|---|---|---|
| A | Recon & Informasi Situs | 100 |
| B | Scan Port & Direktori | 120 |
| C | Uji Form & Input | 80 |
| D | Analisis Konten & NLP | 60 |
| E | Keamanan Jaringan | 80 |
| F | Analisis Kriptografi & TLS | 60 |
| G | OSINT & Footprinting | 80 |
| H | Logging & Reporting | 80 |
| I | Konfigurasi & Sistem | 80 |
| J | CLI Interaktif & UX | 60 |
| K | Otomasi & Scripting | 60 |
| L | Analisis API & REST | 60 |
| M | Forensik Digital | 60 |
| N | Termux Integration | 60 |
| **Total** | | **1.000** |

### Daftar Shortcut Global (Quick Reference)

Berikut adalah **semua 1.000 shortcut** dalam format quick reference. Setiap shortcut dapat diketik langsung di prompt IndoSec CLI.

```
MODUL A (Recon): informasi, kumpulkan_info, cek_server, cek_framework,
  cek_cms, cek_teknologi, analisis_meta, cek_judul, cek_bahasa,
  ukur_waktu_respons, cek_ukuran, cek_redirect, cek_canonical,
  cek_opengraph, cek_robots, cek_sitemap, ekstrak_link,
  ekstrak_link_luar, ekstrak_resource, cek_link_rusak, peta_halaman,
  cek_form_action, cek_target_blank, cek_mixed_content, cek_cdn,
  rasio_link, cek_hsts, cek_csp, analisis_csp, cek_xframe, cek_xcontent,
  cek_referrer, cek_permissions, cek_xss_header, cek_coop, cek_corp,
  cek_coep, skor_keamanan_header, rekomendasi_header, cek_tls, cek_cipher,
  cek_sertifikat, cek_kadaluarsa_ssl, cek_issuer, cek_san, cek_self_signed,
  cek_wildcard, cek_ocsp, cek_ct_log, hitung_hari_ssl, crawl_situs,
  crawl_dalam, crawl_lambat, crawl_cepat, buat_sitemap, cek_favicon,
  cek_apple_touch, cek_manifest, cek_service_worker, cek_cookie, cek_cors,
  cek_dns, cek_a_record, cek_mx_record, cek_txt_record, cek_spf, cek_dkim,
  cek_dmarc, cek_ns, cek_whois, cek_registrar, cek_asn, cek_ip,
  cek_geolokasi, cek_hosting, deteksi_waf, deteksi_cdn_jenis,
  cek_load_balancer, cek_email_situs, cek_nomor_telp, cek_media_sosial,
  analisis_keyword, cek_komentar_html, cek_debug_info, cek_path_sensitif,
  cek_backup_file, cek_git_exposed, cek_env_exposed, cek_config_exposed,
  cek_log_exposed, fingerprint_penuh, bandingkan_target, simpan_info,
  export_json_info, export_csv_info, export_xml_info, ringkasan_info,
  cetak_info, ulangi_info, bantu_info

MODUL B (Scanner): pindai, pindai_port, pindai_port_tcp, pindai_port_udp,
  pindai_port_cepat, pindai_port_lambat, pindai_port_range,
  pindai_port_custom, pindai_port_penting, pindai_port_web,
  pindai_port_database, pindai_port_remote, pindai_port_email,
  pindai_port_file, ambil_banner, deteksi_layanan, atur_timeout_scan,
  atur_thread_scan, highlight_port, simpan_scan_port, export_json_port,
  export_csv_port, ringkasan_port, pindai_direktori, pindai_dir_wordlist,
  pindai_dir_rekursif, pindai_dir_cepat, pindai_dir_lambat,
  pindai_dir_ekstensi, pindai_php, pindai_asp, pindai_jsp, pindai_env_file,
  pindai_backup, pindai_config_file, pindai_admin_panel, pindai_api_endpoint,
  pindai_upload_dir, filter_200, filter_301, filter_403, filter_500,
  filter_status_custom, filter_ukuran_min, filter_ukuran_max,
  abaikan_wildcard, ikuti_redirect, atur_req_per_detik, atur_user_agent,
  atur_header_scan, dump_konten, simpan_scan_dir, export_json_dir,
  export_csv_dir, ringkasan_dir, pindai_multi_target, antrian_target,
  tambah_antrian, hapus_antrian, kosongkan_antrian, ekspor_antrian,
  impor_antrian, jeda_scan, lanjut_scan, hentikan_scan, status_scan,
  persentase_scan, estimasi_waktu, pindai_subdomain, pindai_subdomain_wordlist,
  pindai_subdomain_cepat, validasi_subdomain, cek_vhost, pindai_parameter,
  temukan_parameter, fuzz_parameter, pindai_header, pindai_method,
  cek_put_aktif, cek_delete_aktif, cek_trace_aktif, cek_options,
  pindai_websocket, pindai_graphql, introspeksi_graphql, pindai_swagger,
  pindai_jenkins, pindai_phpmyadmin, pindai_wordpress, pindai_joomla,
  pindai_drupal, pindai_laravel, pindai_django, pindai_flask, pindai_spring,
  pindai_actuator, cek_server_status, cek_nginx_status, ringkasan_pindai,
  bantu_pindai, pindai_s3, pindai_gcs, pindai_azure_blob, cek_cors_wildcard,
  pindai_path_traversal, pindai_ssrf, pindai_open_redirect, pindai_crlf,
  pindai_host_injection, pindai_clickjacking, pindai_cache_poisoning,
  pindai_request_smuggling, pindai_blueprint, pindai_kompresi, cek_waf_bypass,
  ukur_bandwidth, cek_cache_header, cek_etag, skor_pindai, laporan_pindai

[... dst untuk semua 1000 shortcut ...]
```

---

## 18. SPESIFIKASI TEKNIS RUST

### 18.1 Error Handling

```rust
// crates/indosec-core/src/error.rs
use thiserror::Error;

#[derive(Error, Debug)]
pub enum IndoSecError {
    #[error("Koneksi gagal ke {url}: {sumber}")]
    KoneksGagal { url: String, sumber: reqwest::Error },

    #[error("URL tidak valid: {url}")]
    UrlTidakValid { url: String },

    #[error("Target tidak ditemukan dalam konfigurasi")]
    TargetTidakDitemukan,

    #[error("Timeout setelah {ms}ms untuk {operasi}")]
    Timeout { ms: u64, operasi: String },

    #[error("Izin ditolak: {pesan}")]
    IzinDitolak { pesan: String },

    #[error("File tidak ditemukan: {path}")]
    FileTidakDitemukan { path: String },

    #[error("Format JSON tidak valid: {sumber}")]
    JsonTidakValid { sumber: serde_json::Error },

    #[error("Peringatan legal: {pesan}")]
    PeringatanLegal { pesan: String },

    #[error("Fitur tidak tersedia: {fitur}")]
    FiturTidakTersedia { fitur: String },

    #[error("Kesalahan I/O: {sumber}")]
    IoError {
        #[from]
        sumber: std::io::Error,
    },
}
```

### 18.2 Target Struct

```rust
// crates/indosec-core/src/target.rs
use serde::{Deserialize, Serialize};
use url::Url;
use chrono::{DateTime, Utc};

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct Target {
    pub id: u32,
    pub nama: String,
    pub url: String,
    pub catatan: String,
    pub izin_legal: bool,           // Wajib true untuk operasi aktif
    pub tanggal_ditambah: DateTime<Utc>,
    pub terakhir_discan: Option<DateTime<Utc>>,
    pub tag: Vec<String>,
    pub konfigurasi: TargetConfig,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct TargetConfig {
    pub timeout_ms: u64,
    pub user_agent: String,
    pub auth: Option<AuthConfig>,
    pub headers_tambahan: std::collections::HashMap<String, String>,
    pub ikuti_redirect: bool,
    pub maks_redirect: u8,
    pub verifikasi_ssl: bool,
}

impl Target {
    pub fn validasi(&self) -> Result<Url, IndoSecError> {
        if !self.izin_legal {
            return Err(IndoSecError::PeringatanLegal {
                pesan: format!(
                    "Target '{}' belum memiliki flag izin legal. \
                     Set izin_legal=true untuk melanjutkan.",
                    self.nama
                ),
            });
        }
        Url::parse(&self.url).map_err(|_| IndoSecError::UrlTidakValid {
            url: self.url.clone(),
        })
    }
}
```

### 18.3 Async Runtime Pattern

```rust
// src/main.rs
use tokio::runtime::Runtime;

fn main() -> anyhow::Result<()> {
    let runtime = Runtime::new()?;
    runtime.block_on(async_main())
}

async fn async_main() -> anyhow::Result<()> {
    let config = Config::muat_atau_default()?;
    let mut cli = DashboardCLI::baru(config).await?;
    cli.jalankan().await
}
```

### 18.4 HTTP Client Pool

```rust
// crates/indosec-recon/src/client.rs
use reqwest::{Client, ClientBuilder};
use std::time::Duration;

pub fn buat_client(config: &TargetConfig) -> Result<Client, IndoSecError> {
    ClientBuilder::new()
        .timeout(Duration::from_millis(config.timeout_ms))
        .user_agent(&config.user_agent)
        .redirect(
            if config.ikuti_redirect {
                reqwest::redirect::Policy::limited(config.maks_redirect as usize)
            } else {
                reqwest::redirect::Policy::none()
            }
        )
        .danger_accept_invalid_certs(!config.verifikasi_ssl)
        .build()
        .map_err(|e| IndoSecError::KoneksGagal {
            url: "N/A".into(),
            sumber: e,
        })
}
```

---

## 19. SKEMA DATA & FORMAT LOG

### 19.1 Format Log Standar

```
=== [TIMESTAMP] [MODUL] [TARGET] ===
[LEVEL] Pesan
[DATA] Key: Value
[SKOR] N/100
[DURASI] Xms
=== SELESAI ===
```

Contoh:
```
=== [2026-01-15T08:30:00+07:00] [RECON] [https://contoh.co.id] ===
INFO  Mulai pengumpulan informasi...
DATA  Server: nginx/1.18.0
DATA  Framework: Laravel 10.x
DATA  TLS: 1.3 (TLS_AES_256_GCM_SHA384)
WARN  X-Frame-Options tidak ditemukan
WARN  CSP header tidak dikonfigurasi
INFO  Form login ditemukan: /login
SKOR  Keamanan Header: 45/100
SKOR  TLS: 90/100
DURASI 2341ms
=== SELESAI ===
```

### 19.2 Skema JSON Laporan

```json
{
  "meta": {
    "versi_indosec": "1.0.0",
    "timestamp": "2026-01-15T08:30:00+07:00",
    "durasi_ms": 2341,
    "operator": "Pengguna",
    "izin_legal": true
  },
  "target": {
    "url": "https://contoh.co.id",
    "ip": "203.0.113.10",
    "server": "nginx/1.18.0",
    "framework": "Laravel 10.x"
  },
  "skor": {
    "global": 67,
    "header": 45,
    "tls": 90,
    "form": 60,
    "konten": 72
  },
  "temuan": [
    {
      "id": "HDR-001",
      "modul": "recon",
      "tingkat": "sedang",
      "judul": "X-Frame-Options Tidak Ditemukan",
      "deskripsi": "Situs rentan terhadap clickjacking.",
      "rekomendasi": "Tambahkan header: X-Frame-Options: DENY",
      "referensi": "OWASP A05:2021"
    }
  ],
  "statistik": {
    "total_temuan": 12,
    "kritis": 0,
    "tinggi": 2,
    "sedang": 5,
    "rendah": 5
  }
}
```

---

## 20. ROADMAP PENGEMBANGAN

### v0.1.0 (MVP) – Q1 2026
- Modul A (Recon dasar): 30 fitur
- Modul B (Port scan + dir scan): 30 fitur
- Modul C (Form detection): 20 fitur
- Modul H (Logging dasar): 20 fitur
- Modul I (Config dasar): 20 fitur
- Modul J (CLI interaktif): 20 fitur
- Total: ~140 fitur aktif

### v0.5.0 – Q2 2026
- Modul A lengkap (100 fitur)
- Modul B lengkap (120 fitur)
- Modul C lengkap (80 fitur)
- Modul N (Termux integration): 60 fitur
- Total kumulatif: ~400 fitur

### v0.8.0 – Q3 2026
- Modul D, E, F aktif
- Modul G (OSINT dasar)
- Modul K (Otomasi dasar)
- Total kumulatif: ~700 fitur

### v1.0.0 (Stable) – Q4 2026
- Semua 1.000 shortcut aktif
- Test coverage ≥ 80%
- Dokumentasi lengkap
- Binary tersedia untuk ARM64 Termux

---

*Dokumen ini adalah spesifikasi teknis resmi IndoSec CLI v1.0.0.*
*Terakhir diperbarui: 2026*
