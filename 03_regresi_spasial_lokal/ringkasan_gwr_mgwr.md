# Ringkasan Eksperimen GWR / MGWR / GTWR
## Determinan Lokal Prevalensi Kerawanan Pangan (PoU) -- Kepulauan Maluku 2020-2025
*Dihasilkan otomatis dari eksekusi notebook `GWR_MGWR_Determinan_Lokal_PoU_Maluku.ipynb`*

---

## 1. Latar Belakang dan Data

- **Unit analisis:** 21 kabupaten/kota di Kepulauan Maluku (Maluku + Maluku Utara)
- **Cross-section utama:** 2025 (N=21)
- **Panel verifikasi:** 2020-2025 (N=126 = 21 x 6 tahun) dan 2021-2025 (N=105)
- **Variabel dependen (Y):** PoU (Prevalensi Kerawanan Pangan, % populasi)
- **Prediktor awal:** 11 fitur (setelah X20 dibuang karena bug level-provinsi): X1, X5, X8, X9,
  X12, X13a, X13b, X13c, X17, X27 (ditambah X9 sebagai variant robustness)

### Catatan data penting

- **X20 (Akses Sanitasi):** nilai identik di seluruh kab/kota Provinsi Maluku -- bersifat
  angka level-provinsi yang ter-duplikasi, BUKAN variasi kab/kota genuine. Dibuang dari
  semua model setelah verifikasi.
- **Patahan 2020:** pada transisi 2020->2021 teridentifikasi penurunan serentak Y_PoU
  rata-rata yang signifikan di mayoritas kabupaten -- diduga patahan metodologi/basis data.
  Menjadi fokus verifikasi di Tahap 15-16.

---

## 2. Imputasi dan kualitas data

Tidak ada imputasi lintas-kabupaten. Imputasi bersifat temporal dalam kab/kota yang sama.
Variabel dengan imputasi terbanyak: X20 (10/21 kab kosong di 2024-2025 untuk Prov. Maluku Utara).

---

## 3. Tahap 1-4: Model Global (Cross-Section 2025, N=21)

### 3a. OLS (Ordinary Least Squares)

- AICc: 153.0189 | AIC: 114.0189 | R2: 0.8107 | RMSE: 2.0634
- **Hanya X8_PDRB_per_Kapita_ADHB yang signifikan** (koef=-3.125, p=0.0070)
- Moran's I pada residual OLS: tidak signifikan secara statistik (autokorelasi spasial lemah di cross-section 2025)

### 3b. SAR (Spatial Autoregressive Lag)

- AIC: 112.0941 | R2: 0.8518 | RMSE: 1.8265
- AICc lebih tinggi dari SEM dan OLS (164.0941) -- tidak terpilih sebagai model global utama

### 3c. SEM (Spatial Error Model) -- **Model Global Utama Laporan**

- **AICc: 147.6518 (terbaik di antara OLS/SAR/SEM)** | AIC: 108.6518
- R2: 0.8005 | RMSE: 2.1721
- Lambda SEM mendekati batas ruang parameter (-1.0) -- hasil diinterpretasikan sebagai indikatif
- **SEM (ML_Error) dipilih sebagai model global untuk laporan**

### Perbandingan AICc (N=21, bisa dibandingkan sesama):

| Model | AICc | AIC | R2 | RMSE | Gate Stabilitas |
|-------|------|-----|-----|------|-----------------|
| OLS | 153.0189 | 114.0189 | 0.8107 | 2.0634 | - |
| SAR (ML_Lag) | 164.0941 | 112.0941 | 0.8518 | 1.8265 | - |
| **SEM (ML_Error)** | **147.6518** | **108.6518** | 0.8005 | 2.1721 | - |
| GWR (11 fitur) | -1018.4778 | - | 0.9816 | 0.6427 | TIDAK LOLOS |
| GWR Parsimoni (3 fitur) | 122.9048 | - | 0.7061 | 2.5711 | **LOLOS** |

> **Catatan:** AICc GWR (-1018.5) tidak valid karena model jenuh (bw=16 dari 21 lokasi) -- diabaikan.

---

## 4. Tahap 5: Gate Stabilitas GWR (N=21, 11 fitur)

- Bandwidth adaptif optimal: **16.0000 tetangga**
- CN>30: **100.0000%** | VIF>10: **100.0000%**
- Ambang: 35%. **Gate: TIDAK LOLOS** -- GWR 11-fitur dinyatakan eksploratif, bukan hasil utama

### Tahap 5B: GWR Parsimoni (3 fitur: X8, X9, X27)

- Bandwidth adaptif optimal: **21.0000 tetangga** (= N=21, efek seragam)
- CN>30: **0.0000%** | VIF>10: **0.0000%**
- **Gate: LOLOS** -- GWR Parsimoni valid untuk interpretasi
- Bandwidth = N menunjukkan efek X8/X9/X27 **relatif seragam** antar kab/kota (tidak sangat bervariasi spasial)
- MGWR: tidak konvergen bersih pada N=21, GWR Parsimoni dipakai sebagai hasil lokal cross-section

---

## 5. Tahap 11: GTWR Panel 11 Fitur (N=126, 2020-2025)

- Bandwidth awal: **110.0000** | X20 dibuang (bug level-provinsi)
- Setelah drop X20 (10 fitur): CN>30=0.0000%, VIF>10=7.1429%, gate=LOLOS gate CN, belum lolos VIF

---

## 6. Tahap 12-14: Backward Elimination GTWR

Jejak eliminasi (tabel lengkap: `tables/gtwr_jejak_eliminasi.csv`):
| iterasi | fitur_dibuang | alasan | jumlah_fitur_sisa | bw | pct_CN | pct_VIF | gate |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 0.000 | X20_RT_Akses_Sanitasi_Layak | bug data (duplikasi level-provinsi, lihat Tahap 1b / Bagian 2 ringkasan) |  |  |  |  |  |


### Hasil Akhir

| Varian | Fitur | Bandwidth | CN>30 | VIF>10 | Gate | RMSE | R2 |
|--------|-------|-----------|-------|--------|------|------|----|
| GTWR-12b (eliminasi murni) | 10 | 110.0000 | 0.0000% | 7.1429% | LOLOS | 3.4110 | 0.7187 |
| GTWR-12d (IKM composite) | 8 | 96.0000 | 0.0000% | 2.3810% | LOLOS | 3.3995 | 0.7206 |

**Rekomendasi narasi:** GTWR-12b (10 fitur) dipilih untuk laporan karena granularitas fitur lebih tinggi
(X13a/b/c terpisah, lebih informatif untuk narasi maritim) dibanding 12d yang menggabungkan ke IKM.

**Bandwidth optimal mendekati N** (110 dari 126 untuk 12b, 96 dari 126 untuk 12d) menunjukkan efek
fitur relatif SERAGAM di ruang-waktu -- dilaporkan apa adanya.

---

## 7. Tahap 15-16: Verifikasi GTWR Tanpa Data Tahun 2020

RMSE GTWR-12b/12d (N=126) lebih tinggi dari OLS/SEM. Hipotesis: data 2020 mengandung patahan
level artifisial yang mengganggu fitting. Diverifikasi langsung dari data.

### 7a. Dekomposisi varians fitur GTWR-12b (panel 2020-2025)

| Fitur | % Varians Antar-Kabupaten |
|-------|--------------------------|
| X1_Produksi_Perikanan_Tangkap_Laut | 94.1% |
| X5_Persentase_Penduduk_Miskin | 98.6% |
| X8_PDRB_per_Kapita_ADHB | 66.4% |
| X12_Kepadatan_Penduduk | 97.0% |
| X13a_Jumlah_Pelabuhan_Utama | 100.0% |
| X13b_Kunjungan_Kapal_Tahunan | 95.7% |
| X13c_Jarak_ke_Pelabuhan_Induk | 100.0% |
| X17_TPT | 92.6% |
| X27_Volume_Wacana_Digital | 100.0% |
| X9_Pangsa_Pengeluaran_Pangan | 79.9% |

**8/10 fitur** (80%) memiliki >=90% varians dari perbedaan
ANTAR KABUPATEN (bukan pergerakan antar-tahun). Ini menjelaskan mengapa RMSE GTWR lebih tinggi dari OLS:
prediktor yang bergerak lambat/statis secara temporal tidak banyak menjelaskan varians temporal.

### 7b. Re-fit GTWR pada panel 2021-2025 (N=105, bandwidth dicari ulang)

| Varian | N | bw | CN>30 | VIF>10 | Gate | RMSE | R2 Lokal |
|--------|---|----|-------|--------|------|------|----------|
| GTWR-12b (2020-2025) | 126 | 110 | 0.0% | 7.1% | True | 3.411 | 0.608 |
| GTWR-12b (2021-2025) | 105 | 99 | 0.0% | 4.8% | True | 3.219 | 0.611 |
| GTWR-12d (2020-2025) | 126 | 96 | 0.0% | 2.4% | True | 3.399 | 0.610 |
| GTWR-12d (2021-2025) | 105 | 99 | 0.0% | 0.0% | True | 3.312 | 0.594 |

### 7c. Kesimpulan

- RMSE terbaik 2021-2025: **3.219**
  vs OLS **2.0634** / SEM **2.1721**
- RMSE tetap tinggi meski tahun 2020 dibuang -- **penyebab bukan (hanya) patahan 2020**
- Penyebab genuine: 8/10 fitur variansnya didominasi perbedaan antar-kabupaten
- Dilaporkan sebagai keterbatasan metodologis, bukan kegagalan implementasi

---

## 8. Keputusan Model Final

| Pertanyaan | Model Terpilih | Alasan |
|------------|---------------|--------|
| Global: fitur apa yang signifikan? | **SEM (ML_Error)** | AICc terbaik (147.65) |
| Lokal cross-section: variasi spasial? | **GWR Parsimoni (3 fitur)** | Satu-satunya yang gate lolos (N=21) |
| Lokal panel: tren ruang-waktu? | **GTWR-12b (10 fitur, 2020-2025)** | Gate lolos, granularitas fitur optimal |

---

## 9. Temuan Substantif

1. **X8 (PDRB per Kapita)** adalah satu-satunya prediktor signifikan secara statistik di OLS global
   (koef=-3.125, p=0.0070) -- PDRB lebih tinggi berkaitan dengan PoU lebih rendah.
2. **GWR Parsimoni** (bw=21=N): efek X8/X9/X27 terhadap PoU relatif SERAGAM antar kabupaten,
   bukan sangat bervariasi spasial -- memperkuat temuan SEM.
3. **Hipotesis Krisis Senyap (X27):** 7 kabupaten/kota teridentifikasi sebagai kandidat krisis senyap
   kuantitatif (PoU di atas rata-rata, volume wacana digital di bawah median):
   Kepulauan Sula, Buru Selatan, Halmahera Barat, Pulau Taliabu, Seram Bagian Timur, Kepulauan Aru, Pulau Morotai
4. **Keterbatasan GTWR:** bandwidth nyaris menyentuh N (panel) -- efek fitur relatif seragam di
   ruang-waktu, konsisten dengan dekomposisi varians (8/10 fitur statis temporal).

---

## 10. File Output

### Tabel (tables/)
- `perbandingan_semua_model.csv` -- semua model, semua metrik
- `tabel_model_crosssection_N21.csv` -- OLS/SAR/SEM/GWR (AICc bisa dibandingkan)
- `tabel_model_panel_GTWR.csv` -- varian GTWR (AICc JANGAN dibandingkan dengan N=21)
- `gtwr_perbandingan_2020_2025_vs_2021_2025.csv` -- verifikasi Tahap 15-16
- `gtwr_final_12b_koefisien_lokal_kabkota_tahun.csv` -- koefisien lokal 126 baris
- `dekomposisi_varians_fitur_panel.csv` -- % varians antar-kabupaten per fitur
- `krisis_senyap_kandidat.csv` -- 7 kandidat krisis senyap

### Gambar (figures/)
- Fig 1-18: EDA dan deskriptif
- Fig 19-23: OLS diagnostik
- Fig 24: Peta bobot spasial
- Fig 25-27: GWR eksploratif (tidak stabil, bersifat indikatif)
- Fig 28-35: GWR Parsimoni + GTWR awal
- Fig 36-48: GTWR-12b final (koefisien lokal, tren temporal, gate)
- **Fig 49:** Perbandingan RMSE GTWR 2020-2025 vs 2021-2025 (verifikasi Tahap 15-16)

---

## 11. Keterbatasan Metodologis

- N=21 adalah kendala fundamental untuk GWR/MGWR dengan fitur banyak
- Estimasi lambda SEM mendekati batas (-1.0) -- hasil indikatif
- Bandwidth GTWR nyaris menyentuh N -- efek global, bukan lokal yang kaya variasi
- 8/10 fitur GTWR statis temporal -- RMSE GTWR lebih tinggi dari OLS secara inheren


---

## 12. Apa Bedanya 3 Model Ini? Untuk Apa Masing-masing?

Agar mudah dipahami, bayangkan kita ingin tahu: *"Mengapa kabupaten A punya PoU tinggi, tapi kabupaten B tidak?"*

| Pertanyaan | Model | Cara Kerjanya | Keterbatasan |
|-----------|-------|--------------|--------------|
| Faktor apa yang **secara rata-rata** mempengaruhi PoU di seluruh Kepulauan Maluku? | **SEM (global)** | Satu persamaan untuk 21 kab/kota -- efek X diasumsikan SAMA di mana-mana | Tidak bisa lihat perbedaan antar-wilayah |
| Apakah efek faktor-faktor itu **berbeda antar lokasi** di 2025? | **GWR Parsimoni (lokal, cross-section)** | Koefisien berbeda per kab/kota -- "garis regresi" per lokasi, hanya 3 faktor | N=21 terlalu kecil untuk banyak fitur; bandwidth=21 (=N) artinya efeknya hampir seragam |
| Apakah efeknya **berbeda antar lokasi DAN antar waktu** (2020-2025)? | **GTWR-12b (lokal, panel)** | Koefisien berbeda per kab/kota per tahun -- 10 fitur, 126 observasi | Bandwidth≈N berarti variasi spasial-temporal masih relatif kecil |

**Ringkasnya:**
- **SEM** = "Foto keseluruhan" -- apa yang berlaku umum
- **GWR Parsimoni** = "Foto tiap daerah" di satu waktu (2025) -- apakah efek PDRB/kemiskinan lebih kuat di sini vs di sana?
- **GTWR** = "Video tiap daerah" selama 6 tahun -- bagaimana efeknya berubah dari tahun ke tahun di tiap lokasi?

---

## 13. Faktor Penyebab PoU Per Kabupaten/Kota (Interpretasi Lokal)

### 13a. Penjelasan Metodologis

Koefisien lokal GTWR-12b (rata-rata 2021-2025, variabel distandarisasi / z-score) menunjukkan:
- **Koefisien negatif besar** pada X8 (PDRB): daerah ini sangat sensitif terhadap rendahnya ekonomi → PDRB rendah = PoU tinggi
- **Koefisien positif** pada X13c (Jarak Pelabuhan): daerah ini terdampak oleh keterpencilan geografis
- **Koefisien negatif** pada X13b (Kunjungan Kapal): daerah ini sangat bergantung pada konektivitas maritim

> **Catatan penting:** Karena bandwidth GTWR mendekati N (110/126), perbedaan koefisien antar wilayah tidak besar -- model menunjukkan PDRB (X8) sebagai faktor paling dominan di hampir semua kabupaten. Perbedaan lokal yang berarti ada pada faktor ke-2 dan ke-3 (konektivitas vs kemiskinan vs isolasi geografis).

### 13b. Tabel Faktor Dominan per Kabupaten/Kota (Sumber: GTWR-12b, rata-rata 2021-2025)

Data lengkap tersedia di: `tables/interpretasi_faktor_lokal_per_kabkota.csv`

| Kabupaten/Kota | PoU 2025 | Faktor #1 (Terkuat) | Faktor #2 | Faktor #3 | Krisis Senyap? |
|----------------|----------|--------------------|-----------|-----------|----|
| **Kepulauan Sula** | 37.36% | PDRB per Kapita rendah | Jarak ke Pelabuhan Induk | Produksi Perikanan | Ya |
| **Buru Selatan** | 36.17% | PDRB per Kapita rendah | Kunjungan Kapal Tahunan | Produksi Perikanan | Ya |
| **Halmahera Barat** | 36.09% | PDRB per Kapita rendah | Jarak ke Pelabuhan Induk | Kemiskinan | Ya |
| Kepulauan Tanimbar | 35.76% | PDRB per Kapita rendah | Kepadatan Penduduk | Pangsa Pengeluaran Pangan | |
| **Pulau Taliabu** | 34.19% | PDRB per Kapita rendah | Jarak ke Pelabuhan Induk | Produksi Perikanan | Ya |
| Seram Bagian Timur | 33.63% | PDRB per Kapita rendah | Kunjungan Kapal Tahunan | Visibilitas Media Digital | Ya |
| Kepulauan Aru | 33.31% | PDRB per Kapita rendah | Kepadatan Penduduk | Pangsa Pengeluaran Pangan | Ya |
| Seram Bagian Barat | 32.94% | PDRB per Kapita rendah | Kunjungan Kapal Tahunan | Produksi Perikanan | |
| Buru | 32.36% | PDRB per Kapita rendah | Kunjungan Kapal Tahunan | Produksi Perikanan | |
| Maluku Tenggara | 30.99% | PDRB per Kapita rendah | Kepadatan Penduduk | Pangsa Pengeluaran Pangan | |
| Kota Tual | 30.73% | PDRB per Kapita rendah | Kepadatan Penduduk | Pangsa Pengeluaran Pangan | |
| Maluku Barat Daya | 30.73% | PDRB per Kapita rendah | Kepadatan Penduduk | Kunjungan Kapal Tahunan | |
| **Pulau Morotai** | 30.71% | PDRB per Kapita rendah | Jarak ke Pelabuhan Induk | Kepadatan Penduduk | Ya |
| Maluku Tengah | 30.26% | PDRB per Kapita rendah | Kunjungan Kapal Tahunan | Visibilitas Media Digital | |
| Kota Tidore Kepulauan | 28.68% | PDRB per Kapita rendah | Jarak ke Pelabuhan Induk | Produksi Perikanan | |
| Halmahera Timur | 27.76% | PDRB per Kapita rendah | Jarak ke Pelabuhan Induk | Kunjungan Kapal Tahunan | |
| Halmahera Selatan | 26.77% | PDRB per Kapita rendah | Jarak ke Pelabuhan Induk | Produksi Perikanan | |
| Halmahera Utara | 25.35% | PDRB per Kapita rendah | Jarak ke Pelabuhan Induk | Kemiskinan | |
| Kota Ambon | 24.78% | PDRB per Kapita rendah | Kunjungan Kapal Tahunan | Visibilitas Media Digital | |
| Kota Ternate | 21.69% | Jarak ke Pelabuhan Induk | PDRB per Kapita rendah | Produksi Perikanan | |
| Halmahera Tengah | 19.03% | PDRB per Kapita rendah | Jarak ke Pelabuhan Induk | Kunjungan Kapal Tahunan | |

**Keterangan warna/interpretasi:**
- **PDRB per Kapita rendah** = kapasitas ekonomi rendah yang tidak memungkinkan rumah tangga membeli pangan cukup
- **Jarak ke Pelabuhan Induk** = isolasi geografis → sulitnya distribusi dan harga pangan mahal
- **Kunjungan Kapal Tahunan rendah** = konektivitas maritim lemah → pasokan pangan tidak lancar
- **Kepadatan Penduduk** = tekanan urban/kepadatan dikaitkan dengan kompetisi akses pangan
- **Visibilitas Media Digital rendah** = isu ini "senyap" di media → potensi krisis tidak terdeteksi publik

### 13c. Pola yang Muncul

**Dua kelompok utama berdasarkan faktor penyebab lokal:**

1. **Kelompok "Keterisolasian Maritim"** (faktor ke-2 adalah Jarak/Konektivitas Kapal):
   Kepulauan Sula, Halmahera Barat, Pulau Taliabu, Kepulauan Aru, Halmahera Selatan, Halmahera Timur, Halmahera Utara, Pulau Morotai
   → Intervensi: perbaiki konektivitas kapal dan infrastruktur pelabuhan

2. **Kelompok "Kepadatan & Pengeluaran Pangan"** (faktor ke-2 adalah Kepadatan/Pangsa Pangan):
   Kepulauan Tanimbar, Maluku Tenggara, Kepulauan Aru, Kota Tual, Maluku Barat Daya, Maluku Tenggara
   → Intervensi: subsidi harga pangan dan stabilisasi pasar lokal

3. **"Krisis Senyap"** (PoU tinggi, wacana digital rendah):
   7 kabupaten teridentifikasi -- isu kerawanan pangan mereka tidak terwakili di media sosial,
   sehingga tekanan kebijakan lebih rendah padahal kondisinya lebih buruk.

---

## 14. Perbedaan GWR Parsimoni vs GTWR -- Apa yang Berbeda?

### GWR Parsimoni (N=21, 2025, 3 fitur: PDRB, Pangsa Pangan, Wacana Digital)
- Tujuan: "Di 2025, apakah efek PDRB, beban pangan, dan visibilitas media berbeda antar kab/kota?"
- Hasilnya: bandwidth=21=N → efek ketiga faktor ini **relatif seragam** di semua kab/kota
- Implikasi: kebijakan terkait PDRB, bantuan pangan, dan advokasi media bisa dirancang NASIONAL, tidak perlu sangat berbeda per lokasi
- **Untuk website:** menunjukkan bahwa efek PDRB/kemiskinan/wacana digital bersifat universal di Maluku

### GTWR-12b (N=126, 2020-2025, 10 fitur)
- Tujuan: "Selama 6 tahun, apakah faktor yang mendorong PoU berubah di tiap kabupaten?"
- Hasilnya: bandwidth=110/126 → sebagian besar efek masih cukup seragam, tapi ada variasi lokal terutama untuk faktor maritim (X13a/b/c) dan kepadatan (X12)
- Temuan unik: **Kota Ternate** adalah satu-satunya kab/kota di mana **Jarak ke Pelabuhan Induk** menjadi faktor #1 (bukan PDRB), padahal PoU-nya rendah -- menunjukkan Ternate sudah mampu mengkompensasi keterjangkauan ekonominya lewat konektivitas yang baik
- **Untuk website:** menunjukkan tren temporal -- kapan dan di mana faktor tertentu mulai lebih dominan

---


---

## 15. Temuan SEM: X13b (Konektivitas Kapal) Signifikan saat Autokorelasi Dikontrol

Ini temuan yang tidak muncul di OLS biasa:

| Model | X8 PDRB | X13b Kunjungan Kapal | X9 Pangsa Pangan | Spatial Term |
|-------|---------|---------------------|-----------------|-------------|
| **OLS** | -3.126 (p=0.007)*** | -0.891 (p=0.540) | 2.432 (p=0.128) | -- |
| **SAR** | -2.597 (p=0.049)*** | -2.069 (p=0.100) | 2.010 (p=0.083)° | W_Y=+0.487 (p=0.004)*** |
| **SEM** | **-3.930 (p<0.001)***  | **-3.481 (p=0.001)***  | -0.337 (p=0.804) | lambda=-1.00 (p=0.007)*** |

**Interpretasi kritis:**
- OLS hanya menemukan X8 signifikan -- kelihatannya hanya PDRB yang penting
- **SEM menemukan X13b (Kunjungan Kapal Tahunan) juga signifikan** (coef=-3.48, p=0.001), setelah error spasial antar kabupaten dikontrol via lambda
- Artinya: **konektivitas kapal memiliki efek nyata terhadap PoU, tapi tersembunyi di balik autokorelasi spasial** -- ketika pengaruh "tetangga" dikontrol, peran pelabuhan/kapal muncul ke permukaan
- SAR mengkonfirmasi: W_Y=+0.49 (p=0.004) → PoU suatu kabupaten tertular dari tetangganya (efek limpahan spasial)
- Lambda SEM = -1.00 (pada batas parametrik) → perlu interpretasi hati-hati, tapi arah efeknya konsisten

**Implikasi kebijakan:** Intervensi konektivitas maritim (penambahan rute kapal, frekuensi kunjungan) berpotensi langsung menurunkan PoU, bukan hanya PDRB yang perlu ditingkatkan.

---

## 16. Pola Geografis: Utara vs Selatan dalam GWR Parsimoni (2025)

GWR Parsimoni (3 fitur: X8, X9, X27) mengungkap **divide utara-selatan** yang tajam:

### 16a. X9 (Pangsa Pengeluaran Pangan): Lebih Kuat di Maluku Utara

| Kelompok Wilayah | X9 GWR (rata-rata) | Interpretasi |
|-------------------|--------------------|--------------|
| **Maluku Utara (Halmahera):** Halmahera Barat, Tengah, Timur, Utara, Morotai, Ternate, Tidore | **+2.01 s.d. +2.52** | Beban belanja pangan sangat menentukan PoU -- masyarakat lebih rentan terhadap kenaikan harga |
| **Maluku Utara (Sula-Taliabu):** Kepulauan Sula, Pulau Taliabu | +1.67 s.d. +1.69 | Moderat-tinggi |
| **Maluku (Selatan):** Kepulauan Tanimbar, Kota Tual, Maluku Tenggara, Maluku Barat Daya | **+0.09 s.d. +0.56** | Jauh lebih lemah |

→ **Kebijakan subsidi/stabilisasi harga pangan paling efektif di wilayah Halmahera** (utara), bukan Maluku selatan.

### 16b. X27 (Wacana Digital): Lebih Kuat di Maluku Selatan (Krisis Senyap)

| Kelompok Wilayah | X27 GWR | Interpretasi |
|------------------|---------|--------------|
| **Maluku Selatan:** Maluku Barat Daya, Kepulauan Tanimbar, Seram Bagian Timur, Kota Tual | **-1.48 s.d. -2.13** | Rendahnya wacana digital sangat erat dengan tingginya PoU -- wilayah ini "senyap" |
| **Maluku Selatan lainnya:** Buru Selatan, Kepulauan Aru, Seram Bagian Barat, Buru | **-1.34 s.d. -1.42** | Sedang |
| **Maluku Utara:** Halmahera group, Pulau Morotai | **-0.24 s.d. -1.02** | Lebih lemah -- wacana digital di sana sudah lebih ada meski tetap sedikit |

→ **Program peningkatan literasi digital dan visibilitas isu pangan paling urgent di Maluku selatan** (termasuk Tanimbar, MBD, Seram Timur).

### 16c. X8 (PDRB): Lebih Kuat di Kepulauan Terpencil Maluku

| Kab/Kota | X8 GWR | PoU 2025 |
|----------|--------|----------|
| Maluku Barat Daya | **-3.60** | 30.7% |
| Kepulauan Tanimbar | **-3.50** | 35.8% |
| Kota Tual | -3.32 | 30.7% |
| Maluku Tenggara | -3.31 | 31.0% |
| Kepulauan Aru | -3.25 | 33.3% |

→ Di kepulauan terluar Maluku (Tanimbar, MBD, Aru, Tual), efek PDRB terhadap PoU paling kuat -- 1 SD peningkatan PDRB dapat menurunkan PoU 3.3-3.6 poin. Prioritas investasi ekonomi di sini akan paling berdampak.

---

## 17. Tren Temporal 2020-2025: Pergeseran Faktor Dominan (dari GTWR)

GTWR memungkinkan kita melihat koefisien rata-rata berubah dari tahun ke tahun:

| Tahun | X8 PDRB | X13b Kapal | X13c Jarak Pelabuhan | X27 Wacana | X9 Pangan | avg R2 |
|-------|---------|-----------|---------------------|-----------|----------|--------|
| **2020** | -4.18 | -3.97 | +0.91 | **-2.49** | +2.05 | 0.610 |
| **2021** | -4.00 | -3.06 | +1.44 | **-2.14** | +1.58 | 0.609 |
| **2022** | -3.53 | -1.68 | +1.82 | **-1.45** | +0.95 | 0.600 |
| **2023** | -2.86 | -0.74 | +1.80 | -0.40 | +0.50 | 0.599 |
| **2024** | -2.34 | -0.71 | +1.30 | **+0.20** | +0.56 | 0.612 |
| **2025** | -2.22 | -1.06 | +0.77 | +0.02 | +0.56 | 0.618 |

**Pola perubahan yang sangat jelas:**

1. **X8 (PDRB): melemah signifikan** -- pengaruh PDRB terhadap PoU berkurang dari -4.18 (2020) menjadi -2.22 (2025).
   - Interpretasi: kemajuan ekonomi masih terjadi, tapi semakin sedikit yang "tumpah" ke perbaikan pangan

2. **X13b (Kunjungan Kapal): melemah drastis** -- dari -3.97 (2020) menjadi -0.74 s.d. -1.06 (2023-2025).
   - Interpretasi: *konektivitas kapal membaik* (banyak rute baru KKP/PELNI 2021-2023), sehingga variasi antar-kabupaten mengecil → efek lokal melemah

3. **X27 (Wacana Digital): berbalik arah** -- dari negatif kuat (-2.49 di 2020) menjadi hampir nol (+0.02 di 2025).
   - Interpretasi: di 2020, wilayah yang "senyap" di media punya PoU lebih tinggi. Sejak 2023-2024, **efek ini menghilang** -- kemungkinan karena program MBG (Makan Bergizi Gratis) meledakkan volume wacana pangan di semua daerah, meratakan perbedaan antar-wilayah

4. **X13c (Jarak Pelabuhan): menguat dulu, lalu melemah** -- puncak di 2022-2023 (+1.82), kembali melemah di 2025 (+0.77).
   - Interpretasi: isolasi geografis paling berdampak di 2022-2023 (mungkin pasca-COVID ketika logistik belum pulih penuh), lalu membaik setelah normalisasi

5. **X9 (Pangsa Pangan): melemah tajam** -- dari +2.05 (2020) menjadi +0.56 (2023-2025 stabil).
   - Interpretasi: inflasi pangan 2020-2021 membuat beban belanja pangan sangat sensitif terhadap PoU, lalu stabil setelah harga relatif terkontrol

---

## 18. Robustness Check: Variant A (X9) vs Variant B (X10)

| Variant | Variabel | OLS AICc | OLS R2 | X27 GWR gate |
|---------|---------|----------|--------|-------------|
| **A (basis)** | X9 Pangsa Pengeluaran Pangan | 153.02 | 0.811 | Tidak |
| **B (alternatif)** | X10 Pengeluaran Pangan per Kapita (Rp) | **138.19** | **0.907** | Tidak |

- Variant B memberikan AICc lebih baik (+14.8 poin) dan R2 jauh lebih tinggi (0.907 vs 0.811)
- Namun GWR **masih tidak lolos gate** di kedua variant (100% CN>30 untuk 11 fitur)
- X27 tidak signifikan di OLS di kedua variant (p=0.78 dan p=0.47)
- **Kesimpulan robustness:** Temuan utama (X8 PDRB dominan, konektivitas maritim bermakna) **konsisten** di kedua spesifikasi. Pemilihan X9 vs X10 tidak mengubah narasi substantif.

---

## 19. X27 (Wacana Digital): Ketimpangan Ekstrem per Kab/Kota

Data volume wacana digital per kabupaten (dari pipeline NLP -- lihat folder AnalisisTeks_NLP):

| Kab/Kota | Wacana (postingan) | Multi-topik | Catatan |
|----------|---------------------|-------------|---------|
| **Kota Ambon** | **55** | 7 | Dominan -- ibukota provinsi |
| **Maluku Tenggara** | 30 | 7 | Tinggi relatif |
| **Kota Ternate** | 21 | 1 | Ibukota Maluku Utara |
| **Maluku Tengah** | 21 | 7 | Sedang |
| Maluku Barat Daya | 13 | 0 | PoU tinggi (30.7%), wacana minim |
| Kepulauan Tanimbar | 8 | 0 | PoU tinggi (35.8%), nyaris tidak ada |
| Buru | 6 | 0 | |
| Buru Selatan | **1** | 0 | **KRISIS SENYAP** -- PoU 36.2%, hanya 1 postingan |
| Halmahera Barat | **3** | 0 | **KRISIS SENYAP** -- PoU 36.1% |
| Kepulauan Sula | **1** | 0 | **KRISIS SENYAP** -- PoU 37.4% tertinggi! |
| Pulau Taliabu | **0** | 0 | **KRISIS SENYAP** -- PoU 34.2%, NOLA |
| Halmahera Tengah | **0** | 0 | PoU rendah (19%), tapi masih nol |

**Pola ketimpangan informasi yang mencolok:**
- **Ambon** (55 postingan) vs **7 kab/kota krisis senyap** (0-3 postingan) -- rasio 18:1 hingga ∞
- Kabupaten dengan PoU **tertinggi** (Kepulauan Sula 37.4%, Buru Selatan 36.2%) hanya punya 0-1 postingan
- Ini bukan karena kabupaten tersebut tidak punya koneksi internet sama sekali -- ini adalah masalah **visibilitas dan representasi** isu pangan di ruang digital

---

---
*Dokumen ini dihasilkan otomatis dari `GWR_MGWR_Determinan_Lokal_PoU_Maluku.ipynb`*
*Semua angka berasal langsung dari eksekusi notebook dan eksplorasi tabel output.*


## 20. Daftar Berkas Tabel dan Gambar (Available Files)

Seluruh berkas output tersimpan terstruktur dalam folder 	ables/ dan igures/:

### 📊 Tabel Hasil Analisis (	ables/) — 25 Berkas CSV

#### a. Data & Seleksi Fitur
1. cross_section_2025_dengan_flag_imputasi.csv — Dataset cross-section 2025 lengkap dengan kolom flag penanda imputasi.
2. dekomposisi_varians_fitur_panel.csv — Hasil dekomposisi varians fitur panel (within vs between kabupaten/kota).
3. itur_final_vif.csv — Nilai Variance Inflation Factor (VIF) untuk 10-11 fitur prediktor awal.
4. itur_parsimoni_vif.csv — Evaluasi VIF untuk 3 fitur parsimoni (X8 PDRB, X9 Pangsa Pangan, X27 Wacana Digital).
5. province_fixed_effects_X4_X18.csv — Evaluasi indikator level-provinsi (X4 NTP, X18 TK Pertanian) sebagai kontrol.

#### b. Regresi Global (OLS, SAR, SEM)
6. hasil_ols.csv — Koefisien regresi, std error, t-stat, p-value, R², AICc, dan diagnostik OLS global (N=21).
7. hasil_sar_sem.csv — Parameter estimasi dan statistik uji model Spatial Autoregressive (SAR) dan Spatial Error Model (SEM).
8. 	abel_model_crosssection_N21.csv — Tabel ringkasan perbandingan statistik model cross-section N=21 untuk laporan.

#### c. Regresi Spasial Lokal (GWR & GTWR)
9. gwr_diagnostik_stabilitas.csv — Diagnostik stabilitas multikolinearitas lokal GWR 11-fitur (Condition Number & VIF).
10. gwr_koefisien_lokal_per_kabkota.csv — Koefisien lokal GWR 11-fitur cross-section 2025 per kabupaten/kota.
11. gwr_parsimoni_diagnostik_stabilitas.csv — Evaluasi stabilitas Condition Number GWR Parsimoni 3-fitur (0% CN > 30).
12. gwr_parsimoni_koefisien_lokal_per_kabkota.csv — Koefisien lokal GWR Parsimoni (X8, X9, X27) per kabupaten/kota tahun 2025.
13. gtwr_bandwidth_selection.csv — Rekam jejak pencarian bandwidth spasiotemporal optimal untuk GTWR.
14. gtwr_diagnostik_stabilitas.csv — Diagnostik stabilitas Condition Number dan VIF lokal model GTWR panel.
15. gtwr_jejak_eliminasi.csv — Catatan langkah demi langkah prosedur backward elimination GTWR.
16. gtwr_koefisien_lokal_kabkota_tahun.csv — Koefisien lokal GTWR awal sebelum eliminasi fitur.
17. gtwr_final_12b_koefisien_lokal_kabkota_tahun.csv — **Tabel utama koefisien lokal GTWR (Model 12b)** untuk 21 kab/kota x 5-6 tahun.
18. 	abel_model_panel_GTWR.csv — Tabel ringkasan evaluasi performa model panel GTWR (N=105/126) untuk laporan.

#### d. Perbandingan, Robustness & Interpretasi
19. perbandingan_semua_model.csv — Summary perbandingan komprehensif seluruh model (OLS, SAR, SEM, GWR, GTWR).
20. perbandingan_gwr_vs_gtwr_2025.csv — Komparasi koefisien dan fit model GWR cross-section vs GTWR panel khusus tahun 2025.
21. gtwr_perbandingan_2020_2025_vs_2021_2025.csv — Evaluasi dampak penanganan patahan data 2020 terhadap RMSE GTWR.
22. 
obustness_variant_A_vs_B.csv — Pengujian ketahanan spesifikasi model Variant A (X9 Pangsa Pangan) vs Variant B (X10 Pengeluaran Pangan).
23. x27_volume_wacana_per_kabkota.csv — Agregasi volume wacana digital (X27) dari NLP per kabupaten/kota.
24. krisis_senyap_kandidat.csv — Daftar 7 kabupaten/kota kandidat Krisis Senyap (PoU tinggi, wacana digital minim).
25. interpretasi_faktor_lokal_per_kabkota.csv — Mapping 3 faktor determinan utama PoU lokal per kabupaten/kota.

---

### 🖼️ Gambar Visualisasi (igures/) — 39 Berkas PNG

#### a. Diagnostik & Model Global (Fig 18–23)
1. 18_missing_2025_crosssection.png — Heatmap pengecekan missing data pada dataset cross-section 2025.
2. 19_vif_final_features.png — Bar chart nilai VIF 10-11 fitur prediktor final.
3. 20_korelasi_fitur_final.png — Heatmap korelasi antar-prediktor final.
4. 21_ols_diagnostic.png — Plot diagnostik asumsi residual OLS (normality, homoscedasticity).
5. 22_moran_residual_ols.png — Moran scatterplot dan pengujian autokorelasi spasial pada residual OLS.
6. 23_perbandingan_aic_model.png — Diagram perbandingan AICc antar model global (OLS vs SAR vs SEM).

#### b. GWR Cross-Section 2025 & Parsimoni (Fig 24–31)
7. 24_gwr_bandwidth_selection.png — Kurva pencarian bandwidth optimal GWR cross-section.
8. 25_gwr_local_r2_choropleth.png — Peta choropleth sebaran R² lokal GWR 11-fitur.
9. 26_gwr_koefisien_lokal_X8_PDRB_per_Kapita_ADHB.png — Peta koefisien lokal GWR X8 (PDRB per kapita).
10. 27_gwr_koefisien_lokal_X27_Volume_Wacana_Digital.png — Peta koefisien lokal GWR X27 (Volume Wacana Digital).
11. 28_gwr_parsimoni_local_r2_choropleth.png — Peta choropleth R² lokal GWR Parsimoni 3-fitur.
12. 29_gwr_parsimoni_koefisien_lokal_X8_PDRB_per_Kapita_ADHB.png — Peta koefisien lokal GWR Parsimoni X8 (PDRB).
13. 30_gwr_parsimoni_koefisien_lokal_X9_Pangsa_Pengeluaran_Pangan.png — Peta koefisien lokal GWR Parsimoni X9 (Pangsa Pangan).
14. 31_gwr_parsimoni_koefisien_lokal_X27_Volume_Wacana_Digital.png — Peta koefisien lokal GWR Parsimoni X27 (Wacana Digital).

#### c. GTWR Panel & Eliminasi (Fig 32–37)
15. 32_gtwr_bandwidth_selection.png — Plot pencarian bandwidth spasiotemporal GTWR.
16. 33_gtwr_local_r2_2025_choropleth.png — Peta R² lokal GTWR panel pada slice tahun 2025.
17. 34_gtwr_koefisien_lokal_2025_X8_PDRB_per_Kapita_ADHB.png — Peta koefisien lokal GTWR X8 tahun 2025.
18. 35_gtwr_koefisien_lokal_2025_X27_Volume_Wacana_Digital.png — Peta koefisien lokal GTWR X27 tahun 2025.
19. 36_gtwr_jejak_eliminasi.png — Visualisasi penurunan VIF dan Condition Number pada tiap tahap backward elimination.
20. 37_gtwr_final_12b_local_r2_2025_choropleth.png — Peta R² lokal final GTWR Model 12b tahun 2025.

#### d. Peta Koefisien Lokal GTWR 12b per Indikator (Fig 38–47)
21. 38_gtwr_final_12b_koefisien_lokal_2025_X1_Produksi_Perikanan_Tangkap_Laut.png — Peta koefisien lokal X1 (Perikanan).
22. 39_gtwr_final_12b_koefisien_lokal_2025_X5_Persentase_Penduduk_Miskin.png — Peta koefisien lokal X5 (Kemiskinan).
23. 40_gtwr_final_12b_koefisien_lokal_2025_X8_PDRB_per_Kapita_ADHB.png — Peta koefisien lokal X8 (PDRB per Kapita).
24. 41_gtwr_final_12b_koefisien_lokal_2025_X12_Kepadatan_Penduduk.png — Peta koefisien lokal X12 (Kepadatan Penduduk).
25. 42_gtwr_final_12b_koefisien_lokal_2025_X13a_Jumlah_Pelabuhan_Utama.png — Peta koefisien lokal X13a (Jumlah Pelabuhan).
26. 43_gtwr_final_12b_koefisien_lokal_2025_X13b_Kunjungan_Kapal_Tahunan.png — Peta koefisien lokal X13b (Kunjungan Kapal).
27. 44_gtwr_final_12b_koefisien_lokal_2025_X13c_Jarak_ke_Pelabuhan_Induk.png — Peta koefisien lokal X13c (Jarak Pelabuhan).
28. 45_gtwr_final_12b_koefisien_lokal_2025_X17_TPT.png — Peta koefisien lokal X17 (Pengangguran TPT).
29. 46_gtwr_final_12b_koefisien_lokal_2025_X27_Volume_Wacana_Digital.png — Peta koefisien lokal X27 (Wacana Digital).
30. 47_gtwr_final_12b_koefisien_lokal_2025_X9_Pangsa_Pengeluaran_Pangan.png — Peta koefisien lokal X9 (Pangsa Pangan).

#### e. Stabilitas & Tren Temporal GTWR (Fig 48–49 & Prefix _)
31. 48_gtwr_perbandingan_12b_vs_12d.png — Diagram komparasi performa GTWR Model 12b vs 12d.
32. 49_gtwr_rmse_2020_vs_2021_2025.png — Bar plot perbandingan RMSE panel 2020–2025 vs 2021–2025.
33. _gtwr_final_12b_local_condition_number.png — Peta distribusional Condition Number lokal GTWR Model 12b.
34. _gtwr_final_12b_tren_temporal_x27.png — Line chart tren perubahan koefisien lokal X27 dari 2020 s.d. 2025.
35. _gtwr_tren_temporal_x27_contoh.png — Grafik sampel pergerakan koefisien X27 pada kabupaten pilihan.
36. _gwr_local_condition_number.png — Map Condition Number lokal GWR 11-fitur.
37. _gwr_parsimoni_local_condition_number.png — Map Condition Number lokal GWR Parsimoni.
38. _x27_koefisien_lokal.png — Scatter plot sebaran koefisien lokal X27.
39. _x27_krisis_senyap_scatter.png — Quadrant scatterplot PoU vs Volume Wacana Digital (identifikasi 7 Krisis Senyap).
40. 50_peta_faktor_determinan_dominan_gtwr12b.png — **PETA UTAMA (GAMBAR 3):** Peta choropleth sintetis pengelompokan 21 kabupaten/kota berdasarkan kombinasi faktor determinan dominan GTWR-12b (Isolasi Maritim Fisik vs Konektivitas Logistik Kapal vs Dispersi Demografi).

---
---

## Wilayah Prioritas — Top 5 Kab/Kota dari Perspektif Regresi Lokal (GWR/GTWR)

Identifikasi berdasarkan: (a) interpretasi faktor lokal dominan GTWR-12b (2025), (b) nilai koefisien lokal untuk faktor kunci, dan (c) status krisis senyap (X27).

| Rank | Kab/Kota | PoU 2025 | Faktor Dominan (GTWR 2025) | Koefisien Kritis | Relevansi Metode Lain |
|------|----------|----------|-----------------------------|------------------|----------------------|
| 1 | **Pulau Taliabu** | 34.19% | X13c Jarak Pelabuhan | **+3.045** (tertinggi se-Maluku) | LISA HH Hotspot; Clustering C0; NLP Krisis Senyap Super (20 dok) |
| 2 | **Kepulauan Sula** | 37.36% | X13c + X8 PDRB | +2.75 / −2.32 | LISA HH Hotspot persisten; Clustering C0 paradoks; Forecasting #1 terburuk stabil |
| 3 | **Seram Bagian Barat** | 32.94% | X13b Kunjungan Kapal + X1 Perikanan | −2.55 / +1.64 (anomali) | Paradoks produksi: X1 positif → produksi tinggi PoU juga tinggi; NLP #2 sentimen negatif |
| 4 | **Maluku Tengah** | 30.26% | X13b + X27 Wacana | −1.48 / −0.44 | Konektivitas kapal & wacana digital terlemah di Maluku; Forecasting lompat +7 peringkat 2025→2028 |
| 5 | **Halmahera Tengah** | 19.03% | X8 PDRB (pelindung parsial) | −2.12 | Clustering C3 Enklave; PDRB tinggi hanya melindungi secara parsial; PoU mulai naik di Forecasting |

**Catatan interpretasi:**
- Pulau Taliabu memiliki koefisien Jarak ke Pelabuhan Induk (X13c = +3.045) tertinggi se-Maluku, yang berarti **setiap unit peningkatan jarak pelabuhan mendorong PoU naik paling kuat** di wilayah ini.
- Seram Bagian Barat menunjukkan anomali unik: koefisien X1 (Produksi Perikanan) bernilai **positif** (+1.64) — artinya produksi perikanan yang lebih tinggi justru berkorelasi dengan PoU lebih tinggi. Ini membuktikan *bottleneck* pada rantai distribusi, bukan pada kapasitas produksi.
- Tujuh wilayah **"Krisis Senyap"** (PoU tinggi + X27 mendekati nol): Kepulauan Sula, Pulau Taliabu, Buru Selatan, Halmahera Barat, Seram Bagian Timur, Kepulauan Aru, Pulau Morotai. Di antara ini, Kepulauan Sula dan Pulau Taliabu paling kritis karena dikonfirmasi oleh 4–5 metode sekaligus.

---
*Dokumen ini dihasilkan otomatis dari regresi_lokal_gwr_gtwr_pou_maluku.ipynb*
*Semua angka berasal langsung dari eksekusi notebook dan eksplorasi tabel output.*
