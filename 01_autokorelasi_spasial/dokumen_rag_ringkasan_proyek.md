# Dokumen Referensi: Autokorelasi Spasial PoU Kepulauan Maluku

## Ringkasan Proyek
Proyek GEMASTIK XIX/2026 (Divisi Data Mining) menganalisis pola spasial Prevalensi Ketidakcukupan Konsumsi Pangan (PoU) pada 21 kabupaten/kota di Provinsi Maluku dan Maluku Utara, periode 2020-2025 (126 observasi panel), untuk menguji hipotesis paradoks distribusi pangan di wilayah kepulauan.

## Cakupan Data
- Jumlah kabupaten/kota: 21 (Maluku: 11, Maluku Utara: 10)
- Rentang tahun: 2020-2025
- Jumlah observasi panel: 126
- Jumlah variabel prediktor (X): 26

## Kualitas Data dan Praproses
- Total 309 dari 3276 sel numerik (9.4 persen) merupakan hasil imputasi, bukan data mentah asli. Strategi: interpolasi linear internal per kabupaten/kota, dilanjutkan forward/backward-fill, dan KNN Imputer (k=3) lintas-kabupaten sebagai penanganan sisa.
- Kolom X1 (Produksi Perikanan Tangkap Laut) mengandung anomali format angka pada tahun 2023-2024 (campuran pemisah ribuan dan desimal ala format Indonesia); telah dinormalisasi dengan aturan titik terakhir sebagai pemisah desimal, diverifikasi melalui kontinuitas deret waktu.
- X4_NTP dan X18_TKPertanian adalah data level provinsi (bukan diferensiator antar kabupaten/kota), digunakan sebagai variabel kontrol saja.
- X19_AirMinum tahun 2020 memakai definisi lama (pra-revisi Bappenas), tidak sepenuhnya comparable dengan 2021 ke atas; diberi flag flag_definisi_lama_air.

## Metodologi Spasial
- Matriks pembobot spasial: K-Nearest Neighbors (KNN) berbasis jarak antar-centroid pada proyeksi Azimuthal Equidistant berpusat pada wilayah studi, k=4 (dipilih melalui uji sensitivitas k=3 hingga k=7). Queen/Rook Contiguity tidak digunakan karena wilayah studi adalah kepulauan murni tanpa batas darat yang bersinggungan secara luas.
- Statistik yang dihitung: Global Moran's I (per tahun, dengan uji permutasi 999 kali), Moran scatterplot, Local Indicators of Spatial Association (LISA / Local Moran's I), Getis-Ord Gi* (hotspot/coldspot), dan Moran's I Bivariat (PoU terhadap X1, X13_IKM, X5).

## Temuan Kunci
- Global Moran's I PoU 2025: 0.2826 (p=0.014).
- Tren temporal: rata-rata Moran's I 2020-2022 = 0.077, meningkat menjadi 0.249 pada 2023-2025, mengindikasikan penguatan pengelompokan spasial PoU.
- Klaster LISA High-High signifikan (2025): tidak ada.
- Klaster LISA Low-Low signifikan (2025): ['Halmahera Tengah', 'Halmahera Selatan', 'Kota Tidore Kepulauan'].
- Outlier spasial LISA: ['Halmahera Barat'].
- Coldspot Getis-Ord Gi*: ['Halmahera Barat', 'Halmahera Tengah', 'Halmahera Selatan', 'Kota Ternate', 'Kota Tidore Kepulauan'].
- Hotspot Getis-Ord Gi*: tidak ada.
- Analisis persistensi 2020-2025 (LISA dan Gi* dihitung untuk seluruh 6 tahun, bukan hanya tahun terakhir, karena signifikansi lokal pada sampel N=21 unit memiliki daya uji terbatas pada satu tahun saja): kabupaten/kota dengan klaster High-High LISA signifikan minimal 2 dari 6 tahun = ['Pulau Taliabu', 'Kepulauan Sula']; Low-Low minimal 2 dari 6 tahun = ['Halmahera Tengah', 'Halmahera Selatan', 'Kota Tidore Kepulauan']; coldspot Gi* minimal 2 dari 6 tahun = ['Halmahera Selatan', 'Halmahera Tengah', 'Kota Tidore Kepulauan', 'Halmahera Barat']; hotspot Gi* minimal 2 dari 6 tahun = ['Kepulauan Sula', 'Pulau Taliabu'].
- Moran's I Bivariat menunjukkan asosiasi spasial signifikan positif antara PoU dan persentase penduduk miskin (X5), sementara asosiasi dengan produksi perikanan (X1) dan indeks konektivitas maritim (X13_IKM) tidak signifikan pada tahun terakhir observasi -- mendukung narasi bahwa PoU tinggi tidak semata dijelaskan oleh rendahnya kapasitas produksi pangan lokal.

## Keterbatasan
- N=21 unit spasial tergolong kecil untuk metode kalibrasi intensif seperti GWR/MGWR; hasil Moran's I dan LISA di sini adalah metode yang stabil untuk ukuran sampel tersebut.
- Self-Sufficiency Ratio (SSR) presisi tidak dapat dihitung karena data populasi dan luas wilayah eksplisit tidak tersedia pada dataset final; digunakan Indeks Konektivitas Maritim (IKM) sebagai proksi kapasitas distribusi/keterhubungan.
- Poligon batas wilayah diunduh dari rantai sumber eksternal (repositori GeoJSON per kabupaten/kota di GitHub sebagai sumber utama, lalu BPS Geoportal, lalu GADM v4.1 sebagai cadangan); apabila seluruh sumber gagal diakses, notebook otomatis memakai mode peta gelembung berbasis titik centroid. Kabupaten Pulau Taliabu tidak memiliki poligon pada sumber utama (pemekaran 2013) dan ditampilkan sebagai penanda titik pada peta choropleth. Sumber yang berhasil digunakan pada eksekusi ini: Repositori GeoJSON per Kabupaten/Kota (GitHub, sumber utama).