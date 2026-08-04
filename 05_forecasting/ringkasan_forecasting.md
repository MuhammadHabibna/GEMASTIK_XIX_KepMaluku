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