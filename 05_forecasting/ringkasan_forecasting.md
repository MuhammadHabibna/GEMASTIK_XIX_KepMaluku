# Dokumen Referensi: Forecasting PoU 2026-2028 Kepulauan Maluku (Model XGBoost)

## Ringkasan Proyek
Proyek GEMASTIK XIX/2026 (Divisi Data Mining) meramalkan Prevalensi Ketidakcukupan Konsumsi Pangan (PoU) 2026-2028 untuk 21 kabupaten/kota di Provinsi Maluku dan Maluku Utara berbasis data 2020-2025, dilengkapi analisis Paradoks Produksi dan Underdog Ketahanan Pangan.

## Metodologi Forecasting
- Model panel (menggabungkan seluruh 21 kabupaten/kota x tahun menjadi satu dataset 126 baris) dipilih dibanding model univariat per kabupaten/kota, karena tiap unit hanya memiliki 6 observasi tahunan -- terlalu sedikit untuk deret waktu klasik. Fitur: lag PoU, target encoding karakteristik dasar kabupaten/kota (kab_hist_mean), Indeks Kapasitas Pangan, kemiskinan, PDRB, kepadatan, IPM, pangsa pengeluaran pangan, dan tahun.
- Dua model dibandingkan lewat backtesting walk-forward (latih 2021-2023, uji rekursif 2024-2025): ElasticNet dan XGBoost. Model terpilih: XGBoost (Panel, Ditala) (MAE backtesting=2.245).
- Kedalaman pohon XGBoost sengaja dibatasi minimal 2 (bukan 1) pada grid search, karena percobaan awal menunjukkan pohon terlalu dangkal dengan one-hot encoding kabupaten/kota menghasilkan forecast yang identik/flat antar banyak unit -- meski MAE backtesting sedikit lebih rendah, hasil tersebut kurang informatif. Target encoding (kab_hist_mean) menggantikan one-hot encoding untuk memampatkan informasi identitas kabupaten/kota menjadi satu fitur kontinu.
- Fitur eksogen untuk 2026-2028 (kemiskinan, PDRB, kepadatan, kapasitas produksi, IPM, pangsa pengeluaran pangan) ditahan konstan pada nilai 2025 (asumsi ceteris paribus) karena nilai masa depan belum tersedia.

## Insight Model (Feature Importance)
- Karakteristik Dasar Kabupaten/Kota (rata-rata historis): 43.2 persen kontribusi terhadap model.
- PoU Tahun Sebelumnya: 18.6 persen kontribusi terhadap model.
- Pangsa Pengeluaran Pangan: 11.7 persen kontribusi terhadap model.
- PDRB per Kapita: 7.3 persen kontribusi terhadap model.
- Persentase Penduduk Miskin: 4.7 persen kontribusi terhadap model.
- Indeks Pembangunan Manusia: 4.0 persen kontribusi terhadap model.
- Kepadatan Penduduk: 3.6 persen kontribusi terhadap model.
- Tahun (tren): 3.6 persen kontribusi terhadap model.
- Indeks Kapasitas Pangan (Produksi Perikanan): 3.5 persen kontribusi terhadap model.

## Definisi Paradoks dan Underdog
- PoU diregresikan pooled terhadap Indeks Kapasitas Pangan Lokal (126 observasi). Residual rata-rata per kabupaten/kota (2020-2025): residual positif besar = Paradoks (kapasitas tinggi, PoU tetap tinggi); residual negatif besar = Underdog (kapasitas rendah, PoU berhasil ditekan).

## Temuan Kunci
- Rata-rata PoU: 30.44 persen (2025) menjadi 30.30 persen (2028, forecast).
- Faktor paling dominan: Karakteristik Dasar Kabupaten/Kota (rata-rata historis) (43.2 persen).
- Proyeksi perbaikan terbesar: Kota Tidore Kepulauan (-2.72 poin persen).
- Proyeksi pemburukan terbesar: Maluku Tengah (+2.77 poin persen).
- Paradoks Produksi #1: Kepulauan Sula (residual +8.75).
- Underdog Ketahanan Pangan #1: Kota Ternate (residual -11.69).
- Top-1 PoU tertinggi 2025: Kepulauan Sula (37.36 persen).
- Bottom-1 PoU terendah 2025: Halmahera Tengah (19.03 persen).

## Keterbatasan
- Forecast berbasis 6 titik data historis per unit; sebaiknya diperbarui tiap kali data tahun baru tersedia, tidak dipakai untuk horizon lebih dari 3 tahun tanpa evaluasi ulang.
- Fitur eksogen 2026-2028 memakai asumsi ceteris paribus (nilai 2025 ditahan konstan); forecast akan lebih akurat jika data eksogen tahun berjalan tersedia dan dimasukkan ulang.
- Analisis Paradoks/Underdog memakai satu proksi kapasitas (produksi perikanan); R-squared regresi pooled rendah sehingga interpretasi kausal harus tetap berhati-hati.

## Daftar Berkas Tabel dan Gambar (Available Files)

Seluruh berkas output eksperimen forecasting tersimpan dalam folder `tables/` dan `figures/`:

### 📊 Berkas Tabel (`tables/`) — 8 Berkas CSV
1. `forecast_pou_2026_2028.csv` — **Hasil proyeksi utama nilai PoU (%)** 21 kabupaten/kota Kepulauan Maluku untuk tahun 2026, 2027, dan 2028.
2. `dataset_bersih_forecasting.csv` — Dataset panel 21 kab/kota × 6 tahun (2020–2025) setelah pembersihan & feature engineering siap latih.
3. `feature_importance.csv` — Bobot kontribusi kontributif indikator terhadap prediksi XGBoost (Karakteristik dasar 43.2%, Lag PoU 18.6%, Pangsa Pangan 11.7%, PDRB 7.3%, dll).
4. `paradoks_underdog_ranking.csv` — Hasil pemetaan dan ranking 21 kabupaten/kota dalam kategori **Paradoks Produksi** vs **Underdog Ketahanan Pangan**.
5. `perbandingan_model.csv` — Hasil evaluasi MAE backtesting walk-forward (2024–2025) perbandingan model XGBoost Panel vs ElasticNet.
6. `perbandingan_peringkat_2025_2028.csv` — Tabel matriks pergeseran posisi ranking kerawanan pangan kabupaten/kota dari tahun 2025 ke proyeksi 2028.
7. `perubahan_2025_2028.csv` — Perhitungan delta perbaikan/pemburukan poin persentase PoU (2025 vs 2028) per kabupaten/kota.
8. `statistik_pertumbuhan_volatilitas.csv` — Metrik deskriptif rata-rata, CAGR, standar deviasi, dan volatilitas PoU historis.

---

### 🖼️ Berkas Visualisasi (`figures/`) — 12 Berkas PNG
1. `01_historis_semua_kabkota.png` — Line chart tren pergerakan PoU historis 21 kabupaten/kota (2020–2025).
2. `03_perbandingan_model.png` — Bar chart evaluasi performa MAE backtesting model XGBoost vs ElasticNet.
3. `04_feature_importance.png` — Horizontal bar chart persentase kontribusi kepentingan fitur model XGBoost.
4. `05_forecast_small_multiples.png` — Grid panel 21 sub-chart tren PoU historis dan proyeksi 2026–2028 per kabupaten/kota.
5. `06_scatter_paradoks_underdog.png` — Quadrant scatterplot residual PoU vs Indeks Kapasitas Pangan (identifikasi Paradoks vs Underdog).
6. `07_visualisasi_utama_4unit.png` — Chart visualisasi perbandingan mendalam pada 4 kabupaten/kota sampel utama.
7. `08_perubahan_2025_2028.png` — Bar chart proyeksi delta perubahan persentase PoU (2025 vs 2028).
8. `09_forecast_provinsi.png` — Line plot proyeksi agregat tren PoU Provinsi Maluku vs Maluku Utara hingga 2028.
9. `10_slope_chart_peringkat.png` — Slope chart pergeseran peringkat kerawanan pangan dari 2025 ke 2028.
10. `11_peta_forecast_2028.png` — Peta tematik choropleth proyeksi tingkat kerawanan pangan (PoU %) tahun 2028.
11. `51_tren_pou_5kabkota_unik_2020_2028.png` — **[BARU]** Multi-line chart tren PoU 2020–2028 untuk 5 kabupaten/kota kunci (historis + prediksi dengan CI band & zona prediksi).
12. `52_panel_5kabkota_individual_2020_2028.png` — **[BARU]** Panel individual 5 sub-chart per kabupaten/kota kunci dengan nilai PoU per tahun dan delta 2020–2028.

---

## Analisis Mendalam: 5 Kabupaten/Kota Kunci (2020–2028)

Berdasarkan sintesis korelasi lintas metode (Autokorelasi Spasial LISA, Clustering Tipologi K-Means, Regresi GTWR, NLP Krisis Senyap, dan Forecasting XGBoost), dipilih **5 kabupaten/kota kunci** yang paling konsisten dan kaya akan wawasan kebijakan. Data historis mencakup 2020–2025; proyeksi 2026–2028 dihasilkan oleh model XGBoost Panel (MAE backtesting = 2.245).

---

### ① Kepulauan Sula — *"Terburuk Konsisten: Hotspot Spasial & Krisis Senyap"*

| Tahun | 2020 | 2021 | 2022 | 2023 | 2024 | 2025 | 2026* | 2027* | 2028* |
|-------|------|------|------|------|------|------|-------|-------|-------|
| **PoU (%)** | 43.47 | 42.17 | 42.37 | 44.37 | 35.11 | 37.36 | 34.58 | 35.31 | 35.52 |
| *95% CI* | — | — | — | — | — | — | [28.56, 40.61] | [26.79, 43.84] | [25.08, 45.96] |

**Peran & Karakteristik:**
- Meraih **Peringkat #1 PoU tertinggi** pada 2025 (37.36%) dan diperkirakan **tetap memegang Peringkat #1 pada 2028** (35.52%), tanpa ada koreksi struktural signifikan.
- Penurunan tipis pada 2026 (34.58%) diikuti kecenderungan *rebound* naik hingga 2028.

**Korelasi Lintas Metode:**
- **Metode 01 (Autokorelasi Spasial):** Terverifikasi sebagai *High-High Hotspot* pada peta LISA/Getis-Ord Gi* (2 dari 6 tahun observasi), mengindikasikan klaster perkuatan kerawanan pangan bersama wilayah sekitarnya.
- **Metode 02 (Clustering Tipologi):** Masuk ke **Klaster C1 (Miskin Terisolasi)** dengan karakteristik kemiskinan tinggi dan IPM rendah.
- **Metode 03 (GTWR):** Koefisien lokal PDRB = −3.10 dan **Jarak ke Pelabuhan Induk = +2.75** (tertinggi di Maluku Utara), mengonfirmasi isolasi maritim fisik sebagai faktor determinan utama.
- **Metode 04 (NLP Teks):** Teridentifikasi sebagai wilayah **Krisis Senyap (Track B)** — kerawanan pangan tinggi tetapi minim wacana/liputan media digital.

---

### ② Halmahera Tengah — *"Terbaik: Coldspot Spasial Persisten"*

| Tahun | 2020 | 2021 | 2022 | 2023 | 2024 | 2025 | 2026* | 2027* | 2028* |
|-------|------|------|------|------|------|------|-------|-------|-------|
| **PoU (%)** | 31.64 | 27.03 | 25.51 | 15.30 | 19.43 | 19.03 | 21.96 | 21.48 | 21.32 |
| *95% CI* | — | — | — | — | — | — | [15.94, 27.99] | [12.96, 30.01] | [10.88, 31.76] |

**Peran & Karakteristik:**
- Memiliki **PoU terendah se-Kepulauan Maluku pada 2025 (19.03%)** dan diproyeksikan konsisten mempertahankan posisi terbaik (Peringkat #21) hingga 2028 (21.32%).
- Penurunan historis sangat signifikan dari 31.64% (2020) hingga titik terendah 15.30% (2023).

**Korelasi Lintas Metode:**
- **Metode 01 (Autokorelasi Spasial):** Teridentifikasi sebagai *Low-Low Coldspot* paling persisten kedua (muncul 4 dari 6 tahun di LISA/Getis-Ord Gi*), membuktikan adanya efek *spillover* positif ketahanan pangan di wilayah utara.
- **Metode 02 (Clustering Tipologi):** Tergolong dalam **Klaster C2 (Sentra Urban/Industri)** dengan pertumbuhan ekonomi dan IPM yang lebih baik.
- **Metode 03 (GTWR):** Koefisien lokal PDRB (−2.71) dan Jarak Pelabuhan (+2.18) berada pada kisaran moderat.

---

### ③ Pulau Taliabu — *"Paradoks Produksi Kasus Khusus"*

| Tahun | 2020 | 2021 | 2022 | 2023 | 2024 | 2025 | 2026* | 2027* | 2028* |
|-------|------|------|------|------|------|------|-------|-------|-------|
| **PoU (%)** | 44.39 | 49.84 | 38.62 | 39.21 | 32.64 | 34.19 | 34.07 | 34.01 | 33.44 |
| *95% CI* | — | — | — | — | — | — | [28.04, 40.10] | [25.49, 42.53] | [23.00, 43.88] |

**Peran & Karakteristik:**
- Disebutkan secara eksplisit dalam dokumen narasi forecasting sebagai kasus utama **Paradoks Produksi**: wilayah dengan potensi dan kapasitas produksi perikanan/pangan yang memadai namun tingkat PoU tetap tinggi (mencapai puncak 49.84% pada 2021).
- Diproyeksikan mengalami penurunan perlahan dari 34.19% (2025) ke 33.44% (2028).

**Korelasi Lintas Metode:**
- **Metode 01 (Autokorelasi Spasial):** Membentuk pasangan *High-High Hotspot* bersama Kepulauan Sula di gugus kepulauan sula-taliabu.
- **Metode 03 (GTWR):** Memiliki koefisien **Jarak ke Pelabuhan Induk (+3.05)** tertinggi di seluruh dataset, membuktikan bahwa kendala utama paradoks ini adalah konektivitas logistik distribusi laut.

---

### ④ Seram Bagian Barat — *"Korelasi Kuadran Paradoks Clustering & Anomali Trend"*

| Tahun | 2020 | 2021 | 2022 | 2023 | 2024 | 2025 | 2026* | 2027* | 2028* |
|-------|------|------|------|------|------|------|-------|-------|-------|
| **PoU (%)** | 43.30 | 39.20 | 37.77 | 32.70 | 32.71 | 32.94 | 33.54 | 33.51 | 33.54 |
| *95% CI* | — | — | — | — | — | — | [27.51, 39.57] | [24.99, 42.03] | [23.10, 43.98] |

**Peran & Karakteristik:**
- Menunjukkan tren kecenderungan naik pada periode 2025–2028 (+0.60 pp), bergeser dari Peringkat #8 (2025) ke Peringkat #5 paling rawan (2028).

**Korelasi Lintas Metode:**
- **Metode 02 (Clustering Tipologi):** Terkonfirmasi secara ekonometris sebagai anggota **Kuadran Paradoks K-Means** (wilayah dengan kapasitas produksi perikanan di atas median tetapi PoU tetap di atas median).
- **Metode 03 (SEM Spasial):** Menunjukkan koefisien lokal Kunjungan Kapal ($X_{13b} = -2.55$) yang signifikan, mengindikasikan ketergantungan tinggi pada kelancaran rantai pasok maritim.

---

### ⑤ Maluku Tengah — *"Korelasi Kuadran Paradoks & Rebound Tertinggi"*

| Tahun | 2020 | 2021 | 2022 | 2023 | 2024 | 2025 | 2026* | 2027* | 2028* |
|-------|------|------|------|------|------|------|-------|-------|-------|
| **PoU (%)** | 43.07 | 35.10 | 36.90 | 34.62 | 34.30 | 30.26 | 32.78 | 33.05 | 33.03 |
| *95% CI* | — | — | — | — | — | — | [26.75, 38.80] | [24.52, 41.57] | [22.59, 43.47] |

**Peran & Karakteristik:**
- Mengalami **proyeksi kenaikan PoU tertinggi se-Kepulauan Maluku** dari 2025 ke 2028 (+2.77 pp), melonjak dari Peringkat #14 ke Peringkat #7 terburuk.

**Korelasi Lintas Metode:**
- **Metode 02 (Clustering Tipologi):** Juga terkonfirmasi sebagai anggota **Kuadran Paradoks K-Means** bersama Seram Bagian Barat.
- Kombinasi status *Kuadran Paradoks* di clustering serta *Proyeksi Rebound* di forecasting menjadikan Maluku Tengah kasus paling krusial untuk intervensi stabilitas pasokan pangan.

---

## Tabel Sintesis Komparatif 5 Kabupaten/Kota Kunci

| No | Kabupaten/Kota | PoU 2020 | PoU 2025 | Forecast 2028 | Δ (2025→2028) | Peran Utama & Korelasi Lintas Metode |
|----|----------------|----------|----------|---------------|---------------|--------------------------------------|
| ① | **Kepulauan Sula** | 43.47% | 37.36% | 35.52% | −1.84 pp | Terburuk Konsisten (#1), LISA High-High Hotspot, Krisis Senyap NLP |
| ② | **Halmahera Tengah** | 31.64% | 19.03% | 21.32% | +2.29 pp | Terbaik (#21), LISA Low-Low Coldspot Persisten (4/6 thn) |
| ③ | **Pulau Taliabu** | 44.39% | 34.19% | 33.44% | −0.75 pp | Paradoks Produksi Eksplisit, LISA Hotspot, GTWR Jarak Pelabuhan (+3.05) |
| ④ | **Seram Bagian Barat** | 43.30% | 32.94% | 33.54% | +0.60 pp | Kuadran Paradoks K-Means, Diproyeksi Naik, Rank #8 → #5 |
| ⑤ | **Maluku Tengah** | 43.07% | 30.26% | 33.03% | +2.77 pp | Kuadran Paradoks K-Means, Proyeksi Rebound Naik Tertinggi (+2.77pp) |

---

### 📊 Visualisasi Analisis 5 Kabupaten/Kota Kunci

**Gambar 51:** Tren PoU Multi-Line 2020–2028 (5 Kab/Kota Kunci)
> File: `figures/51_tren_pou_5kabkota_unik_2020_2028.png`
- Grafik multi-line berlatar belakang putih (*print-friendly*)
- Area prediksi (2026–2028) ditandai dengan zona oranye muda (*shading*) & batas garis putus-putus
- Pita pita ketidakpastian CI 95% untuk periode proyeksi
- Label nilai persentase PoU pada tahun 2020 dan 2028

**Gambar 52:** Panel Individual 5 Sub-Chart Per Kabupaten/Kota
> File: `figures/52_panel_5kabkota_individual_2020_2028.png`
- Panel individual 5 sub-chart per kabupaten/kota kunci
- Menampilkan angka PoU historis (2020–2025) dan proyeksi (2026–2028)
- Menampilkan delta perubahan total (2020→2028) pada kotak label di tiap panel

---
*Dihasilkan dari pipeline forecasting XGBoost tim GEMASTIK XIX / 2026*


