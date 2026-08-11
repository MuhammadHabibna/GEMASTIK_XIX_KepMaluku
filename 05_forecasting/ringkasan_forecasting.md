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

Dari 21 kabupaten/kota, berikut dipilih **5 kasus yang paling informatif dan unik** secara trajektori PoU, korelasi lintas metode, dan implikasi kebijakan. Data historis mencakup 2020–2025; proyeksi 2026–2028 dihasilkan model XGBoost Panel (MAE backtesting = 2.245).

---

### ① Kepulauan Sula — *"Terburuk Konsisten: Paradoks Produksi Terparah"*

| Tahun | 2020 | 2021 | 2022 | 2023 | 2024 | 2025 | 2026* | 2027* | 2028* |
|-------|------|------|------|------|------|------|-------|-------|-------|
| **PoU (%)** | 43.47 | 42.17 | 42.43 | 44.40 | 35.11 | 37.36 | 34.58 | 35.31 | 35.52 |
| *95% CI* | — | — | — | — | — | — | [28.6, 40.6] | [26.8, 43.8] | [25.1, 46.0] |

**Mengapa unik — Terburuk Konsisten:**
- Meraih **Peringkat #1 PoU tertinggi** pada 2025 (37.36%) dan diperkirakan **tetap di Peringkat #1 pada 2028** (35.52%), satu-satunya kabupaten tanpa pergeseran peringkat di klaster teratas.
- Penurunan tertunda di 2026 (34.58) namun kembali merayap naik di 2027–2028, mengindikasikan **pola "rebound" yang mengkhawatirkan** dan tidak ada koreksi struktural.
- Volatilitas sedang (StdDev YoY = 4.22) namun trajektori tidak konvergen ke angka yang aman.

**Korelasi lintas metode:**
- **Metode 01 (Autokorelasi Spasial):** Terdeteksi sebagai bagian klaster *High-High* di LISA 2025, artinya kabupaten tetangga (Taliabu, Halmahera Tengah) juga memiliki PoU tinggi — efek penguatan spasial.
- **Metode 02 (Clustering Tipologi):** Masuk **Klaster C1 — Miskin Terisolasi** (IPM rendah, akses terbatas, kemiskinan tinggi).
- **Metode 03 (Regresi Spasial/GTWR):** Koefisien lokal PDRB = −3.10 dan **Jarak ke Pelabuhan Induk = +2.75** (tertinggi di seluruh Maluku Utara), membuktikan isolasi maritim fisik sebagai hambatan struktural utama.
- **Metode 04 (NLP):** Masuk kategori **"Krisis Senyap"** — volume wacana digital sangat rendah, kerawanan pangan tidak terdeteksi lewat kanal berita arus utama.

**Implikasi kebijakan:** Prioritas intervensi logistik (subsidi ongkos angkut, kapal perintis reguler ke pelabuhan Sanana) bersama program bantuan sosial berbasis geostatistik.

---

### ② Halmahera Tengah — *"Terbaik / Underdog: Penurunan Tercepat Tanpa Modal Awal"*

| Tahun | 2020 | 2021 | 2022 | 2023 | 2024 | 2025 | 2026* | 2027* | 2028* |
|-------|------|------|------|------|------|------|-------|-------|-------|
| **PoU (%)** | 31.64 | 27.03 | 25.55 | 15.54 | 19.43 | 19.03 | 21.97 | 21.48 | 21.32 |
| *95% CI* | — | — | — | — | — | — | [15.9, 28.0] | [13.0, 30.0] | [10.9, 31.8] |

**Mengapa unik — Terbaik / Underdog:**
- Memiliki **PoU terendah se-Kepulauan Maluku pada 2025 (19.03%)** dan akan mempertahankan posisi terbawah di 2028 (21.32%).
- Dikategorikan sebagai **Underdog** (residual = −9.02): kapasitas produksi lokal dinilai rendah oleh model, namun PoU aktual jauh lebih rendah dari yang diprediksi — bukti efektivitas kebijakan lokal atau konektivitas yang belum terukur oleh proksi produksi perikanan.
- Catatan: sempat mencapai PoU 15.54% pada 2023 (titik terendah di seluruh dataset), namun sedikit rebound di 2024–2025.
- Model memproyeksikan sedikit kenaikan di 2026 (21.97%) sebelum stabil — ini merupakan **"warning early sign"** bahwa kemajuan mungkin reversibel tanpa penguatan program.

**Korelasi lintas metode:**
- **Metode 03 (GTWR):** Koefisien lokal PDRB = −2.71 dan Jarak Pelabuhan = +2.18 — tekanan isolasi ada, namun nilai koefisiennya lebih rendah dibanding Kepulauan Sula, menunjukkan ketahanan relatif lebih kuat.
- **Metode 02 (Clustering):** Masuk **Klaster C2 — Sentra Urban** — aksesibilitas dan IPM lebih tinggi dari rata-rata kepulauan, mendukung penurunan PoU yang cepat.

**Implikasi kebijakan:** Dokumentasi "best practice" Halmahera Tengah untuk direplikasi ke kabupaten tetangga; waspadai proyeksi kenaikan di 2026 sebagai sinyal perlunya intervensi penguatan program gizi berkelanjutan.

---

### ③ Buru Selatan — *"Paradoks Produksi: Kaya SDA, Miskin Pangan"*

| Tahun | 2020 | 2021 | 2022 | 2023 | 2024 | 2025 | 2026* | 2027* | 2028* |
|-------|------|------|------|------|------|------|-------|-------|-------|
| **PoU (%)** | 47.80 | 40.88 | 30.00 | 38.90 | 34.20 | 36.17 | 34.23 | 34.65 | 33.88 |
| *95% CI* | — | — | — | — | — | — | [28.2, 40.3] | [26.1, 43.2] | [23.4, 44.3] |

**Mengapa unik — Paradoks Produksi:**
- Memiliki **residual paradoks = +6.22** (peringkat #2 tertinggi): kapasitas produksi pangan/perikanan lokal tinggi, namun PoU jauh melampaui ekspektasi model.
- Trajektori sangat **tidak stabil** — volatilitas tertinggi se-Kepulauan Maluku (StdDev YoY = 7.26), dengan lompatan dari 30.00% (2022) ke 38.90% (2023) dan kembali ke 34.20% (2024).
- Proyeksi 2026–2028 menunjukkan penurunan gradual (33.88% di 2028), namun CI yang sangat lebar [23.4–44.3] mencerminkan **ketidakpastian proyeksi tertinggi** dari semua kabupaten.
- Pergeseran peringkat: dari posisi #2 pada 2025 → posisi #4 pada 2028 (Buru Selatan relatif membaik terhadap kabupaten lain).

**Korelasi lintas metode:**
- **Metode 02 (Clustering):** Masuk **Klaster C1 — Miskin Terisolasi** meski memiliki produksi relatif tinggi — memperkuat paradoks bahwa produksi lokal tidak otomatis meningkatkan ketahanan pangan karena hambatan distribusi.
- **Metode 03 (SEM):** Koefisien Kunjungan Kapal (X₁₃b = −2.365) menunjukkan bahwa konektivitas maritim adalah kunci yang hilang — produksi ada, tetapi distribusi ke pasar terhambat oleh frekuensi kapal yang rendah.
- **Metode 04 (NLP):** Masuk **Krisis Senyap** — minim wacana digital; isu kerawanan pangan tidak tersaring sistem monitoring konvensional.

**Implikasi kebijakan:** Paradoks ini mengindikasikan perlunya **intervensi rantai pasok**, bukan hanya peningkatan produksi; program subsidi distribusi dan pasar murah pangan di Buru Selatan.

---

### ④ Seram Bagian Barat — *"Anomali Kenaikan: Satu-Satunya yang Naik di Kelompok Menengah"*

| Tahun | 2020 | 2021 | 2022 | 2023 | 2024 | 2025 | 2026* | 2027* | 2028* |
|-------|------|------|------|------|------|------|-------|-------|-------|
| **PoU (%)** | 43.30 | 39.20 | 37.80 | 32.73 | 32.71 | 32.94 | 33.54 | 33.51 | 33.54 |
| *95% CI* | — | — | — | — | — | — | [27.5, 39.6] | [25.0, 42.0] | [23.1, 44.0] |

**Mengapa unik — Anomali Kenaikan:**
- Satu-satunya kabupaten di **kelompok menengah** yang diproyeksikan **meningkat PoU-nya** dari 2025 ke 2028 (+0.60 poin persen), bergerak naik di tengah tren penurunan rata-rata regional.
- Pergeseran peringkat paling dramatis ke arah **negatif**: dari Peringkat #8 pada 2025 → Peringkat #5 pada 2028 (artinya relatif lebih buruk dari banyak kabupaten lain yang berhasil menurun).
- Proyeksi flat/stagnan di kisaran 33.5% untuk 3 tahun ke depan mengindikasikan **jebakan keseimbangan tinggi** (*high-level equilibrium trap*): tidak memburuk akut, namun tidak ada perbaikan yang terdeteksi.

**Korelasi lintas metode:**
- **Metode 01 (Autokorelasi):** Meskipun secara geografis berdekatan dengan Kota Ambon (PoU rendah), Seram Bagian Barat tidak memperoleh *spillover* positif — ada hambatan struktural yang memblokir difusi ketahanan pangan.
- **Metode 03 (GTWR):** Koefisien Kunjungan Kapal (X₁₃b) = −2.545 mengindikasikan bahwa konektivitas kapal merupakan determinan #2 dominan — penurunan frekuensi kapal pasca-COVID belum pulih sepenuhnya.
- **Metode 03 (SEM):** Terdeteksi efek *spatial error* positif, artinya ada **sumber ketidakpastian spasial** tidak terobservasi yang mengangkat PoU di sekitar Seram Bagian Barat.

**Implikasi kebijakan:** Evaluasi ulang rute dan jadwal kapal perintis antara Seram Bagian Barat–Ambon; pemantauan khusus karena anomali kenaikan ini berpotensi menjadi sinyal dini krisis baru.

---

### ⑤ Maluku Tengah — *"Gejolak Temporal: Turun Tajam, Lalu Berbalik Naik"*

| Tahun | 2020 | 2021 | 2022 | 2023 | 2024 | 2025 | 2026* | 2027* | 2028* |
|-------|------|------|------|------|------|------|-------|-------|-------|
| **PoU (%)** | 43.07 | 35.10 | 36.86 | 36.92 | 34.30 | 30.26 | 32.78 | 33.05 | 33.03 |
| *95% CI* | — | — | — | — | — | — | [26.7, 38.8] | [24.5, 41.6] | [22.6, 43.5] |

**Mengapa unik — Gejolak Temporal:**
- Mencetak penurunan PoU terbesar secara historis: **−12.81 poin persen** dari 2020 (43.07%) ke 2025 (30.26%).
- Namun, model memproyeksikan **pembalikan arah (*reversal*)** di 2026–2028: PoU kembali naik ke 33.03% (+2.77 pp dari 2025) — **peningkatan PoU terbesar** yang diproyeksikan dari seluruh 21 kabupaten/kota.
- Pergeseran peringkat **paling dramatis ke arah negatif**: dari Peringkat #14 (2025) → Peringkat #7 (2028) — melompat 7 posisi menjadi lebih buruk.
- Volatilitas YoY moderat (StdDev = 3.33), namun pembalikan arah yang curam ini bersifat **struktural**: tren koefisien GTWR dari 2020 ke 2025 menunjukkan melemahnya efek perlindungan PDRB dan Kunjungan Kapal.

**Korelasi lintas metode:**
- **Metode 02 (Clustering):** Maluku Tengah masuk **Klaster C0 — Rawan Konsumsi** meski secara spasial berdekatan dengan Kota Ambon — hubungan dekat secara geografis tidak cukup untuk mengangkat ketahanan pangan jika struktur ekonomi lokal tidak mendukung.
- **Metode 03 (GTWR):** Koefisien Kunjungan Kapal (X₁₃b) = −2.524 — konektivitas maritim yang membaik 2020–2025 mendorong penurunan PoU, namun jika konektivitas stagnan atau turun, proyeksi rebound akan terjadi persis seperti yang diprediksi model.
- **Metode 05 (Forecasting, Feature Importance):** Fitur `kab_hist_mean` (43.2% kontribusi) yang menangkap karakteristik dasar kabupaten, menyimpan "memori" struktural bahwa Maluku Tengah secara historis cenderung berada di atas rata-rata PoU — inilah yang menarik proyeksi kembali naik.

**Implikasi kebijakan:** Penurunan 2020–2025 tidak boleh dijadikan indikator keberhasilan permanen — perlu program penguatan struktural (konektivitas kapal reguler, peningkatan akses pasar, diversifikasi sumber pendapatan) untuk mencegah rebound yang diprediksi model.

---

## Ringkasan Komparatif 5 Kabupaten/Kota Kunci

| No | Kabupaten/Kota | PoU 2020 | PoU 2025 | Forecast 2028 | Δ (2025→2028) | Δ Total (2020→2028) | Klasifikasi |
|----|----------------|----------|----------|---------------|---------------|---------------------|-------------|
| ① | Kepulauan Sula | 43.47% | 37.36% | 35.52% | −1.84 pp | −7.95 pp | Paradoks #1, Terburuk Konsisten |
| ② | Halmahera Tengah | 31.64% | 19.03% | 21.32% | +2.29 pp | −10.32 pp | Underdog Terbaik, Terbawah |
| ③ | Buru Selatan | 47.80% | 36.17% | 33.88% | −2.30 pp | −13.92 pp | Paradoks #2, Volatilitas Tertinggi |
| ④ | Seram Bagian Barat | 43.30% | 32.94% | 33.54% | +0.60 pp | −9.76 pp | Anomali Kenaikan, Rank −3 |
| ⑤ | Maluku Tengah | 43.07% | 30.26% | 33.03% | +2.77 pp | −10.04 pp | Rebound Terbesar, Rank −7 |

> **Catatan Penting:** Kabupaten ② (Halmahera Tengah) dan ③ (Buru Selatan) keduanya naik sedikit di 2028, meskipun trajektori jangka panjang tetap turun signifikan dari 2020. Kabupaten ④ dan ⑤ justru memerlukan perhatian lebih karena naik dari level 2025.

---

### 📊 Visualisasi Analisis 5 Kabupaten/Kota Kunci

**Gambar 51:** Tren PoU Multi-Line 2020–2028 (5 Kab/Kota Kunci)
> File: `figures/51_tren_pou_5kabkota_unik_2020_2028.png`
- Menampilkan seluruh 5 trajektori sekaligus pada satu panel
- Area prediksi (2026–2028) ditandai dengan zona kuning (*shading*)
- CI band 95% ditampilkan untuk periode prediksi
- Anotasi nilai PoU di titik awal (2020) dan akhir (2028)

**Gambar 52:** Panel Individual 5 Sub-Chart Per Kabupaten/Kota
> File: `figures/52_panel_5kabkota_individual_2020_2028.png`
- Setiap panel menampilkan nilai PoU per tahun (putih = historis, kuning = prediksi)
- Delta total 2020→2028 ditampilkan sebagai label di pojok kiri bawah tiap panel
- CI band 95% tersedia untuk evaluasi ketidakpastian proyeksi

---
*Dihasilkan dari pipeline forecasting XGBoost tim GEMASTIK XIX / 2026*

