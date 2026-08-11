# Ringkasan Eksperimen Autokorelasi Spasial
## Prevalensi Kerawanan Pangan (PoU) — Kepulauan Maluku 2020–2025
*Output dari analisis autokorelasi spasial (Moran's I, LISA, Getis-Ord Gi*)*

---

## 1. Data dan Setup

- **Unit analisis:** 21 kabupaten/kota Kepulauan Maluku (Provinsi Maluku + Maluku Utara)
- **Rentang waktu:** 2020–2025 (panel 6 tahun)
- **Variabel utama (Y):** PoU (Prevalensi Kerawanan Pangan, %)
- **Matriks bobot spasial:** KNN adaptif dengan analisis sensitivitas k=2,3,4,5,6
- **Library:** PySAL (`esda`, `libpysal`), GeoPandas, Matplotlib, Seaborn
- **GeoJSON:** `data/batas_21_kabkota_kepulauan_maluku.geojson`

---

## 2. Ranking PoU 2025 (21 Kab/Kota)

| Rank | Kab/Kota | Provinsi | PoU (%) |
|------|----------|----------|---------|
| 1 | Kepulauan Sula | Maluku Utara | 37.36 |
| 2 | Buru Selatan | Maluku | 36.17 |
| 3 | Halmahera Barat | Maluku Utara | 36.09 |
| 4 | Kepulauan Tanimbar | Maluku | 35.76 |
| 5 | Pulau Taliabu | Maluku Utara | 34.19 |
| 6 | Seram Bagian Timur | Maluku | 33.63 |
| 7 | Kepulauan Aru | Maluku | 33.31 |
| 8 | Seram Bagian Barat | Maluku | 32.94 |
| 9 | Buru | Maluku | 32.36 |
| 10 | Maluku Tenggara | Maluku | 30.99 |
| ... | ... | ... | ... |
| 19 | Kota Ambon | Maluku | 24.78 |
| 20 | Kota Ternate | Maluku Utara | 21.69 |
| 21 | Halmahera Tengah | Maluku Utara | **19.03** (terendah) |

---

## 3. Moran's I Global (Autokorelasi Spasial PoU)

### Per Tahun (2020–2025)

| Tahun | Moran's I | z-score | p-value | Signifikan | Interpretasi |
|-------|-----------|---------|---------|------------|--------------|
| 2020 | 0.0296 | 0.606 | 0.244 | Tidak | Pola acak |
| 2021 | 0.1374 | 1.357 | 0.108 | Tidak | Pola acak |
| 2022 | 0.0654 | 0.897 | 0.174 | Tidak | Pola acak |
| 2023 | 0.1798 | 1.917 | **0.041** | **Ya** | Klaster spasial positif |
| 2024 | 0.2861 | 2.713 | **0.016** | **Ya** | Klaster spasial positif |
| 2025 | 0.2826 | 2.697 | **0.014** | **Ya** | Klaster spasial positif |

**Kesimpulan:** Autokorelasi spasial PoU **tidak ada di 2020–2022**, mulai muncul signifikan di **2023** dan **menguat di 2024–2025** (I=0.28, p=0.014). Tren ini menunjukkan klasterisasi spasial PoU yang semakin nyata dalam 3 tahun terakhir.

### Sensitivitas terhadap k (KNN)

Nilai Moran's I relatif stabil lintas k=2 hingga k=6, mengindikasikan hasil tidak sensitif terhadap pilihan bandwidth matriks bobot (lihat `tables/sensitivitas_k_moran.csv`).

---

## 4. LISA (Local Indicators of Spatial Association) 2025

### Klaster Lokal Signifikan (p < 0.05)

| Kab/Kota | Provinsi | PoU | Local I | p-value | Tipe Klaster |
|----------|----------|-----|---------|---------|--------------|
| **Halmahera Selatan** | Maluku Utara | 26.77 | 0.9566 | **0.002** | **Low-Low** |
| **Halmahera Tengah** | Maluku Utara | 19.03 | 2.0378 | **0.007** | **Low-Low** |
| **Kota Tidore Kepulauan** | Maluku Utara | 28.68 | 0.3393 | **0.024** | **Low-Low** |
| **Halmahera Barat** | Maluku Utara | 36.09 | -1.0933 | **0.030** | **High-Low** |

**Interpretasi:**
- **Low-Low cluster** (coldspot PoU): Halmahera Selatan, Halmahera Tengah, Kota Tidore Kepulauan — wilayah dengan PoU rendah dikelilingi tetangga PoU rendah (klaster ketahanan pangan relatif baik di Maluku Utara bagian tengah-selatan).
- **High-Low outlier**: Halmahera Barat — PoU tinggi (36.09%) di tengah tetangga PoU rendah, menonjol sebagai daerah rawan yang terisolasi.

### Persistensi LISA (2020–2025)

| Kab/Kota | Tipe Dominan | Tahun Signifikan |
|----------|-------------|-----------------|
| Halmahera Selatan | Low-Low (5x) + High-Low (1x) | **6/6** |
| Halmahera Tengah | Low-Low (4x) | 4/6 |
| Kota Tidore Kepulauan | Low-Low (3x) | 3/6 |
| Kepulauan Sula | High-High (2x) | 2/6 |
| Pulau Taliabu | High-High (2x) | 2/6 |
| Halmahera Barat | High-Low (2x) | 2/6 |
| Kota Ambon | Low-High (1x) | 1/6 |
| Kota Tual | Low-High (1x) | 1/6 |

**Halmahera Selatan** adalah satu-satunya kab/kota yang signifikan di **semua 6 tahun** — klaster coldspot paling persisten di Kepulauan Maluku.

---

## 5. Getis-Ord Gi* 2025 (Hot/Coldspot)

### Signifikan di 2025

| Kab/Kota | PoU | Gi* (Z) | p-value | Klasifikasi |
|----------|-----|---------|---------|-------------|
| Halmahera Tengah | 19.03 | -1.193 | **0.007** | **Coldspot** |
| Halmahera Selatan | 26.77 | -1.193 | **0.002** | **Coldspot** |
| Kota Ternate | 21.69 | -0.901 | **0.049** | **Coldspot** |
| Kota Tidore Kepulauan | 28.68 | -0.841 | **0.024** | **Coldspot** |
| Halmahera Barat | 36.09 | -0.533 | **0.030** | **Coldspot** |

**Tidak ada hotspot PoU yang signifikan di 2025.** Semua yang signifikan adalah coldspot — klaster PoU rendah di Halmahera bagian tengah-selatan dan kota-kota di Maluku Utara.

### Persistensi Getis-Ord (2020–2025)

| Kab/Kota | Coldspot (6 tahun) |
|----------|--------------------|
| **Halmahera Selatan** | **6/6** (paling konsisten) |
| Halmahera Tengah | 4/6 |
| Kota Tidore Kepulauan | 3/6 |
| Halmahera Barat | 2/6 |
| Kota Ternate | 1/6 |

---

## 6. Moran Bivariat 2025

| Pasangan | Moran Bivariat I | p-value | Signifikan |
|----------|-----------------|---------|------------|
| PoU vs Produksi Perikanan (X1) | -0.0493 | 0.339 | Tidak |
| PoU vs Indeks Konektivitas Maritim (IKM) | -0.1282 | 0.165 | Tidak |
| **PoU vs % Penduduk Miskin (X5)** | **0.2574** | **0.023** | **Ya** |

**PoU berkorelasi spasial positif dengan kemiskinan (X5)** — wilayah miskin cenderung bertetangga dengan wilayah miskin yang juga punya PoU tinggi. Konektivitas maritim dan produksi perikanan tidak menunjukkan pola autokorelasi bivariat yang signifikan.

---

## 7. Temuan Utama

1. **Klasterisasi PoU baru muncul signifikan mulai 2023** dan menguat di 2024–2025 (Moran I=0.28, p=0.014) — bukan karakteristik statis jangka panjang melainkan fenomena yang berkembang.
2. **Klaster Low-Low (aman pangan) dominan di Maluku Utara tengah-selatan:** Halmahera Selatan, Halmahera Tengah, Kota Tidore Kepulauan, Kota Ternate — klaster ini persisten lintas tahun.
3. **Tidak ada hotspot PoU tinggi yang signifikan** di 2025 — kabupaten dengan PoU tertinggi (Kepulauan Sula 37.36%, Buru Selatan 36.17%) belum membentuk klaster spasial yang terdeteksi (LISA p > 0.1).
4. **Halmahera Barat** adalah anomali: PoU tinggi (36.09%) tapi dikelilingi tetangga PoU rendah (High-Low outlier) — daerah rawan yang terisolasi, perlu perhatian kebijakan khusus.
5. **Kemiskinan (X5) adalah satu-satunya variabel yang menunjukkan autokorelasi spasial bivariat signifikan** dengan PoU (I=0.26, p=0.023) — konsisten dengan temuan GWR.

---

## 8. Daftar Berkas Tabel dan Gambar (Available Files)

Semua berkas output tersimpan terstruktur dalam folder `tables/` dan `figures/`:

### 📊 Tabel Hasil Analisis (`tables/`) — 13 Berkas CSV
1. `moran_global_per_tahun.csv` — Nilai indeks Moran's I global, z-score, p-value, dan kesimpulan autokorelasi tahun 2020–2025.
2. `lisa_hasil_2025.csv` — Hasil autokorelasi spasial lokal LISA 2025 per kabupaten/kota (Local I, p-value, tipe quadrant klaster).
3. `lisa_panel_2020_2025.csv` — Hasil autokorelasi spasial lokal LISA lengkap untuk seluruh 21 kabupaten/kota selama 6 tahun panel (126 observasi).
4. `getis_ord_gi_2025.csv` — Nilai statistik Getis-Ord Gi* Z-score dan p-value hot/coldspot tahun 2025 per kabupaten/kota.
5. `getis_ord_panel_2020_2025.csv` — Nilai Getis-Ord Gi* panel lengkap tahun 2020–2025.
6. `persistensi_lisa.csv` — Frekuensi dan persistensi keanggotaan tipe klaster LISA per kabupaten/kota selama rentang waktu 6 tahun.
7. `persistensi_getis_ord.csv` — Rekapitulasi jumlah tahun suatu kabupaten/kota terklasifikasi sebagai hot/coldspot Getis-Ord Gi*.
8. `moran_bivariat.csv` — Nilai Moran's I bivariat antara PoU dengan X1 (Perikanan), X5 (Kemiskinan), dan IKM (Konektivitas Maritim) tahun 2025.
9. `moran_bivariat_panel_2020_2025.csv` — Evaluasi Moran's I bivariat lintas tahun panel 2020–2025.
10. `ringkasan_panel_moran.csv` — Summary gabungan metrik Moran's I univariat dan bivariat panel 2020–2025.
11. `sensitivitas_k_moran.csv` — Hasil pengujian sensitivitas matriks bobot k-nearest neighbors (k=2 s.d. k=6) terhadap stabilitas Moran's I.
12. `ranking_pou_2025.csv` — Tabel peringkat Prevalensi Kerawanan Pangan (PoU) 21 kabupaten/kota Kepulauan Maluku tahun 2025.
13. `dataset_bersih_kepulauan_maluku.csv` — Dataset panel 21 kabupaten/kota (2020–2025) setelah pembersihan dan imputasi data.

### 🖼️ Gambar Visualisasi (`figures/`) — 22 Berkas PNG
1. `01_missing_heatmap_before.png` — Heatmap pola missing data pada indikator awal sebelum pembersihan.
2. `02_distribusi_pou_before.png` — Histogram dan boxplot distribusi nilai PoU sebelum proses imputasi.
3. `03_korelasi_raw.png` — Heatmap matriks korelasi antar-indikator pada dataset awal.
4. `04_missing_before_after.png` — Diagram perbandingan tingkat kelengkapan data sebelum dan sesudah cleaning.
5. `05_korelasi_after.png` — Heatmap korelasi antar-indikator setelah imputasi dan cleaning data.
6. `06_tren_pou_kabkota.png` — Line plot pergerakan tren PoU 21 kabupaten/kota dari tahun 2020 hingga 2025.
7. `07_ranking_pou_latest.png` — Bar chart peringkat PoU 21 kabupaten/kota pada tahun 2025.
8. `08_verifikasi_geojson.png` — Peta verifikasi batas poligon administratif GeoJSON 21 kabupaten/kota Kepulauan Maluku.
9. `09_provinsi_tren_ringkasan.png` — Grafik perbandingan tren rata-rata PoU Provinsi Maluku vs Maluku Utara.
10. `10_choropleth_provinsi.png` — Peta tematik choropleth PoU di tingkat agregat provinsi.
11. `11_sensitivitas_k.png` — Plot analisis sensitivitas stabilitas Moran's I terhadap variasi k-nearest neighbors.
12. `12_tren_moran_global.png` — Line chart tren perubahan nilai Moran's I global dari tahun 2020 hingga 2025.
13. `13_moran_scatterplot.png` — Moran scatterplot tahun 2025 dengan 4 kuadran spasial (HH, LL, HL, LH).
14. `14_lisa_choropleth.png` — Peta choropleth tipe klaster spasial LISA tahun 2025.
15. `14b_lisa_signifikansi_per_tahun.png` — Visualisasi heatmap tingkat p-value signifikansi LISA per kabupaten per tahun.
16. `14c_lisa_evolusi_choropleth.png` — Panel peta choropleth perkembangan klaster LISA dari 2020 hingga 2025.
17. `14d_persistensi_lisa_barchart.png` — Bar chart frekuensi persistensi tipe klaster LISA per kabupaten/kota.
18. `15_getis_ord_barchart.png` — Bar chart Z-score Getis-Ord Gi* hot/coldspot tahun 2025.
19. `15b_getis_ord_persistensi.png` — Visualisasi tingkat persistensi hot/coldspot Getis-Ord Gi* (2020–2025).
20. `16_moran_bivariat_scatter.png` — Scatterplot Moran's I bivariat PoU vs Kemiskinan (X5), Perikanan (X1), dan IKM.
21. `16b_tren_moran_bivariat.png` — Line plot tren temporal Moran's I bivariat (2020–2025).
22. `17_choropleth_time_series.png` — Seri peta choropleth PoU 21 kabupaten/kota untuk seluruh 6 tahun panel.

---

## 10. Wilayah Prioritas — Top 5 Kab/Kota dari Perspektif Autokorelasi Spasial

Identifikasi berikut berdasarkan kombinasi: (a) peringkat PoU 2025, (b) persistensi klaster LISA, dan (c) persistensi Getis-Ord Gi*.

| Rank | Kab/Kota | PoU 2025 | Klaster LISA | Getis-Ord | Relevansi Metode Lain |
|------|----------|----------|--------------|-----------|-----------------------|
| 1 | **Kepulauan Sula** | 37.36% (#1) | High-High persisten (2× / 6 tahun) | Hotspot 2× / 6 tahun | Clustering C0 Paradoks; GTWR isolasi maritim berat; Forecasting #1 terburuk tetap 2028 |
| 2 | **Pulau Taliabu** | 34.19% (#5) | High-High persisten (2× / 6 tahun) | Hotspot 2× / 6 tahun | GTWR jarak pelabuhan tertinggi (+3.04); NLP hanya 20 dok (Krisis Senyap Super) |
| 3 | **Halmahera Selatan** | 26.77% (#17) | Low-Low paling stabil (6× / 6 tahun) | Coldspot 6× / 6 tahun | *Wilayah terbaik secara spasial* — konsisten sebagai zona PoU rendah di kawasan |
| 4 | **Halmahera Tengah** | 19.03% (#21) | Low-Low (4× / 6 tahun) | Coldspot 4× / 6 tahun | Clustering C3 Enklave Industri; PDRB tertinggi tetapi PoU mulai naik di Forecasting 2028 |
| 5 | **Seram Bagian Barat** | 32.94% (#8) | Tidak signifikan | Tidak signifikan | Kapasitas produksi pangan tertinggi ke-3 tetapi PoU tinggi → paradoks distribusi; Forecasting memburuk |

**Catatan interpretasi:**
- Kepulauan Sula dan Pulau Taliabu adalah satu-satunya dua wilayah yang *konsisten* muncul sebagai High-High Hotspot LISA *dan* Hotspot Getis-Ord secara berulang. Ini menunjukkan klasterisasi kerawanan pangan yang saling menguatkan dengan tetangga terdekat.
- Halmahera Selatan adalah *benchmark* keberhasilan: PoU rendah, konsisten, dan secara spasial menjadi "penarik turun" PoU wilayah sekitarnya.
- Absennya Maluku Tengah dari daftar LISA/Getis-Ord yang signifikan *tidak berarti aman* — ia diidentifikasi oleh Forecasting sebagai wilayah yang akan melonjak paling tajam (peringkat naik 7 posisi pada 2028).

---
*Dihasilkan dari analisis dengan PySAL oleh tim GEMASTIK XIX / 2026*
