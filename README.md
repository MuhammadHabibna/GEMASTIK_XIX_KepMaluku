# GEMASTIK XIX — Analisis Determinan Lokal Kerawanan Pangan Kepulauan Maluku

**Tim:** Universitas Negeri Surabaya (UNESA)  
**Kompetisi:** GEMASTIK XIX 2026 — Kategori Sains Data  
**Fokus:** 21 Kabupaten/Kota di Kepulauan Maluku (Maluku + Maluku Utara), 2020–2025

---

## 📂 Struktur Repository

```
├── data/                         # Dataset mentah dan hasil preprocessing
├── dev/                          # Script pengolahan data
├── 01_autokorelasi_spasial/      # Analisis Moran's I, LISA, Getis-Ord Gi*
│   ├── figures/                  # Visualisasi peta klaster, scatterplot Moran
│   ├── tables/                   # Output statistik autokorelasi per tahun
│   └── ringkasan_autokorelasi_spasial.md
├── 02_clustering_spasial/        # K-Means, K-Medoids, SKATER
│   ├── figures/
│   └── tables/
├── 03_regresi_spasial_lokal/     # OLS, SAR, SEM, GWR Parsimoni, GTWR Panel
│   ├── figures/
│   ├── tables/
│   ├── regresi_lokal_gwr_gtwr_pou_maluku.ipynb
│   └── ringkasan_gwr_mgwr.md
├── 04_nlp_analisis_teks/         # BERTopic + Analisis Sentimen IndoBERT
│   ├── output/                   # Semua output notebook NLP
│   └── ringkasan_analisis_teks_nlp.md
└── 05_forecasting/               # ARIMA, Prophet, XGBoost per Kab/Kota
    ├── figures/
    ├── tables/
    ├── forecasting_pou_2026_2028.ipynb
    └── ringkasan_forecasting.md
```

---

## 🔍 Ringkasan Temuan Utama

### Target: Prevalensi Kerawanan Pangan (PoU, %)
- **Unit Analisis:** 21 Kabupaten/Kota Kepulauan Maluku
- **Rentang:** 2020–2025 (cross-section 2025 + panel 2021–2025)

### Temuan Kunci
1. **Faktor dominan universal:** PDRB per kapita rendah → PoU tinggi (SEM: coef=−3.93, p<0.001)
2. **Faktor tersembunyi:** Konektivitas kapal (X13b) signifikan di SEM (p=0.001) — tidak terlihat di OLS biasa
3. **Divide spasial:** Pengeluaran pangan (X9) lebih kuat di Halmahera Utara; wacana digital (X27) lebih kuat di Maluku Selatan
4. **7 Daerah Krisis Senyap:** PoU tinggi (30–37%) tapi hampir tidak ada wacana di media sosial
   - Kepulauan Sula · Buru Selatan · Pulau Taliabu · Halmahera Barat · Seram Bagian Timur · Kepulauan Aru · Pulau Morotai
5. **Tren 2020–2025:** Pengaruh PDRB melemah; efek kapal menurun (konektivitas membaik); wacana digital berbalik arah sejak 2024 (program MBG)

---

## 🛠️ Metode yang Digunakan

| # | Metode | Output Utama |
|---|--------|-------------|
| 1 | Autokorelasi Spasial (Moran's I, LISA, Gi*) | Klaster PoU signifikan mulai 2023 |
| 2 | Clustering Spasial (K-Means, SKATER) | Tipologi daerah rawan |
| 3 | Regresi Global (OLS, SAR, SEM) | Faktor signifikan: PDRB + Konektivitas Kapal |
| 4 | GWR Parsimoni (Lokal, 2025) | Pola utara-selatan dalam pengaruh PDRB/pangsa pangan |
| 5 | GTWR Panel (Lokal, 2021–2025) | Tren pergeseran faktor dominan temporal |
| 6 | NLP: BERTopic + Sentimen IndoBERT | X27 Wacana Digital, 7 Krisis Senyap |
| 7 | Forecasting (ARIMA, Prophet, XGBoost) | Proyeksi PoU 2026–2028 per Kab/Kota |

---

## 📊 Data

- **PoU per Kab/Kota:** BPS Provinsi Maluku & Maluku Utara
- **Sosio-ekonomi:** BPS (PDRB, Kemiskinan, TPT, Stunting, dll)
- **Konektivitas maritim:** Kemenhub + BPS Kabupaten Dalam Angka
- **Pangan:** Bapanas / Portal SiPangan
- **Teks media sosial:** TikTok, Twitter/X, YouTube (2.810 dokumen, 2021–2026)

---

*GEMASTIK XIX — UNESA 2026*
