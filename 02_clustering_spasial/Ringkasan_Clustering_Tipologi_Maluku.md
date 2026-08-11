# Ringkasan Eksperimen Clustering & Tipologi Spasial
## Ketahanan Pangan Kepulauan Maluku (2020–2025)
*Output dari Analisis Clustering (K-Means, K-Medoids, Ward, SKATER, SC-Hierarchical)*

---

## 1. Kerangka Analisis & Tujuan

- **Unit analisis:** 21 kabupaten/kota Kepulauan Maluku (Provinsi Maluku + Maluku Utara)
- **Variabel karakterisasi (Y):** PoU (Prevalensi Ketidakcukupan Konsumsi Pangan, %)
- **Prediktor awal:** 26 indikator → 20 setelah pembuangan indikator bermasalah → 13 setelah seleksi VIF
- **Desain kunci:** PoU **sengaja dikeluarkan** dari input clustering. Cluster dibentuk murni dari kondisi struktural socio-economic dan maritim, lalu PoU dipakai memvalidasi hasil secara independen. Ini mencegah argumen sirkular dan membuktikan validitas eksternal.
- **Strategi dua lapis:** Tipologi final berbasis cross-section rata-rata (observasi independen); pelacakan pergerakan berbasis clustering panel gabungan (N=126).

---

## 2. Kualitas Data dan Pembersihan

- **Rekonstruksi data:** Ditemukan 539 sel (16,5% tabel) rusak akibat penafsiran format spreadsheet (bukan data hilang). Dipulihkan secara deterministik (contoh: Gini Ratio 316 dipulihkan jadi 0,316).
- **Indikator terduplikasi level-provinsi:** Nilai Tukar Petani (NTP) dan % Tenaga Kerja Pertanian bernilai identik untuk seluruh kabupaten dalam satu provinsi. Dibuang dari input clustering, difungsikan sebagai variabel kontrol.
- **Ketersediaan indikator jalan mantap:** Dibuang dari input karena 64% data hilang (4 kabupaten kosong total). Aksesibilitas fisik tereduksi menjadi konektivitas maritim saja.
- **Patahan 2020:** Komposisi tipologi tahun 2020 menunjukkan anomali akibat dampak awal pandemi COVID-19, lalu relatif konsisten/stabil sejak 2021.

---

## 3. Rekayasa Fitur Utama

1. **Indeks Konektivitas Maritim (IKM):**
   $$\text{IKM}_i = w_1 \cdot \text{norm}(\text{pelabuhan}) + w_2 \cdot \text{norm}(\text{kunjungan kapal}) + w_3 \cdot \text{norm}\left(\frac{1}{\text{jarak hub}+1}\right)$$
   *Bobot dikalibrasi dari muatan komponen utama pertama (PCA).*
2. **Indeks Kapasitas Produksi Pangan (IKPP):** Pengganti Self-Sufficiency Ratio (SSR) komparatif antar 21 unit dari perikanan tangkap dan log-padi.
3. **Rasio Tekanan Pangan:** Operationalization Hukum Engel (pengeluaran pangan relatif terhadap garis kemiskinan).

---

## 4. Reduksi Dimensi & Penentuan Jumlah Cluster

- **Seleksi VIF:** Iteratif ambang 10 memotong 20 indikator menjadi 13 indikator final.
- **PCA Kriteria Kaiser (Eigenvalue > 1):** 5 komponen menjelaskan 78,6% total varians:
  - PC1 (27,7%): Sumbu keterhubungan & layanan (IKM, sanitasi, AHH)
  - PC2 (21,0%): Sumbu kesejahteraan vs biaya (kemiskinan, pengeluaran pangan, PDRB)
  - PC3 (11,2%): IKPP, Gini ratio, sinyal 4G
  - PC4 (10,4%): Garis kemiskinan, PDRB, stunting
  - PC5 (8,3%): TPAK
- **Penentuan jumlah cluster ($k$):** Ditetapkan $k=4$ berdasarkan kriteria Silhouette tertinggi ($0.315$) pada rentang aturan $k \in [3, 5]$.

---

## 5. Perbandingan 5 Algoritma Clustering

| Metode | Filosofi | Silhouette | Davies-Bouldin | Calinski-Harabasz | SCR | Jaccard |
|--------|----------|------------|----------------|-------------------|-----|---------|
| **K-Means** | Non-spasial | **0.315** | **0.962** | **8.841** | 0.491 | 0.802 |
| **K-Medoids (PAM)** | Non-spasial | 0.306 | 1.005 | 8.673 | 0.528 | 0.780 |
| **Hierarchical (Ward)** | Non-spasial | 0.315 | 0.962 | 8.841 | 0.491 | 0.814 |
| **SC-Hierarchical** | Berkendala spasial | 0.214 | 1.515 | 4.320 | **0.717** | – |
| **SKATER** | Berkendala spasial | 0.200 | 1.379 | 4.013 | 0.604 | – |

*Catatan: Kesepakatan antar-algoritma non-spasial sangat tinggi (Adjusted Rand Index ARI = 0.96).*

---

## 6. Empat Tipologi Ketahanan Pangan Kepulauan Maluku

| Cluster | Nama Tipologi | $n$ | PoU Rata-rata | IKM | Kemiskinan | IPM | PDRB/Kapita |
|---------|---------------|-----|---------------|-----|------------|-----|-------------|
| **C0** | **Rawan Konsumsi Tanpa Kemiskinan Ekstrem** | 6 | 33.7% | 0.10 | 6.5% | 66.0 | 36.7 jt |
| **C1** | **Kepulauan Miskin Terisolasi** | 11 | 32.2% | 0.09 | 19.9% | 69.5 | 27.6 jt |
| **C2** | **Sentra Urban Terhubung** | 2 | 21.6% | 0.66 | 4.9% | 82.0 | 47.2 jt |
| **C3** | **Enklave Ekonomi Terisolasi** | 2 | 26.0% | 0.03 | 9.6% | 73.3 | 91.5 jt |

---

## 7. Temuan Substantif Kunci

1. **Hambatan ada pada distribusi, bukan produksi:** Kapasitas produksi pangan tidak berkorelasi dengan PoU ($r = +0.14$). Sebaliknya, isolasi maritim ($r = +0.60$) dan beban pengeluaran pangan ($r = +0.70$) menjelaskan variasi kerawanan pangan.
2. **Paradoks Rawan Konsumsi (C0):** 6 kabupaten Maluku Utara (termasuk Kepulauan Sula & Halmahera Barat) memiliki PoU tinggi (33.7%) meskipun tingkat kemiskinan rendah (6.5%). Masalah utama adalah pasokan pangan yang tidak sampai, bukan ketidakmampuan membeli.
3. **Paradoks Enklave Industri (C3):** Halmahera Tengah dan Halmahera Timur memiliki PDRB 3.3× rata-rata akibat enklave tambang/smelter, namun PoU tetap tinggi (26.0%) dan akses air minum terendah.

---

## 8. Implikasi Kebijakan per Tipologi

- **C0 (Rawan Konsumsi):** Penguatan rantai pasok fisik, cadangan pangan daerah, dan stabilisasi harga logistik (bukan bantuan tunai).
- **C1 (Miskin Terisolasi):** Jalur ganda: penambahan trayek/frekuensi kapal perintis + penguatan ekonomi rumah tangga.
- **C2 (Sentra Urban):** Pertahankan fungsi hub regional dan optimalkan sistem redistribusi pangan ke pulau sekitar.
- **C3 (Enklave Industri):** Kewajiban integrasi pertumbuhan industri tambang dengan penyediaan infrastruktur dasar & layanan pangan masyarakat lokal.

---

## 9. Daftar Berkas Tabel dan Gambar (Available Files)

Seluruh berkas terkait eksperimen clustering tersimpan lengkap di folder `02_clustering_spasial/`:

### 📄 Berkas Utama & Notebook
1. `Ringkasan_Clustering_Tipologi_Maluku.pdf` — Dokumen laporan riset teknis lengkap tipologi clustering ketahanan pangan Maluku.
2. `ringkasan_clustering_spasial.md` / `Ringkasan_Clustering_Tipologi_Maluku.md` — Ringkasan eksekutif berbasis markdown untuk dokumentasi repository.
3. `Clustering_Ketahanan_Pangan_Maluku (FINAL).ipynb` — Jupyter Notebook sumber untuk seluruh pipeline pembersihan, PCA, K-Means, SKATER, dan visualisasi.

### 🖼️ Berkas Visualisasi Peta Utama (PNG)
1. `03_peta_pou.png` — Peta tematik distribusi Prevalensi Kerawanan Pangan (PoU) 21 kabupaten/kota Kepulauan Maluku.
2. `19_peta_tipologi.png` — Peta utama pembagian 4 tipologi ketahanan pangan hasil clustering final.
3. `20_peta_tipologi_per_tahun.png` — Peta perkembangan evolusi keanggotaan tipologi per tahun (2020–2025).

### 📊 Berkas Output Hasil Modeling (`output/` & Notebook Generated)
- `hasil_clustering.csv` — Keanggotaan final 21 wilayah + label 5 algoritma + nilai silhouette + penanda kuadran paradoks.
- `profil_cluster.csv` — Rata-rata nilai 13 indikator socio-economic untuk masing-masing dari 4 tipologi.
- `validasi_eksternal.csv` — Hasil pengujian validitas eksternal (ANOVA, Kruskal-Wallis, $\eta^2$) terhadap PoU dan indikator luar.
- `perbandingan_metode.csv` — Evaluasi metrik kuantitatif (Silhouette, DB, CH, SCR) untuk 5 algoritma clustering.
- `tipologi_panel_per_tahun.csv` — Matriks perubahan keanggotaan tipologi 21 kabupaten/kota selama 6 tahun (2020–2025).
- `cross_section_2021_2025.csv` — Dataset rata-rata multi-tahun siap pakai untuk clustering cross-section.
- `data_bersih_panel.csv` — Dataset panel 126 observasi setelah pembersihan data.
- `audit_pembersihan.csv` — Catatan pemulihan 539 sel data yang mengalami kesalahan format.
- `indikator_terpakai.csv` — Daftar 13 indikator socio-economic & maritim yang lolos seleksi VIF.

---

## 8. Implikasi Kebijakan per Tipologi

- **C0 (Rawan Konsumsi):** Penguatan rantai pasok fisik, cadangan pangan daerah, dan stabilisasi harga logistik (bukan bantuan tunai).
- **C1 (Miskin Terisolasi):** Jalur ganda: penambahan trayek/frekuensi kapal perintis + penguatan ekonomi rumah tangga.
- **C2 (Sentra Urban):** Pertahankan fungsi hub regional dan optimalkan sistem redistribusi pangan ke pulau sekitar.
- **C3 (Enklave Industri):** Kewajiban integrasi pertumbuhan industri tambang dengan penyediaan infrastruktur dasar & layanan pangan masyarakat lokal.

---

## 9. Wilayah Prioritas — Top 5 Kab/Kota dari Perspektif Clustering

Identifikasi berdasarkan: (a) keanggotaan tipologi, (b) nilai PoU rata-rata, dan (c) anomali/paradoks yang diidentifikasi dari profil multi-variabel.

| Rank | Kab/Kota | Kluster | PoU 2025 | Kapasitas Produksi | Peran & Relevansi Metode Lain |
|------|----------|---------|----------|--------------------|-------------------------------|
| 1 | **Kepulauan Sula** | C0 Paradoks | 37.36% | Sedang | Paradoks: PoU tertinggi tapi kemiskinan rendah (6.5%) → masalah distribusi logistik; LISA HH Hotspot; Forecasting #1 terburuk stabil |
| 2 | **Pulau Taliabu** | C0 Paradoks | 34.19% | Sedang | Produksi lokal ada, tapi pangan tidak sampai ke masyarakat; GTWR isolasi terberat; NLP hanya 20 dok (Krisis Senyap Super) |
| 3 | **Halmahera Tengah** | C3 Enklave | 19.03% | Tertinggi (PDRB 91.5 jt) | Paradoks enklave tambang: PDRB 3.3× rata-rata, PoU malah mulai naik di 2028; air minum terendah |
| 4 | **Seram Bagian Barat** | C0/C1 | 32.94% | Tertinggi ke-3 (0.742) | Paradoks produksi-distribusi: kapasitas produksi besar, PoU tetap tinggi & memburuk 2025→2028 |
| 5 | **Maluku Tengah** | C1 Terisolasi | 30.26% | Tertinggi (1.00, dinorm.) | Kapasitas produksi tertinggi tetapi konektivitas kapal lemah; Forecasting memproyeksikan lonjakan +7 peringkat pada 2028 |

**Catatan interpretasi:**
- Kluster C0 menghasilkan dua paradoks terkuat: wilayah dengan kemiskinan *rendah* tetapi PoU *tinggi*, membuktikan bahwa hambatan ketahanan pangan Maluku Utara bukan soal kemampuan beli melainkan soal **pasokan fisik yang tidak sampai**.
- Kluster C3 (Halmahera Tengah & Halmahera Timur) adalah paradoks enklave industri: pertumbuhan ekonomi tidak menetes ke pangan dan air minum masyarakat lokal.
- Kluster C2 (Kota Ternate & Kota Tidore Kepulauan) adalah *benchmark* yang harus dipertahankan dan dijadikan hub redistribusi pangan ke pulau-pulau sekitar.

---
*Dihasilkan dari analisis clustering oleh tim GEMASTIK XIX / 2026*
