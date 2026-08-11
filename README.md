# 🌊 Analisis Ketahanan Pangan Kepulauan Maluku (GEMASTIK XIX / 2026)

**Judul Penelitian:** *Pemetaan Ketahanan Pangan Kepulauan Maluku Berbasis Integrasi Analisis Spasial, Regresi Lokal, NLP Wacana Digital, dan Machine Learning Forecasting*

**Unit Analisis:** 21 Kabupaten/Kota di Provinsi Maluku dan Maluku Utara  
**Rentang Waktu:** 2020–2025 (historis) · 2026–2028 (proyeksi)  
**Variabel Utama:** PoU — Prevalence of Undernourishment (Prevalensi Ketidakcukupan Konsumsi Pangan, %)

---

## 🗺️ Peta Navigasi Analisis

```
RESULT GEMASTIK 26 NEW/
├── 01_autokorelasi_spasial/     → LISA, Getis-Ord Gi*, Moran Global & Bivariat
├── 02_clustering_spasial/       → K-Means, K-Medoids, Ward, SKATER, SC-Hierarchical (4 Tipologi)
├── 03_regresi_spasial_lokal/    → OLS, SAR, SEM, GWR Parsimoni, GTWR-12b Panel 2020–2025
├── 04_nlp_analisis_teks/        → IndoBERT Sentimen + BERTopic (88 topik, 2.266 dokumen)
└── 05_forecasting/              → XGBoost multi-output per kabupaten (2026–2028)
```

---

## 📊 Ringkasan Temuan Utama per Metode

### Metode 01 — Autokorelasi Spasial (LISA & Getis-Ord Gi*)

| Indikator | Nilai |
|-----------|-------|
| Moran's I global (rata-rata 2020–2025) | 0.03–0.29 (tidak signifikan mayoritas) |
| Tahun dengan Moran tertinggi (k=4 tetangga) | 2025 (*I* = **0.283**, p = 0.017) |
| Wilayah Hotspot LISA persisten | **Kepulauan Sula** (2×) & **Pulau Taliabu** (2×) — High-High |
| Wilayah Coldspot LISA paling stabil | **Halmahera Selatan** (6×) & **Halmahera Tengah** (4×) — Low-Low |

**Fakta kunci:** Pola spasial PoU baru muncul signifikan di tahun 2025, menunjukkan klasterisasi kerawanan pangan **semakin menguat** setelah 2023. Kepulauan Sula dan Pulau Taliabu konsisten menjadi zona ketidakcukupan tinggi yang saling menguatkan antar-tetangga.

---

### Metode 02 — Clustering & Tipologi Spasial

**4 Tipologi Ketahanan Pangan Kepulauan Maluku:**

| Cluster | Tipologi | n | PoU Rata-rata | Ciri Utama |
|---------|----------|---|---------------|------------|
| **C0** | Rawan Konsumsi Tanpa Kemiskinan Ekstrem | 6 | **33.7%** | IKM rendah, kemiskinan nominal kecil → masalah distribusi |
| **C1** | Kepulauan Miskin Terisolasi | 11 | 32.2% | Kemiskinan 19.9%, isolasi ganda |
| **C2** | Sentra Urban Terhubung | 2 | **21.6%** | IKM tinggi (0.66), IPM 82.0 |
| **C3** | Enklave Ekonomi Terisolasi | 2 | 26.0% | PDRB 3.3× rata-rata (tambang), tapi PoU tinggi & air minum terendah |

**Fakta kunci:** Produksi pangan (perikanan, padi) *tidak berkorelasi* dengan PoU (r = +0.14). Yang menentukan adalah **isolasi maritim** (r = +0.60) dan **beban pengeluaran pangan** (r = +0.70).

---

### Metode 03 — Regresi Spasial Lokal (GWR & GTWR)

**Model terpilih:** GTWR-12b (10 fitur, panel 2020–2025, N=126)
- AICc = 726.43 | RMSE = 3.41 | R² = 0.719

**Tiga faktor paling dominan secara lokal (2025):**

| Fitur | Pengaruh Global (SEM) | Arah | Interpretasi |
|-------|-----------------------|------|--------------|
| PDRB per Kapita (X8) | −3.481 (SEM) | Negatif | PDRB tinggi → PoU turun di semua wilayah |
| Kunjungan Kapal (X13b) | −3.481 (SEM, p=0.001) | Negatif | Konektivitas maritim mengurangi PoU |
| Visibilitas Wacana (X27) | Bervariasi lokal (GTWR) | Negatif–positif | Krisis senyap di wilayah terpencil |

**Tren temporal GTWR (koefisien 3 fitur utama, 2020→2025):**
- PDRB: −4.18 → −2.22 *(dampak meleeh)*
- Kunjungan Kapal: −3.97 → −1.06
- Wacana Digital: −2.49 → **+0.02** *(efek hampir netral di 2025)*

---

### Metode 04 — NLP Wacana Digital (IndoBERT + BERTopic)

| Parameter | Nilai |
|-----------|-------|
| Dokumen final | 2.266 (dari 2.810 mentah) |
| Distribusi Sentimen | 44.5% Neutral · **37.4% Negatif** · 18.1% Positif |
| Model F1 Macro (test set) | **0.9257** |
| Jumlah Topik BERTopic | **88 topik** (coherence 0.668, diversitas 0.915) |

**Topik prioritas kebijakan tertinggi:**
1. 🔴 *Kenaikan Harga Beras dan Pangan* — skor 0.7235, 81 dok, 55.6% negatif
2. 🔴 *Diskusi Program Makan Bergizi Gratis (MBG)* — skor 0.6176, 46 dok
3. 🟥 *Kelaparan dan Kehidupan Masyarakat Adat* — skor 0.5997, **86.5% negatif**

**Dikotomi platform:**
- **YouTube** (520 dok): paling kritis — 63.1% negatif, wadah luapan emosi warga
- **TikTok** (1.068 dok): paling besar — 42.7% negatif, arena viral ibu & remaja pasar
- **Twitter/X** (678 dok): paling institusional — hanya 9.4% negatif, dominan berita resmi

---

### Metode 05 — Forecasting (XGBoost)

**Model terpilih:** XGBoost multi-output (RMSE ≈ 2.4, MAE ≈ 1.8)

**Proyeksi PoU 2025 → 2028 (21 kab/kota):**

| Arah | Jumlah Wilayah | Penurunan Terbaik |
|------|---------------|-------------------|
| Turun (membaik) | 13 | Kota Tidore Kepulauan: −2.72 pp |
| Naik (memburuk) | 7 | **Maluku Tengah: +2.77 pp** |
| Stabil | 1 | Kota Ambon: −0.11 pp |

---

## 🔴 5 Kabupaten/Kota Kunci: Sintesis Lintas Metode

Berikut 5 wilayah yang paling penting secara analitis — masing-masing dikonfirmasi oleh **≥ 3 metode berbeda**.

---

### 1️⃣ Kepulauan Sula — *Episentrum Krisis Konsisten*

| Metode | Temuan |
|--------|--------|
| **01 LISA** | High-High Hotspot (2× persisten, 2024–2025) — zona PoU tinggi bercluster |
| **02 Clustering** | Kluster C0 "Rawan Konsumsi Tanpa Kemiskinan Ekstrem" — paradoks distribusi |
| **03 GTWR** | X13c (Jarak Pelabuhan) = **+2.75** — isolasi fisik terbukti paling berat |
| **04 NLP** | 78 dok, **53.85% negatif** (#5 tertinggi), X27=1 → *krisis senyap* |
| **05 Forecasting** | PoU 2025: **37.36%** (#1) → 2028: **35.52%** (#1) — **tidak beranjak dari peringkat terburuk** |

**Korelasi:** Satu-satunya wilayah yang #1 terburuk *dan* tetap #1 pada 2028. PDRB rendah + terisolasi maritim + wacana digital minim = triple burden.

---

### 2️⃣ Halmahera Tengah — *Paradoks Enklave Industri*

| Metode | Temuan |
|--------|--------|
| **01 LISA** | Low-Low Coldspot (4× persisten) — PoU terendah di kawasan tetangga |
| **02 Clustering** | Kluster C3 "Enklave Ekonomi" — PDRB 91.5 jt (tertinggi), PoU tetap 26% |
| **03 GTWR** | X8 (PDRB) = −2.12 — dampak PDRB melindungi tapi tidak sempurna |
| **04 NLP** | X27=0 — **nol wacana digital** di media sosial; tertutup total dari aspirasi publik |
| **05 Forecasting** | PoU 2025: **19.03%** (#21, terendah) → 2028: **21.32%** — naik walau tetap terendah |

**Korelasi:** PDRB tinggi dari smelter nikel *tidak otomatis* menjamin ketahanan pangan masyarakat lokal. Air minum terendah + PoU merayap naik = risiko tersembunyi enklave industri.

---

### 3️⃣ Pulau Taliabu — *Krisis Senyap Super*

| Metode | Temuan |
|--------|--------|
| **01 LISA** | High-High Hotspot (2× persisten) — klaster PoU tinggi bersama Kep. Sula |
| **02 Clustering** | Kluster C0 "Rawan Konsumsi" — produksi lokal tinggi tapi pangan tak tersalur |
| **03 GTWR** | X13c = **+3.045** (tertinggi se-Maluku) — jarak pelabuhan paling mematikan |
| **04 NLP** | 20 dok, **5.00% negatif** (terendah!) vs PoU 34.19% → *Krisis Senyap Super* |
| **05 Forecasting** | PoU 2025: 34.19% → 2028: 33.44% — penurunan lambat (hanya −0.75 pp) |

**Korelasi:** Ketidakhadiran suara digital warga Taliabu di media sosial (hanya 20 dokumen dari 2.266) bukan tanda kondisi baik — melainkan sinyal *keterisolasian aspirasi* yang paling ekstrem di Maluku Utara.

---

### 4️⃣ Seram Bagian Barat — *Paradoks Produksi*

| Metode | Temuan |
|--------|--------|
| **01 LISA** | Tidak Signifikan — tidak terdeteksi sebagai hotspot LISA |
| **02 Clustering** | Kluster C0/C1 — kapasitas produksi pangan tertinggi ketiga (0.742), PoU 32.94% |
| **03 GTWR** | X13b = −2.55 (kunjungan kapal rendah), X1 = **+1.64** (anomali: produksi ↑ PoU ↑) |
| **04 NLP** | 56 dok, **58.93% negatif** (#2 tertinggi) — warga mengeluhkan distribusi hasil laut |
| **05 Forecasting** | PoU 2025: 32.94% → 2028: **33.54%** — *satu-satunya wilayah Maluku yang memburuk selain Maluku Utara* |

**Korelasi:** Produksi perikanan besar tetapi infrastruktur distribusi (kapal, pelabuhan) tidak mampu membawa hasil ke masyarakat. Dikonfirmasi oleh 3 metode berbeda sebagai "paradoks produksi-distribusi."

---

### 5️⃣ Maluku Tengah — *Bom Waktu Tersembunyi*

| Metode | Temuan |
|--------|--------|
| **01 LISA** | Tidak Signifikan — terlindungi oleh kepadatan tetangga |
| **02 Clustering** | Kluster C1 "Kepulauan Miskin Terisolasi" — kapasitas produksi tertinggi (1.00, dinormalisasi) |
| **03 GTWR** | X13b = −1.48, X27 = −0.44 — konektivitas kapal & wacana paling lemah di Provinsi Maluku |
| **04 NLP** | X27 (volume wacana) rendah → *kandidat krisis senyap* |
| **05 Forecasting** | PoU 2025: 30.26% (peringkat 14) → 2028: **33.03%** (peringkat 7) — **lompatan peringkat terburuk (+7 posisi)** |

**Korelasi:** Wilayah yang "tidak terlihat berbahaya" hari ini tetapi model XGBoost memproyeksikan lonjakan PoU terbesar di antara seluruh 21 wilayah. Sinyal peringatan dini yang kuat dari 3 metode berbeda.

---

## 📌 Matriks Sintesis Lintas Metode

| Kab/Kota | LISA | Cluster | GTWR | NLP | Forecast | Skor Konfirmasi |
|----------|------|---------|------|-----|----------|-----------------|
| **Kepulauan Sula** | HH-Hotspot | C0 Paradoks | Isolasi berat | Sentimen negatif | #1 & tetap #1 | ⭐⭐⭐⭐⭐ 5/5 |
| **Pulau Taliabu** | HH-Hotspot | C0 Paradoks | Jarak terjauh | Krisis senyap super | Lambat membaik | ⭐⭐⭐⭐⭐ 5/5 |
| **Seram Bagian Barat** | – | C0 Produksi | Paradoks X1 | #2 sentimen negatif | Memburuk | ⭐⭐⭐⭐ 4/5 |
| **Maluku Tengah** | – | C1 Terisolasi | Konektivitas lemah | Wacana rendah | Lompat 7 peringkat ↑ | ⭐⭐⭐⭐ 4/5 |
| **Halmahera Tengah** | LL-Coldspot | C3 Enklave | PDRB melindungi | Nol wacana | Naik pelan-pelan | ⭐⭐⭐⭐ 4/5 |

---

## 🎯 Tiga Narasi Kebijakan Utama

### 🚨 Narasi 1 — "Pulau Terlupakan" (Kepulauan Sula + Pulau Taliabu)
Dua wilayah ini berbagi satu karakteristik: **ketidakhadiran ganda** — tidak ada infrastruktur logistik memadai *dan* tidak ada suara digital yang mewakili mereka di media sosial. Kerawanan pangan mereka tidak terlihat secara politik, sehingga intervensi kebijakan cenderung melewatkan mereka.

> **Rekomendasi:** Penambahan trayek kapal perintis reguler + program literasi digital untuk memperkuat aspirasi publik yang terdengar.

### ⚠️ Narasi 2 — "Paradoks Produksi" (Seram Bagian Barat)
Wilayah dengan kapasitas produksi pangan tertinggi ketiga justru memiliki PoU tinggi *dan* diprediksi memburuk. Masalah bukan di hulu (produksi), melainkan di hilir (distribusi).

> **Rekomendasi:** Pembangunan cold chain dan jaringan koperasi hasil laut yang menyasar akses internal, bukan hanya ekspor regional.

### 💣 Narasi 3 — "Bom Waktu" (Maluku Tengah)
Wilayah yang saat ini tidak mencolok tetapi diprediksi melonjak 7 peringkat menjadi salah satu wilayah terburuk pada 2028. Sinyal awal dari NLP (wacana rendah) dan GTWR (konektivitas kapal menurun) konsisten dengan proyeksi XGBoost.

> **Rekomendasi:** Prioritaskan Maluku Tengah dalam rencana penganggaran jangka menengah (RPJMD 2025–2030), fokus pada konektivitas maritim dan pemantauan dini PoU.

---

## 📁 Direktori Output Lengkap

| Folder | Isi | File Kunci |
|--------|-----|------------|
| `01_autokorelasi_spasial/` | 11 tabel CSV + 10+ peta | `persistensi_lisa.csv`, `getis_ord_panel_2020_2025.csv` |
| `02_clustering_spasial/` | Notebook + 3 peta PNG + PDF | `Ringkasan_Clustering_Tipologi_Maluku.md` |
| `03_regresi_spasial_lokal/` | 25 tabel CSV + 50+ gambar | `gtwr_final_12b_koefisien_lokal_kabkota_tahun.csv` |
| `04_nlp_analisis_teks/` | 18 CSV + 36 PNG/HTML + 90 RAG | `ringkasan_analisis_teks_nlp.md`, `35_dashboard_eksekutif.png` |
| `05_forecasting/` | 8 tabel CSV + 10+ gambar | `forecast_pou_2026_2028.csv`, `51_tren_pou_5kabkota_unik_2020_2028.png` |

---

## 👥 Tim & Konteks

**Kompetisi:** GEMASTIK XIX 2026 — Kategori Komputasi Data dan Analitika  
**Institusi:** Universitas Negeri Surabaya (UNESA)  
**Dataset:** BPS Provinsi Maluku & Maluku Utara, 2020–2025  
**Pipeline analitik:** Python (GeoPandas · PySAL · scikit-learn · XGBoost · Transformers · BERTopic)

---
*README ini dihasilkan dari sintesis otomatis 5 metode analisis dengan total ≈ 140 file output. Semua angka bersumber dari data CSV hasil running notebook yang terverifikasi.*
