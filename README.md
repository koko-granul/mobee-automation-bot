# 🐝 Mobee Trading Platform

> Platform otomatisasi perdagangan aset kripto (*cryptocurrency trading automation*) yang berfokus pada strategi bot terkonfigurasi, manajemen portofolio terintegrasi, pemantauan pasar secara *real-time*, dan interaksi berbasis kecerdasan buatan (*AI-assisted interaction*).

---

## 📌 Ringkasan Eksekutif (Overview)

**Mobee Trading Platform** merupakan rancangan sistem otomasi perdagangan aset kripto modular yang mengintegrasikan berbagai komponen inti arsitektur perangkat lunak finansial:

- 🌐 **Antarmuka Web (*Web Interface*)**: Dasbor manajemen, konfigurasi bot, dan analitik portofolio.
- 🤖 **Mesin Bot (*Trading Bot Engine*)**: Pengelolaan siklus hidup (*lifecycle*) dan orkestrasi strategi transaksi otomatis.
- 🧠 **Asisten AI (*AI Assistant*)**: Antarmuka interaksi bahasa alami (*natural language*) dengan pemahaman konteks sistem.
- 🔌 **Mobee API Gateway**: Lapisan komunikasi terpusat antara antarmuka pengguna, layanan AI, dan mesin eksekusi.
- ⚙️ **Mesin Perdagangan (*Trading Engine*)**: Komponen inti pemrosesan sinyal pasar, evaluasi logika, dan orkestrasi instruksi.
- 📊 **Agregator Data Pasar (*Market Data Aggregator*)**: Penyedia data harga, volume, serta buku pesanan (*order book*) secara *real-time*.
- 📤 **Lapisan Eksekusi (*Order Execution Layer*)**: Penanganan pengiriman, validasi, dan pelacakan pesanan ke bursa (*exchange*).
- 💼 **Manajemen Portofolio (*Portfolio Management*)**: Pelacakan saldo, valuasi aset, alokasi modal, serta kalkulasi laba-rugi (*P/L*).
- 💾 **Lapisan Data & Persistensi (*Data & History Layer*)**: Penyimpanan relasional dan riwayat transaksi auditabel.
- 🛡️ **Kontrol Risiko & Keamanan (*Safety & Risk Control*)**: Batasan modal (*capital guardrails*), mitigasi kerugian, dan mekanisme pemutus darurat (*circuit breaker / emergency stop*).
- 🔔 **Sistem Notifikasi (*Notification System*)**: Notifikasi kejadian penting (*event-driven alerts*) melalui multi-kanal.

Dokumen ini memuat **rancangan konsep dan arsitektur sistem** komprehensif sebagai panduan teknis pengembangan platform Mobee.

---

# 🧭 Gambaran Umum Aliran Sistem (System Overview)

Secara konseptual, arsitektur aliran data dan koordinasi antar-komponen Mobee berjalan sebagai berikut:

```text
                    👤 PENGGUNA (USER)
                            │
                            ▼
                  🌐 ANTARMUKA WEB
                            │
               ┌────────────┴────────────┐
               ▼                         ▼
        📊 DATA PASAR             💼 PORTOFOLIO
               │                         │
               └────────────┬────────────┘
                            ▼
                     🤖 MANAJEMEN BOT
                            │
                            ▼
                    🧠 ASISTEN AI
                            │
                            ▼
                    🔌 MOBEE API
                            │
                            ▼
                ⚙️ MESIN PERDAGANGAN
                            │
               ┌────────────┼────────────┐
               ▼            ▼            ▼
          DATA PASAR   MESIN BOT     EKSEKUSI
                                         │
                                         ▼
                                  🏦 BURSA / EXCHANGE
                                         │
                                         ▼
                                   💼 PORTOFOLIO
                                         │
                                         ▼
                                  💾 BASIS DATA
                                         │
                                         ▼
                             📜 RIWAYAT & LAPORAN AUDIT
```

Arsitektur sistem Mobee tidak dirancang sebagai kumpulan modul monolitik yang terisolasi. Setiap modul memiliki tanggung jawab (*single responsibility*) yang terdefinisi secara ketat serta berinteraksi melalui antarmuka pertukaran data yang terstandarisasi.

---

# 🏗️ Arsitektur Tingkat Tinggi (High-Level Architecture)

Struktur hierarki modular platform Mobee terbagi ke dalam lapisan-lapisan independen:

```text
Mobee Trading Platform
│
├── 🌐 Web Interface (Dasbor Pengguna & Monitoring)
│
├── 🤖 AI Assistant (Interaksi Kontekstual & Inferensi Data)
│
├── 🔌 Mobee API Gateway (REST & WebSocket Orchestration)
│
├── ⚙️ Trading Engine (Komponen Inti Otomasi)
│   ├── Market Data Aggregator
│   ├── Bot Engine
│   ├── Strategy Engine
│   ├── Task Scheduler
│   ├── State Manager
│   └── Execution Layer
│
├── 💼 Portfolio Engine (Pengelolaan Nilai & Alokasi Modal)
│
├── 💾 Data Persistence Layer (Basis Data Transaksional & Riwayat)
│
├── 🛡️ Safety & Risk Layer (Guardrails, Limitasi Modal, & Circuit Breakers)
│
└── 🔔 Notification Layer (Webhooks, Telegram, & Antarmuka UI)
```

---

# 🌐 Lapisan Antarmuka Web (Web Interface)

Antarmuka Web merupakan sarana interaksi utama pengguna untuk memantau performa dan mengontrol parameter sistem secara visual.

## Dasbor Utama (Dashboard)

Dasbor utama menyajikan indikator performa utama (*key performance indicators*) secara konsolidasi:

- **Saldo Kas Tersedia (*Available Cash Balance*)**: Likuiditas yang siap digunakan.
- **Valuasi Portofolio (*Total Portfolio Valuation*)**: Nilai agregat dari kas dan aset aktif.
- **Nilai Kepemilikan Aset (*Asset Holdings Value*)**: Nilai pasar berjalan untuk instrumen aset kripto.
- **Laba / Rugi Berjalan (*Unrealized & Realized Profit/Loss*)**: Metrik profitabilitas historis dan terkini.
- **Kinerja Portofolio (*Portfolio Performance Benchmark*)**: Grafik pertumbuhan ekuitas.
- **Log Aktivitas Terkini (*Recent Activity Logs*)**: Catatan transaksi dan perubahan status operasional.
- **Status Operasional Bot (*Bot Operational Status*)**: Pemantauan status seluruh instans bot yang aktif.

Indikator status siklus operasional bot:

```text
🟢 Active     : Bot sedang aktif memantau kondisi pasar
🟡 Waiting    : Bot dalam antrean evaluasi interval atau pemenuhan sinyal
🔵 Completed  : Siklus strategi bot selesai memenuhi target
🔴 Stopped    : Bot dihentikan secara manual oleh pengguna
⚠️ Error      : Terjadi anomali teknis atau kegagalan eksekusi order
```

---

## 📊 Modul Pasar (Market Explorer)

Menyediakan representasi komprehensif pergerakan instrumen perdagangan kripto:

- **Daftar Pasangan Perdagangan (*Trading Pairs List*)**: Katalog pasangan aset yang didukung.
- **Informasi Harga (*Price Feeds*)**: Harga terakhir (*last traded price*), *bid*, dan *ask*.
- **Volatilitas & Perubahan Harga (*Price Action & Percentage Change*)**: Fluktuasi rentang 24 jam.
- **Volume Transaksi (*Trading Volume*)**: Likuiditas pasar riil.
- **Visualisasi Grafik (*Candlestick Charting*)**: Representasi tren berbasis interval waktu.
- **Metadata Aset (*Asset Specification*)**: Batas minimum order (*lot size*), presisi desimal, dan batasan bursa.

Data pasar ini berfungsi sebagai input primer (*primary telemetry*) yang disuplai ke Mesin Perdagangan.

---

## ⭐ Daftar Pantau (Watchlist)

Fitur kustomisasi pemantauan instrumen strategis:

- Pengelompokan aset prioritas (*starred / favorite assets*).
- Pemantauan metrik pergerakan harga secara terkonsentrasi.
- Visualisasi grafik tren ringkas (*sparklines*).

---

## 💼 Modul Portofolio (Portfolio Management)

Menyajikan metrik kepemilikan dan performa investasi secara terperinci:

- **Saldo Kas (*Liquid Balance*)**: Dana yang belum teralokasi.
- **Kepemilikan Aset (*Asset Holdings*)**: Jumlah unit per instrumen kripto.
- **Harga Rata-rata Pembelian (*Average Purchase Price / Cost Basis*)**: Rerata tertimbang harga akumulasi.
- **Valuasi Terkini (*Current Market Value*)**: Nilai aset berdasarkan harga pasar saat ini.
- **Realized P/L**: Akumulasi keuntungan/kerugian dari posisi yang telah ditutup.
- **Unrealized P/L**: Potensi keuntungan/kerugian mengambang dari posisi terbuka.
- **Distribusi Alokasi Aset (*Asset Allocation Breakdown*)**: Diversifikasi modal dalam portofolio.

---

## 🤖 Modul Manajemen Bot (Bot Operations)

Antarmuka terpusat untuk konfigurasi, orkestrasi, dan pemantauan bot:

- **Bot Aktif (*Active Bots*)**: Instans bot yang sedang berjalan.
- **Bot Selesai (*Completed Bots*)**: Riwayat instans bot yang telah menuntaskan targetnya.
- **Pembuatan Bot (*Bot Provisioning*)**: Formulir konfigurasi parameter strategi baru.
- **Detail & Telemetri Bot (*Bot Deep Metrics*)**: Parameter, metrik transaksi, dan log eksekusi instans.
- **Visualisasi Progres (*Progress Tracking*)**: Persentase pencapaian modal yang dialokasikan terhadap target profit.
- **Riwayat Siklus Bot (*Bot Lifecycle History*)**: Rekam jejak eksekusi dari inisiasi hingga terminasi.

---

## 📋 Manajemen Pesanan (Order Management)

Modul audit dan pemantauan transaksi perdagangan:

- **Pesanan Terbuka (*Open Orders*)**: Pesanan limit yang berada pada buku pesanan bursa.
- **Pesanan Tertunda (*Pending Orders*)**: Instruksi yang sedang diverifikasi sebelum diteruskan ke bursa.
- **Pesanan Terpenuhi (*Filled Orders*)**: Pesanan yang telah berhasil dieksekusi penuh.
- **Pesanan Dibatalkan (*Cancelled Orders*)**: Instruksi yang dibatalkan oleh pengguna atau sistem risiko.
- **Buku Riwayat Pesanan (*Order Audit Ledger*)**: Rekaman historis komprehensif beserta stempel waktu.

---

## 📜 Riwayat Transaksi & Audit (Transaction Ledger)

Penyimpanan catatan audit permanen atas semua mutasi akun dan transaksi:

- Eksekusi Pembelian (*BUY Records*).
- Eksekusi Penjualan (*SELL Records*).
- Realisasi Keuntungan/Kerugian (*Realized P/L Settlement*).
- Log Perubahan Status Bot (*Bot State Transitions*).
- Mutasi dan Aktivitas Akun (*Account Mutation & Security Logs*).

---

## ⚙️ Pengaturan Sistem (System Settings)

Konfigurasi parameter global platform:

- **Manajemen Akun (*Account & Credential Management*)**.
- **Preferensi Pengguna (*User Preferences & Regional Settings*)**.
- **Konektivitas Bursa / API (*Exchange API Key & Secret Management*)**.
- **Konfigurasi Saluran Notifikasi (*Notification Integrations*)**.
- **Pengaturan Keamanan (*Security, IP Whitelisting, & Auth Policy*)**.
- **Parameterisasi Model AI (*AI Model Configuration & Inference Limits*)**.

---

# 🤖 Lapisan Asisten AI (AI Assistant & Context Engine)

Asisten AI berfungsi sebagai antarmuka pemrosesan bahasa alami (*natural language interface*) yang menyederhanakan akses telemetri sistem dan analisis data tanpa mengharuskan navigasi manual antarmuka.

### Contoh Interaksi Pengguna:

```text
"Bagaimana tren likuiditas dan volatilitas BTC dalam 4 jam terakhir?"

"Berapa realisasi laba bersih yang dihasilkan oleh bot akumulasi ETH hari ini?"

"Tampilkan daftar bot yang saat ini berada dalam status penahanan aset (HOLDING)."

"Berapa harga rata-rata pembelian (average entry price) untuk posisi SOL saat ini?"

"Berapa sisa modal cadangan yang tersedia sebelum batas risiko maksimum tercapai?"
```

---

## 🧠 Mesin Konteks AI (AI Context Engine)

Model AI tidak beroperasi secara terisolasi. Seluruh inferensi diperkaya dengan injeksi konteks *real-time*:

```text
               Kueri Pengguna (User Query)
                            +
            Data Telemetri Pasar (Market Telemetry)
                            +
             Status Portofolio (Portfolio State)
                            +
              Kondisi Internal Bot (Bot State)
                            +
             Riwayat Pesanan (Order History Ledger)
                            +
           Batasan Kebijakan Sistem (System Policy)
                            │
                            ▼
              Injeksi Konteks Terpadu
                            │
                            ▼
            Respons Terkalibrasi & Relevan
```

---

## 📊 Analisis Pasar Terbimbing (Guided Market Analysis)

Asisten AI memberikan analisis deskriptif terhadap kondisi pasar:

- Evaluasi tren harga dan momentum pergerakan aset.
- Analisis kedalaman likuiditas dan volume perdagangan.
- Komparasi performa antar-aset dalam portofolio.
- Penjelasan teknis mengenai pembentukan pola harga.

> [!NOTE]
> Asisten AI berperan eksklusif sebagai lapisan analisis data dan penerjemah wawasan kualitatif, **bukan sebagai pengambil keputusan otonom** yang menggantikan logika Mesin Perdagangan (*Trading Engine*).

---

## 💼 Analisis Portofolio & Risiko (Portfolio & Risk Diagnostics)

Asisten AI mengagregasi data portofolio secara komprehensif:

- Evaluasi rasio eksposur risiko terhadap aset tertentu.
- Perhitungan dampak harga rata-rata akumulasi (*cost averaging*).
- Analisis margin keuntungan mengambang (*unrealized gain/loss*).
- Rekomendasi penyesuaian likuiditas kas berdasarkan parameter risiko.

---

## 🤖 Pemantauan Status & Diagnostik Bot

Memberikan visibilitas mendalam atas status operasional bot:

- Integritas parameter konfigurasi instans bot.
- Kondisi siklus hidup terkini (*current state & sub-state*).
- Evaluasi pemenuhan target akumulasi dan likuidasi.
- Rekapitulasi efisiensi modal yang telah digunakan.

---

## ⚙️ Otorisasi Aksi Berbantuan AI (Guarded AI Action Protocol)

Asisten AI dapat memformulasikan rekomendasi tindakan operasional (seperti pembuatan konfigurasi bot, penyesuaian parameter, atau penghentian instans). Namun, seluruh tindakan yang memengaruhi mutasi keuangan atau status eksekusi **wajib melalui otorisasi eksplisit pengguna (*Human-in-the-Loop Verification*)**:

```text
               Asisten AI (AI Assistant)
                          │
                          ▼
            Formulasi Proposal Aksi (Action Proposal)
                          │
                          ▼
             Persetujuan Pengguna (User Approval)
                          │
            ┌─────────────┴─────────────┐
            ▼                           ▼
      DITOLAK (REJECT)            DISETUJUI (APPROVE)
            │                           │
            ▼                           ▼
      Batalkan Aksi             Kirim Instruksi ke
                                   Mobee API
                                        │
                                        ▼
                                Mesin Perdagangan
```

Mekanisme otorisasi ini menjamin bahwa model AI tidak dapat mengeksekusi transaksi moneter secara sepihak tanpa kontrol langsung dari pengguna.

---

# 🔌 Lapisan Mobee API Gateway

Mobee API Gateway merupakan saluran komunikasi terpadu yang memediasi pertukaran data antara Antarmuka Web, Layanan AI, dan Mesin Perdagangan.

```text
            Antarmuka Web                 Asisten AI
                 │                            │
                 └──────────────┬─────────────┘
                                │
                                ▼
                       Mobee API Gateway
                                │
                                ▼
                     Mesin Perdagangan
```

Spesifikasi domain endpoint arsitektural:

### 📊 API Pasar (Market Endpoints)
- `GET /api/v1/market/summary` : Ringkasan data pasar agregat.
- `GET /api/v1/market/ticker` : Data harga *real-time* per pasangan perdagangan.
- `GET /api/v1/market/candles` : Data historis candlestick (OHLCV).
- `GET /api/v1/market/assets` : Spesifikasi instrumen aset kripto.

### 🤖 API Manajemen Bot (Bot Endpoints)
- `GET /api/v1/bots` : Daftar instans bot aktif dan riwayat.
- `POST /api/v1/bots/create` : Inisiasi konfigurasi bot baru.
- `POST /api/v1/bots/:id/start` : Pengaktifan instans bot terkonfigurasi.
- `POST /api/v1/bots/:id/stop` : Penghentian operasional instans bot.
- `GET /api/v1/bots/:id/state` : Telemetri dan status operasional berjalan.
- `GET /api/v1/bots/:id/logs` : Rekaman log eksekusi bot.

### 💼 API Portofolio (Portfolio Endpoints)
- `GET /api/v1/portfolio/balance` : Saldo likuid dan cadangan.
- `GET /api/v1/portfolio/holdings` : Detail kepemilikan instrumen aset.
- `GET /api/v1/portfolio/valuation` : Valuasi agregat portofolio.
- `GET /api/v1/portfolio/pnl` : Metrik realisasi dan potensi laba-rugi.

### 📋 API Pesanan (Order Endpoints)
- `POST /api/v1/orders/buy` : Pengiriman instruksi order beli.
- `POST /api/v1/orders/sell` : Pengiriman instruksi order jual.
- `GET /api/v1/orders/:id/status` : Pengecekan status eksekusi pesanan.
- `DELETE /api/v1/orders/:id` : Pembatalan pesanan aktif di bursa.

> [!NOTE]
> Struktur endpoint di atas merupakan gambaran desain arsitektur konseptual dan dapat disesuaikan pada tahap implementasi spesifikasi OpenAPI/Swagger final.

---

# ⚙️ Mesin Perdagangan (Trading Engine)

Mesin Perdagangan merupakan inti komputasi dan orkestrasi otomatisasi pada platform Mobee.

Tanggung jawab fungsional primer:
1. Mengonsumsi dan memvalidasi umpan data pasar (*stream & polling feeds*).
2. Memuat dan memelihara status internal (*state*) setiap instans bot aktif.
3. Mengevaluasi kondisi parameter terhadap aturan logika pada Mesin Strategi (*Strategy Engine*).
4. Menentukan keputusan operasional (`BUY`, `WAIT`, `SELL`).
5. Mengirimkan instruksi pesanan yang terverifikasi ke Lapisan Eksekusi (*Execution Layer*).
6. Melakukan rekonsiliasi data pasca-eksekusi dan memicu pembaruan pada Mesin Portofolio.

Alur pemrosesan logika:

```text
                  Data Pasar (Market Feeds)
                              +
             Konfigurasi Bot (Bot Configuration)
                              +
                Status Internal (Bot State)
                              │
                              ▼
                     Mesin Perdagangan
                              │
                              ▼
                        Mesin Strategi
                              │
            ┌─────────────────┼─────────────────┐
            ▼                 ▼                 ▼
       AKUISISI (BUY)     MENUNGGU (WAIT)    LIKUIDASI (SELL)
```

---

# 📥 Agregator Data Pasar (Market Data Layer)

Menyediakan infrastruktur data berkualitas tinggi dan rendah latensi untuk kebutuhan komputasi strategi.

Parameter data yang dikelola:
- Harga transaksi terakhir (*Last Traded Price*).
- Persentase perubahan nilai dalam berbagai rentang waktu.
- Volume perdagangan teragregasi.
- Kedalaman buku pesanan (*Order Book Depth / L2 Data*).
- Status likuiditas dan konektivitas bursa.

Pipeline distribusi data:

```text
                  🏦 Bursa Eksternal / Exchange
                                │
                                ▼
                   📥 Agregator Data Pasar
                                │
                                ▼
                     ⚙️ Mesin Perdagangan
```

---

# 🤖 Mesin Bot (Bot Engine)

Mesin Bot mengelola siklus hidup (*lifecycle*), transisi status (*state machine*), dan isolasi proses operasional setiap instans bot secara independen.

Alur operasional internal Mesin Bot:

```text
               1. Pemuatan Konfigurasi (Load Config)
                                │
                                ▼
                 2. Pemuatan Status (Load State)
                                │
                                ▼
                 3. Pembacaan Data Pasar Terkini
                                │
                                ▼
                 4. Evaluasi Kondisi & Parameter
                                │
                                ▼
                 5. Inferensi Mesin Strategi
                                │
                                ▼
                 6. Penentuan Tindakan (Action Logic)
                                │
                                ▼
                 7. Pembaruan Status & Pencatatan Log
```

---

# 🔄 Siklus Hidup Bot (Bot Lifecycle State Machine)

Setiap bot diatur oleh model mesin status berhingga (*finite state machine*) yang ketat:

```text
     [CREATED]
         │
         ▼
      [READY] ◄───────────────┐
         │                    │
         ▼                    │
     [RUNNING]                │
         │                    │
         ▼                    │
     [WAITING] ───────────────┤ (Siklus evaluasi berulang)
         │                    │
         ▼                    │
      [BUYING] ───────────────┤
         │                    │
         ▼                    │
     [HOLDING] ───────────────┘
         │
         ▼
     [SELLING]
         │
         ▼
    [COMPLETED]
```

### Status Anomali & Intervensi:
- **`STOPPED`**: Dihentikan oleh instruksi pengguna atau batas waktu operasional tercapai.
- **`FAILED`**: Mengalami kegagalan validasi atau penolakan pesanan oleh bursa.
- **`ERROR`**: Terjadi gangguan jaringan, kesalahan otentikasi API, atau anomali sistem kritis.

---

# 🤖 Konfigurasi Parameter Bot (Bot Configuration)

Inisiasi bot memerlukan spesifikasi parameter yang terdefinisi secara presisi:

- **Instrumen Aset (*Trading Asset Pair*)**: Pasangan kripto yang diperdagangkan (misal: BTC/IDR).
- **Alokasi Modal Total (*Total Capital Allocation*)**: Batas plafon modal maksimum untuk instans bot.
- **Nominal Per Transaksi Beli (*Order Size per BUY*)**: Jumlah modal per satu siklus akumulasi.
- **Interval Evaluasi (*Evaluation / Cadence Interval*)**: Jarak waktu antar-pemeriksaan kondisi sinyal.
- **Kondisi Sinyal Beli (*BUY Trigger Conditions*)**: Indikator teknikal atau ambang batas penurunan harga.
- **Batas Alokasi Maksimum (*Maximum Exposure Ceiling*)**: Pembatas risiko modal agregat.
- **Strategi Likuidasi (*Sell / Exit Strategy*)**: Aturan penjualan posisi akumulasi.
- **Target Keuntungan (*Profit Target*)**: Persentase margin laba bersih yang diharapkan.
- **Batas Waktu Operasional (*Time Limit / Expiration Window*)**: Jendela waktu aktif bot sebelum terminasi otomatis.

Alur provisioning konfigurasi:

```text
       FORMULASI PARAMETER ──► VALIDASI ATURAN BISNIS ──► STATUS READY ──► BOT AKTIF
```

---

# 🔍 Validasi Konfigurasi & Manajemen Risiko Awal

Sebelum status bot diubah menjadi `RUNNING`, sistem melakukan serangkaian validasi preventif (*pre-flight verification*):

```text
                    Pemeriksaan Parameter Konfigurasi
                                   │
       ┌───────────────────────────┼───────────────────────────┐
       ▼                           ▼                           ▼
Apakah Modal Valid?       Apakah Saldo Kas Cukup?     Apakah Limit Risiko Sah?
       │                           │                           │
       └───────────────────────────┼───────────────────────────┘
                                   │
                                   ▼
                   Hasil Validasi Integritas
                                   │
                    ┌──────────────┴──────────────┐
                    ▼                             ▼
              VALID (LOLOS)               TIDAK VALID (GAGAL)
                    │                             │
                    ▼                             ▼
              Status READY                Tolak Inisiasi &
                    │                     Laporkan Galat ke UI
                    ▼
               Aktifkan Bot
```

---

# 🧠 Mesin Strategi (Strategy Engine)

Mesin Strategi bertugas mengevaluasi metrik analitik kuantitatif untuk menghasilkan sinyal operasional berdasarkan gabungan data:

- Umpan Data Pasar (*Market Telemetry*).
- Konfigurasi Spesifik Bot (*Bot Configuration*).
- Posisi dan Rerata Harga Akumulasi Terkini (*Current Position & Cost Basis*).
- Status Siklus Bot (*Internal State*).

Keluaran keputusan strategi:
- **`BUY`**: Kondisi sinyal terpenuhi dan alokasi modal masih tersedia.
- **`WAIT`**: Kondisi pasar belum memenuhi kriteria aturan transaksi.
- **`SELL`**: Kriteria target laba atau batas proteksi risiko terpenuhi.

> [!IMPORTANT]
> **Pemisahan Peran Strategi dan Eksekusi (*Decoupled Architecture*)**:
> Mesin Strategi murni berfungsi sebagai pengambil keputusan logika kuantitatif (*logic decision maker*), sedangkan eksekusi order ke bursa sepenuhnya ditangani oleh **Lapisan Eksekusi (*Execution Layer*)**. Pemisahan ini memfasilitasi penambahan model strategi baru tanpa mengubah mekanisme transaksi.

---

# 🛒 Logika Akuisisi (BUY Logic) & Evaluasi Berkala (WAIT Logic)

## Logika Pembelian (BUY Logic)
Menentukan waktu dan besaran eksekusi akumulasi posisi berdasarkan kriteria terukur:
- Pemenuhan kriteria sinyal beli (*BUY Condition Evaluation*).
- Verifikasi batas saldo kas yang belum terikat (*Unencumbered Balance Check*).
- Verifikasi plafon modal maksimum instans bot (*Exposure Ceiling*).

```text
                       Umpan Data Pasar Masuk
                                 │
                                 ▼
                     Evaluasi Kriteria Sinyal Beli
                                 │
                 ┌───────────────┴───────────────┐
                 ▼                               ▼
        SYARAT TERPENUHI                BELUM MEMENUHI SYARAT
                 │                               │
                 ▼                               ▼
         Instruksi BUY Ditransmisikan     Status: WAITING
         ke Lapisan Eksekusi              (Lanjutkan Pemantauan)
```

## Logika Menunggu (WAIT Logic)
Status `WAITING` mengindikasikan bahwa instans bot aktif memantau dinamika pasar namun belum mendeteksi kondisi yang selaras dengan parameter strategi.
- **Interval waktu tidak memicu eksekusi otomatis**: Berjalannya interval waktu semata-mata menandai jadwal pengulangan evaluasi kondisi, bukan pemicu langsung transaksi beli.

---

# 🏊 Strategi Akumulasi Penurunan / DCA ("Nyelam" Strategy)

Konsep **"Nyelam"** (istilah umum dalam komunitas perdagangan untuk akumulasi saat tren penurunan) dirancang sebagai **salah satu varian konfigurasi strategi (*Strategy Plugin / Configuration Profile*)**, bukan sebagai modul mesin terpisah (*separate engine*).

```text
                          Mesin Strategi Terpadu
                                    │
            ┌───────────────────────┴───────────────────────┐
            ▼                                               ▼
  Profil Strategi Standar               Profil Akumulasi Bertahap ("Nyelam")
  (Fixed Interval DCA)                  (Dynamic Dip Accumulation)
```

Parameterisasi Strategi Akumulasi Bertahap mencakup:
- **Ambang Batas Penurunan (*Dip Percentage Trigger*)**: Syarat persentase koreksi harga sebelum eksekusi beli berikutnya.
- **Skema Penggandaan Nilai Transaksi (*Martingale / Weighted Allocation*)**: Penyesuaian volume pembelian pada level koreksi tertentu.
- **Interval Pendinginan (*Cooldown Period*)**: Waktu jeda minimum antar-eksekusi untuk menghindari kehabisan modal saat terjadi penurunan tajam berkelanjutan.
- **Pemantauan Titik Impas (*Dynamic Breakeven Tracking*)**: Perhitungan otomatis harga rata-rata posisi berjalan.

---

# 🎯 Strategi Penjualan & Realisasi Laba (Sell & Profit Strategy)

Menentukan waktu likuidasi posisi untuk merealisasikan keuntungan modal (*capital gain*).

Parameter evaluasi keluar (*exit parameters*):
- **Target Margin Keuntungan (*Target Profit Margin %*)**: Persentase keuntungan relatif terhadap harga beli rata-rata (*average entry price*).
- **Target Harga Absolut (*Absolute Target Price*)**: Batas nominal harga likuidasi tertentu.
- **Kondisi Penjualan Bersyarat (*Dynamic Trailing Stop / Exit Indicator*)**.
- **Batas Kedaluwarsa Transaksi (*Maximum Holding Duration*)**.
- **Proporsi Likuidasi (*Partial / Full Position Exit*)**.

Alur eksekusi likuidasi laba:

```text
                  Target Margin Laba Ditetapkan: +2.0%
                                   │
                                   ▼
         Pemantauan Rerata Biaya Posisi vs Harga Pasar Berjalan
                                   │
                                   ▼
          Laba Berjalan (Unrealized P/L) >= Ambang Target (+2.0%)?
                                   │
                   ┌───────────────┴───────────────┐
                   ▼                               ▼
                YA (YES)                        TIDAK (NO)
                   │                               │
                   ▼                               ▼
         Kirim Instruksi SELL            Pertahankan Posisi (HOLDING)
         ke Lapisan Eksekusi
```

---

# 📤 Lapisan Eksekusi & Manajemen Pesanan (Execution & Order Layer)

Lapisan Eksekusi bertindak sebagai gerbang operasional yang mengonversi keputusan Mesin Strategi menjadi instruksi transaksi nyata pada bursa kripto.

Alur pengiriman dan pelacakan instruksi:

```text
                   Keputusan Mesin Strategi (BUY / SELL)
                                    │
                                    ▼
                     Manajer Pesanan (Order Manager)
                                    │
                                    ▼
                   Validasi Integritas & Limit Saldo
                                    │
                                    ▼
                      Konektor Bursa / Exchange API
                                    │
                                    ▼
                   Sinkronisasi Status Pesanan Terkini
```

## Siklus Hidup Pesanan (Order Lifecycle)

Manajer Pesanan mengelola status pesanan sejak pembuatan hingga pemenuhan:

```text
                   [PEMBUATAN PESANAN (CREATE)]
                                │
                                ▼
                    [TERTUNDA / PENDING]
                                │
          ┌─────────────────────┼─────────────────────┐
          ▼                     ▼                     ▼
     [TERPENUHI]          [DIBATALKAN]             [GAGAL]
      (FILLED)            (CANCELLED)             (FAILED)
```

---

# 🏦 Integrasi Bursa Kripto (Exchange Integration Layer)

Menangani komunikasi langsung dengan infrastruktur bursa penyedia likuiditas melalui REST API dan WebSocket:

Layanan pertukaran data bursa mencakup:
- Penerimaan umpan data harga dan buku pesanan (*Order Book Feeds*).
- Sinkronisasi informasi saldo akun dan aset secara berkala.
- Pembuatan dan pengiriman pesanan (*Order Placement*).
- Penarikan kembali / pembatalan pesanan aktif (*Order Cancellation*).
- Penerimaan laporan eksekusi seketika (*Execution / Fill Reports*).

---

# 💼 Mesin Portofolio (Portfolio Engine)

Mesin Portofolio memperbarui status kepemilikan aset dan saldo kas berdasarkan laporan eksekusi pesanan yang berhasil terpenuhi (*filled order reports*).

Alur sinkronisasi portofolio:

```text
                   Laporan Eksekusi Pesanan Diterima
                                 │
                                 ▼
                          Mesin Portofolio
                                 │
        ┌────────────────────────┼────────────────────────┐
        ▼                        ▼                        ▼
Pembaruan Saldo Kas     Pembaruan Kuantitas      Rekalkulasi Harga
   (Cash Balance)          Unit Aset              Rerata Akumulasi
        │                        │                        │
        └────────────────────────┼────────────────────────┘
                                 ▼
                     Kalkulasi Valuasi Pasar &
                   Laba/Rugi (Realized/Unrealized)
```

## Metrik Laba & Rugi (Profit & Loss / PnL)

Sistem membedakan dua jenis klasifikasi laba/rugi:
1. **Laba/Rugi Mengambang (*Unrealized P/L*)**:
   - Diperhitungkan secara *real-time* dari posisi yang masih aktif dipegang (*holding*), membandingkan harga pasar saat ini terhadap harga rata-rata akumulasi.
2. **Laba/Rugi Terealisasi (*Realized P/L*)**:
   - Keuntungan atau kerugian permanen yang telah dikunci setelah eksekusi likuidasi (*sell order fill*) selesai diproses.

---

# ⏳ Kondisi Penghentian & Penutupan Siklus Bot

Setiap bot memiliki aturan terminasi yang deterministik untuk memastikan disiplin eksekusi:

```text
                       KRITERIA TERMINASI OPERASIONAL
                                      │
         ┌───────────────┬────────────┴───┬───────────────┐
         ▼               ▼                ▼               ▼
Modal Teralokasi   Target Keuntungan  Batas Durasi   Intervensi Manual /
Habis Terpakai     Telah Terpenuhi    Operasional    Pemberhentian Darurat
(Capital Limit)    (Take Profit)      (Time Limit)   (Manual / Emergency Stop)
```

Alur pasca-terminasi bot:

```text
                  Instruksi Terminasi Terpicu
                               │
                               ▼
               Kompilasi Seluruh Data Transaksi
                               │
                               ▼
              Kalkulasi Performa & Hasil Akhir
                               │
                               ▼
               Generasi Laporan Ringkas (Summary)
                               │
                               ▼
             Arsip Permanen ke Riwayat Sistem (History)
```

## Ringkasan Metrik Bot (Bot Performance Summary)

Metrik yang dicatat saat bot menyelesaikan siklus operasionalnya:
- Frekuensi total transaksi akumulasi (*BUY Count*).
- Frekuensi total transaksi likuidasi (*SELL Count*).
- Total modal yang dimanfaatkan (*Capital Deployed*).
- Harga rata-rata pembelian (*Weighted Average Entry Price*).
- Harga rata-rata penjualan (*Average Exit Price*).
- Laba/rugi bersih terealisasi (*Net Realized P/L*).
- Durasi aktif bot (*Total Active Lifespan*).
- Kode status penyelesaian akhir (*Final Exit Code*).

---

# 💾 Lapisan Data & Skema Entitas (Data Layer)

Menangani persistensi informasi sistem secara terstruktur dan terindeks:

Entitas data utama:
- 👤 **Pengguna (*Users*)**: Profil, preferensi, dan konfigurasi kredensial bursa.
- 🤖 **Bot (*Bots*)**: Parameter konfigurasi, metadata, dan status mesin status berjalan.
- 📋 **Pesanan (*Orders*)**: Rincian pesanan limit/market, volume, harga, dan ID bursa.
- 💼 **Portofolio (*Portfolios*)**: Snapshot berkala saldo akun dan nilai kepemilikan aset.
- 📜 **Transaksi (*Transactions Ledger*)**: Catatan audit mutasi keuangan dan eksekusi perdagangan.
- 📊 **Snapshot Pasar (*Market Snapshots*)**: Arsip data harga historis untuk referensi analisis.
- 📝 **Log Audit (*Audit & System Logs*)**: Rekaman kejadian operasional bot dan galat sistem.

Relasi hierarkis entitas:

```text
PENGGUNA (USER)
 ├── PORTOFOLIO (PORTFOLIO)
 ├── INSTANS BOT (BOTS)
 │    ├── KONFIGURASI (CONFIG)
 │    ├── STATUS INTERNAL (STATE)
 │    ├── PESANAN TERKAIT (ORDERS)
 │    └── LOG OPERASIONAL (LOGS)
 ├── PESANAN GLOBAL (ORDERS)
 └── BUKU BESAR RIWAYAT (HISTORY)
```

---

# 🛡️ Lapisan Keamanan & Manajemen Risiko (Safety & Risk Layer)

Lapisan Keamanan berfungsi sebagai pengaman proaktif (*fail-safe system*) yang membatasi tindakan di luar toleransi risiko modal.

Komponen utama mitigasi risiko:
- **Pengendalian Modal (*Capital Exposure Controls*)**: Membatasi alokasi dana per bot dan mempertahankan batas cadangan kas minimum (*liquidity reserve*).
- **Pembatasan Kerugian (*Drawdown & Loss Mitigation*)**: Mekanisme pembatalan dan cut-off otomatis ketika pergerakan pasar melampaui toleransi risiko.
- **Validasi Integritas Instruksi (*Pre-Flight Order Validation*)**: Pencegahan pengiriman order duplikat (*idempotency keys*), verifikasi saldo, dan validasi kepatuhan aturan bursa (*lot step size, minimum notional*).
- **Prosedur Pemutus Arus Darurat (*Emergency Circuit Breaker*)**: Penghentian total instan saat terdeteksi anomali kritis.

Prosedur Eksekusi Pemutus Darurat (*Emergency Stop Protocol*):

```text
               KONDISI KRITIS TERDETEKSI / PERINTAH MANUAL
                                    │
                                    ▼
                       EMERGENCY STOP TERPICU
                                    │
       ┌────────────────────────────┼────────────────────────────┐
       ▼                            ▼                            ▼
Hentikan Semua Bot           Batalkan Seluruh             Simpan Snapshot
Aktif Seketika              Pesanan Terbuka di           Kondisi Terakhir
                                   Bursa
       │                            │                            │
       └────────────────────────────┼────────────────────────────┘
                                    ▼
                     Catat Log Forensik & Siarkan
                    Notifikasi Kritis ke Pengguna
```

---

# 🔔 Lapisan Notifikasi (Notification Layer)

Menyediakan mekanisme publikasi kejadian (*event publishing*) multi-kanal untuk memberikan visibilitas langsung kepada pengguna atas dinamika sistem:

Kategori kejadian (*System Events*):
- Inisiasi dan pengaktifan bot (*Bot Started*).
- Bot memasuki mode penantian sinyal (*Bot Waiting*).
- Eksekusi transaksi beli berhasil (*BUY Filled*).
- Eksekusi transaksi penjualan berhasil (*SELL Filled*).
- Target laba tercapai (*Profit Target Reached*).
- Penghentian bot normal (*Bot Completed / Stopped*).
- Kegagalan pengiriman atau eksekusi pesanan (*Order Rejection / API Error*).
- Aktivasi pemutus arus darurat (*Emergency Stop Activated*).

Saluran distribusi yang didukung:
- 🌐 Notifikasi internal aplikasi (*In-App Webhooks & Toast UI Alerts*).
- 📱 Integrasi bot Telegram (*Direct Telegram Messenger Alerts*).

---

# 🔄 Diagram Alur Sistem Terpadu (Complete Trading Flow)

Representasi lengkap dari inisiasi pengguna hingga audit transaksi:

```text
👤 PENGGUNA (USER)
   │
   ▼
🌐 ANTARMUKA WEB
   │
   ▼
🤖 FORMULASI BOT BARU
   │
   ▼
⚙️ KONFIGURASI PARAMETER
   │
   ▼
🔍 VALIDASI INTEGRITAS & RISIKO
   │
   ▼
▶️ PENGAKTIFAN BOT (START)
   │
   ▼
🔌 MOBEE API GATEWAY
   │
   ▼
⚙️ MESIN PERDAGANGAN
   │
   ▼
📥 AGREGATOR DATA PASAR
   │
   ▼
🧠 MESIN STRATEGI
   │
   ├──────────────────────────┬──────────────────────────┐
   ▼                          ▼                          ▼
AKUISISI (BUY)          MENUNGGU (WAIT)            LIKUIDASI (SELL)
   │                          │                          │
   │                          │                          │
   └──────────────────────────┼──────────────────────────┘
                              ▼
                   📤 LAPISAN EKSEKUSI
                              │
                              ▼
                     🏦 BURSA / EXCHANGE
                              │
                              ▼
                   📋 STATUS EKSEKUSI ORDER
                              │
                              ▼
                    💼 MESIN PORTOFOLIO
                              │
                              ▼
                    💾 BASIS DATA SISTEM
                              │
                              ▼
                    📋 RINGKASAN PERFORMA
                              │
                              ▼
                    📜 BUKU AUDIT RIWAYAT
```

---

# 🧩 Prinsip Arsitektur Perangkat Lunak (Architectural Principles)

Pengembangan sistem Mobee dipandu oleh prinsip-prinsip rekayasa perangkat lunak berikut:

### 1. Pemisahan Tanggung Jawab (*Separation of Concerns*)
Setiap modul dirancang untuk menjalankan fungsi tunggal yang independen, meminimalkan keterikatan (*loose coupling*), dan memaksimalkan keutuhan fungsi (*high cohesion*):
- Antarmuka Web bertanggung jawab atas visualisasi data dan input pengguna.
- Asisten AI bertanggung jawab atas interpretasi konteks dan analisis kualitatif.
- API Gateway memediasi kontrak komunikasi terstandardisasi.
- Mesin Perdagangan mengorkestrasi evaluasi logika dan pemrosesan transaksi.
- Lapisan Data bertanggung jawab atas persistensi dan integritas catatan transaksi.

### 2. Dekopling Strategi dan Eksekusi (*Decoupled Strategy & Execution*)
Logika analitik kuantitatif pada Mesin Strategi sepenuhnya terpisah dari mekanisme pengiriman pesanan pada Lapisan Eksekusi. Strategi baru dapat diintegrasikan tanpa memerlukan modifikasi pada lapisan integrasi bursa.

### 3. Ekstensibilitas Strategi yang Modular (*Modular Strategy Extensibility*)
Varian strategi (seperti *dollar-cost averaging*, akumulasi berbasis penurunan dinamis/nyelam, atau *grid trading*) diimplementasikan sebagai profil konfigurasi di dalam Mesin Strategi, bukan dengan menduplikasi mesin perdagangan baru.

### 4. Parameterisasi Terstandarisasi (*Standardized Parameterization*)
Semua ambang batas (seperti target keuntungan, batas toleransi risiko, rasio alokasi, dan interval waktu) dikelola sebagai parameter terkonfigurasi (*configuration as data*), memungkinkan penyesuaian dinamis tanpa rekayasa ulang basis kode.

### 5. AI Terarah dengan Otorisasi Manusia (*Human-in-the-Loop AI Assistance*)
Kecerdasan buatan dimanfaatkan sebagai akselerator analisis data dan penyusun proposal tindakan yang terikat pada batasan sistem. Tidak ada transaksi finansial yang dapat dijalankan tanpa otorisasi terverifikasi dari pengguna.

---

# 🗺️ Cetak Biru Arsitektur Terpadu (Unified System Blueprint)

```text
                                  👤 PENGGUNA (USER)
                                           │
                        ┌──────────────────┴──────────────────┐
                        │                                     │
                        ▼                                     ▼
               🌐 ANTARMUKA WEB                        🤖 ASISTEN AI
                        │                                     │
                        │                                     ▼
                        │                            🧠 MESIN KONTEKS AI
                        │                                     │
                        └──────────────────┬──────────────────┘
                                           ▼
                                🔌 MOBEE API GATEWAY
                                           │
                                           ▼
                             ⚙️ MESIN PERDAGANGAN UTAMA
                                           │
               ┌───────────────────────────┼───────────────────────────┐
               │                           │                           │
               ▼                           ▼                           ▼
       📥 DATA PASAR               🤖 MESIN BOT                🧩 MESIN STRATEGI
                                                                       │
                                                       ┌───────────────┼───────────────┐
                                                       ▼               ▼               ▼
                                                   AKUISISI        MENUNGGU        LIKUIDASI
                                                    (BUY)           (WAIT)          (SELL)
                                                                       │
                                                                       ▼
                                                             📤 LAPISAN EKSEKUSI
                                                                       │
                                                                       ▼
                                                             🏦 BURSA / EXCHANGE
                                                                       │
                                                                       ▼
                                                             💼 MESIN PORTOFOLIO
                                                                       │
                                                                       ▼
                                                                💾 BASIS DATA
                                                                       │
                                     ┌─────────────────────────────────┼─────────────────────────────────┐
                                     ▼                                 ▼                                 ▼
                             BUKU BESAR RIWAYAT                 LOG OPERASIONAL                  LAPORAN RINGKAS
                                 (HISTORY)                           (LOGS)                         (SUMMARY)

       🛡️ LAPISAN KEAMANAN & RISIKO ──────────────────────────────────────────────────────────► SELURUH LAPISAN SISTEM
       🔔 LAPISAN NOTIFIKASI ◄───────────────────────────────────────────────────────────────── DISTRIBUSI KEJADIAN
```

---

# 🚧 Status Proyek Saat Ini (Project Status)

Mobee saat ini berada dalam fase **Perancangan Arsitektur & Spesifikasi Konsep (*System Concept & Architecture Design Phase*)**.

Fungsi utama dokumen ini:
- Sebagai acuan arsitektur dasar bagi tim pengembang.
- Menetapkan batasan fungsional dan model interaksi antar-modul.
- Menyediakan panduan spesifikasi sebelum fase implementasi kode program dimulai.

---

# 🛣️ Peta Jalan Pengembangan (Development Roadmap)

Tahapan implementasi sistem dirancang secara berjenjang:

```text
1. Fondasi Model Data & Skema Persistensi (Core Data Models)
       ↓
2. Spesifikasi Antarmuka API Gateway & Kontrak Layanan (Mobee API)
       ↓
3. Konektor Data Pasar & Umpan Real-time (Market Data Aggregator)
       ↓
4. Kerangka Kerja Mesin Perdagangan (Core Trading Engine Architecture)
       ↓
5. Implementasi Mesin Strategi & Aturan Keputusan (Strategy Engine)
       ↓
6. Lapisan Eksekusi, Konektor Bursa, & Manajemen Pesanan (Order Management)
       ↓
7. Mesin Portofolio & Pelacakan Nilai Laba-Rugi (Portfolio Engine)
       ↓
8. Orkestrasi Siklus Hidup Instans Bot (Bot Lifecycle Management)
       ↓
9. Pengembangan Dasbor Antarmuka Pengguna (Web Interface Dashboard)
       ↓
10. Integrasi Saluran Notifikasi Multi-Kanal (Notification Layer)
       ↓
11. Integrasi Mesin Konteks & Asisten Kecerdasan Buatan (AI Assistant)
```

---

# 📚 Struktur Dokumentasi Teknis (Documentation Structure)

Untuk menjaga modularitas informasi, dokumentasi detail teknis selanjutnya akan dipartisi ke dalam repositori spesifik:

```text
docs/
│
├── architecture.md       # Cetak biru arsitektur menyeluruh
├── trading-engine.md     # Spesifikasi teknis mesin perdagangan
├── strategy-engine.md    # Formulasi logika strategi dan sinyal
├── bot-lifecycle.md      # Model mesin status berhingga instans bot
├── execution.md          # Protokol eksekusi dan integrasi bursa
├── portfolio.md          # Metrik valuasi portofolio dan rekonsiliasi PnL
├── api-spec.md           # Spesifikasi kontrak OpenAPI / REST / WebSocket
├── ai-assistant.md       # Arsitektur mesin konteks dan integrasi model AI
└── safety-risk.md        # Parameter kontrol risiko dan protokol pemutus darurat
```

---

# 🐝 Mobee

**Mobee** dibangun sebagai platform terintegrasi yang menyatukan **otomasi perdagangan (*automation*), analitik data pasar (*market telemetry*), tata kelola modal (*portfolio management*), dan asisten cerdas (*AI assistance*)** ke dalam arsitektur yang tangguh, modular, dan dapat diperluas (*scalable*).

> **Mobee is not just a trading bot. It is a comprehensive platform engineered around an automated trading engine.**
