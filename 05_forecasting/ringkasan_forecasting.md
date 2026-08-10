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

### 🖼️ Berkas Visualisasi (`figures/`) — 10 Berkas PNG
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

---
*Dihasilkan dari pipeline forecasting XGBoost tim GEMASTIK XIX / 2026*
