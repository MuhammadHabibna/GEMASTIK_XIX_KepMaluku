# Ringkasan Eksperimen Autokorelasi Spasial
## Prevalensi Kerawanan Pangan (PoU) -- Kepulauan Maluku 2020-2025
*Output dari analisis autokorelasi spasial (Moran, LISA, Getis-Ord Gi*)*

---

## 1. Data dan Setup

- **Unit analisis:** 21 kabupaten/kota Kepulauan Maluku (Maluku + Maluku Utara)
- **Rentang waktu:** 2020-2025 (panel 6 tahun)
- **Variabel utama (Y):** PoU (Prevalensi Kerawanan Pangan, %)
- **Matriks bobot spasial:** KNN adaptif dengan analisis sensitivitas k=2,3,4,5,6
- **Library:** PySAL (esda, libpysal)
- **GeoJSON:** `batas_21_kabkota_kepulauan_maluku.geojson`

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

### Per Tahun (2020-2025)

| Tahun | Moran's I | z-score | p-value | Signifikan | Interpretasi |
|-------|-----------|---------|---------|------------|--------------|
| 2020 | 0.0296 | 0.606 | 0.244 | Tidak | Pola acak |
| 2021 | 0.1374 | 1.357 | 0.108 | Tidak | Pola acak |
| 2022 | 0.0654 | 0.897 | 0.174 | Tidak | Pola acak |
| 2023 | 0.1798 | 1.917 | **0.041** | **Ya** | Klaster spasial positif |
| 2024 | 0.2861 | 2.713 | **0.016** | **Ya** | Klaster spasial positif |
| 2025 | 0.2826 | 2.697 | **0.014** | **Ya** | Klaster spasial positif |

**Kesimpulan:** Autokorelasi spasial PoU **tidak ada di 2020-2022**, mulai muncul signifikan di **2023** dan **menguat di 2024-2025** (I=0.28, p=0.014). Tren ini menunjukkan klasterisasi spasial PoU yang semakin nyata dalam 3 tahun terakhir.

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
- **Low-Low cluster** (coldspot PoU): Halmahera Selatan, Halmahera Tengah, Kota Tidore Kepulauan -- wilayah dengan PoU rendah dikelilingi tetangga PoU rendah (klaster ketahanan pangan relatif baik di Maluku Utara bagian tengah-selatan)
- **High-Low outlier**: Halmahera Barat -- PoU tinggi (36.09%) di tengah tetangga PoU rendah, menonjol sebagai daerah rawan yang terisolasi

### Persistensi LISA (2020-2025)

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

**Halmahera Selatan** adalah satu-satunya kab/kota yang signifikan di **semua 6 tahun** -- klaster coldspot paling persisten di Kepulauan Maluku.

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

**Tidak ada hotspot PoU yang signifikan di 2025.** Semua signifikan adalah coldspot -- klaster PoU rendah di Halmahera bagian tengah-selatan dan kota-kota di Maluku Utara.

### Persistensi Getis-Ord (2020-2025)

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

**PoU berkorelasi spasial positif dengan kemiskinan (X5)** -- wilayah miskin cenderung bertetangga dengan wilayah miskin yang juga punya PoU tinggi. Konektivitas maritim dan produksi perikanan tidak menunjukkan pola autokorelasi bivariat yang signifikan.

---

## 7. Temuan Utama

1. **Klasterisasi PoU baru muncul signifikan mulai 2023** dan menguat di 2024-2025 (Moran I=0.28, p=0.014) -- bukan karakteristik statis jangka panjang melainkan fenomena yang berkembang.
2. **Klaster Low-Low (aman pangan) dominan di Maluku Utara tengah-selatan:** Halmahera Selatan, Halmahera Tengah, Kota Tidore Kepulauan, Kota Ternate -- klaster ini persisten lintas tahun.
3. **Tidak ada hotspot PoU tinggi yang signifikan** di 2025 -- kabupaten dengan PoU tertinggi (Kepulauan Sula 37.36%, Buru Selatan 36.17%) belum membentuk klaster spasial yang terdeteksi (LISA p > 0.1).
4. **Halmahera Barat** adalah anomali: PoU tinggi (36.09%) tapi dikelilingi tetangga PoU rendah (High-Low outlier) -- daerah rawan yang terisolasi, perlu perhatian kebijakan khusus.
5. **Kemiskinan (X5) adalah satu-satunya variabel yang menunjukkan autokorelasi spasial bivariat signifikan** dengan PoU (I=0.26, p=0.023) -- konsisten dengan temuan GWR.

---

## 8. File Output

### Tabel (tables/)
- `moran_global_per_tahun.csv` -- Moran's I per tahun 2020-2025
- `lisa_hasil_2025.csv` -- LISA per kab/kota 2025
- `lisa_panel_2020_2025.csv` -- LISA panel lengkap
- `getis_ord_gi_2025.csv` -- Getis-Ord Gi* 2025
- `getis_ord_panel_2020_2025.csv` -- Gi* panel lengkap
- `persistensi_lisa.csv` -- persistensi klaster LISA 6 tahun
- `persistensi_getis_ord.csv` -- persistensi hotspot/coldspot 6 tahun
- `moran_bivariat.csv` -- Moran bivariat PoU vs X1, X5, IKM (2025)
- `moran_bivariat_panel_2020_2025.csv` -- Moran bivariat panel
- `ringkasan_panel_moran.csv` -- ringkasan Moran's I 2020-2025
- `sensitivitas_k_moran.csv` -- sensitivitas terhadap pilihan k
- `ranking_pou_2025.csv` -- ranking PoU 21 kab/kota 2025
- `dataset_bersih_kepulauan_maluku.csv` -- dataset akhir setelah cleaning

### Gambar (figures/)
- Fig 01-05: EDA, missing data, distribusi, korelasi
- Fig 06-10: Tren PoU, ranking, choropleth provinsi, verifikasi GeoJSON
- Fig 11: Sensitivitas k Moran's I
- Fig 12: Tren Moran's I global 2020-2025
- Fig 13: Moran scatterplot 2025
- Fig 14, 14b, 14c, 14d: LISA choropleth + evolusi + persistensi
- Fig 15, 15b: Getis-Ord barchart + persistensi
- Fig 16, 16b: Moran bivariat scatter + tren panel
- Fig 17: Choropleth time series (panel 2020-2025)

---
*Dihasilkan dari analisis dengan PySAL oleh anggota tim GEMASTIK 2026*
