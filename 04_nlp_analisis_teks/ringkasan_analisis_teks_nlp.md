# Ringkasan Eksperimen Analisis Teks / NLP
## Ketahanan Pangan Maluku dan Maluku Utara -- Media Sosial 2021-2026
*Output dari pipeline BERTopic + Analisis Sentimen (GEMASTIK XIX/2026)*
*Timestamp run: 2026-07-31 15:02:36*

---

## 1. Data dan Preprocessing

| Parameter | Nilai |
|-----------|-------|
| Data mentah | 2.810 dokumen |
| Setelah filter relevansi wilayah | **2.266 dokumen** |
| Rentang waktu | 2021 -- 2026 |
| Platform | TikTok (1.068), Twitter/X (678), YouTube (520) |

---

## 2. Model Sentimen

### Arsitektur dan Pelatihan

- **Model dasar:** IndoBERT / transformer berbahasa Indonesia
- **Metode label:** Pseudo-labeling otomatis (confidence threshold tinggi: 1.545 dari 2.266 dokumen)
- **Fine-tuning:** 6 epoch (best model di epoch 3)

### Performa Model

| Metrik | Nilai |
|--------|-------|
| F1 Macro (validasi, epoch terbaik) | **0.9051** |
| F1 Macro (test set independen) | **0.9257** |
| Akurasi (test set) | **0.931** |
| Baseline tanpa fine-tuning (F1 Macro) | 0.9251 |
| Peningkatan fine-tuning | +0.0006 |

*Catatan: baseline sudah sangat tinggi -- model pretrained sudah mampu, fine-tuning memberikan peningkatan marginal.*

### Riwayat Pelatihan

| Epoch | Eval Loss | Akurasi | F1 Macro |
|-------|-----------|---------|----------|
| 1 | 0.603 | 0.780 | 0.748 |
| 2 | 0.391 | 0.858 | 0.849 |
| **3** | **0.351** | **0.909** | **0.905** ← terbaik |
| 4 | 0.452 | 0.897 | 0.891 |
| 5 | 0.630 | 0.871 | 0.864 |
| 6 | 0.531 | 0.901 | 0.895 |

### Distribusi Sentimen Final (N=2.266)

| Sentimen | Jumlah | Persen |
|----------|--------|--------|
| Neutral | 1.008 | 44.5% |
| **Negative** | **848** | **37.4%** |
| Positive | 410 | 18.1% |

**Lebih dari 1 dari 3 dokumen bersentimen negatif** -- dominan pada isu harga pangan dan keracunan MBG.

---

## 3. Model Topik (BERTopic)

### Eksperimen Backend Embedding

| Backend | Topik | Outlier | Coherence (c_v) | Diversitas | Layak |
|---------|-------|---------|-----------------|------------|-------|
| **TF-IDF + SVD** (n=10, min=12) | 74 | 12.71% | **0.583** | **0.920** | **Ya** ← terpilih |
| SBERT MiniLM-L12 (n=20, min=15) | 28 | 23.48% | 0.579 | 0.875 | Ya |
| TF-IDF + SVD (n=20, min=15) | 61 | 17.70% | 0.579 | 0.912 | Ya |
| All-MiniLM-L6-v2 (n=10, min=8) | 58 | 34.51% | 0.577 | 0.890 | Ya |
| All-MiniLM-L6-v2 (n=15, min=15) | 2 | 0% | 0.344 | 1.0 | Tidak |

**Model final terpilih:** TF-IDF + Truncated SVD (n_neighbors=10, min_cluster_size=12)
- **88 topik** (di luar outlier/noise)
- Coherence score (c_v): **0.6679**
- Diversitas topik: **0.9148**

---

## 4. Topik dengan Skor Prioritas Tertinggi

Skor prioritas = kombinasi volume dokumen + proporsi sentimen negatif + tren temporal

| Rank | Nama Topik | Skor | Docs | % Negatif |
|------|-----------|------|------|-----------|
| 1 | **Kenaikan Harga Beras dan Pangan** | **0.7235** | 81 | 55.6% |
| 2 | **Diskusi Program Makan Bergizi Gratis** | 0.6176 | 46 | 54.4% |
| 3 | **Kelaparan dan Kehidupan Masyarakat Adat** | 0.5997 | 37 | **86.5%** |
| 4 | **Komentar Menu Makan Bergizi Gratis** | 0.5953 | 56 | 32.1% |
| 5 | **Kritik Pajak dan Kebijakan Pemerintah** | 0.5375 | 33 | 75.8% |

### Top 10 Topik Volume Terbesar

| Rank | Topik | Dokumen |
|------|-------|---------|
| 1 | Kenaikan Harga Beras dan Pangan | 81 |
| 2 | Komentar Menu Makan Bergizi Gratis | 56 |
| 3 | Ketahanan Pangan Provinsi Maluku | 51 |
| 4 | Sekolah Rakyat dan Anak | 48 |
| 5 | Diskusi Program Makan Bergizi Gratis | 46 |
| 6 | Dukungan kepada Pemerintah Daerah | 43 |
| 7 | Diskusi Umum tentang MBG | 41 |
| 8 | Kemiskinan dan Kerawanan Pangan | 41 |
| 9 | Perbandingan Program MBG | 39 |
| 10 | Bantuan Sosial Presiden | 37 |

---

## 5. Analisis Frekuensi Kata & N-gram

### Top 20 Kata Paling Sering

`maluku` (633) · `mbg` (516) · `pangan` (358) · `makan` (212) · `beras` (207) · `indonesia` (199) · `anak` (178) · `program` (153) · `ketahanan` (151) · `bansos` (150) · `harga` (148) · `presiden` (145) · `makanan` (140) · `keracunan` (126) · `rakyat` (123)

### Top 15 Bigram (Frasa 2 Kata)

| Frasa | Frekuensi |
|-------|-----------|
| ketahanan pangan | 138 |
| provinsi maluku | 113 |
| maluku utara | 112 |
| bergizi gratis | 80 |
| makan bergizi | 71 |
| kasus keracunan | 46 |
| harga beras | 40 |
| keracunan makanan | 40 |

### Top 15 Trigram (Frasa 3 Kata)

| Frasa | Frekuensi |
|-------|-----------|
| makan bergizi gratis | 66 |
| pangan provinsi maluku | 48 |
| dinas ketahanan pangan | 45 |
| kasus keracunan makanan | 29 |
| keracunan makanan basi | 25 |
| makanan basi mbg | 24 |
| gerakan pangan murah | 17 |

---

## 6. Analisis per Sentimen

### Bigram Negatif Terbanyak

`harga beras` (21) · `beras mahal` (15) · `maluku utara` (14) · `beli beras` (11) · `swasembada pangan` (9) · `pak prabowo` (9) · `program mbg` (8)

### Bigram Positif Terbanyak

`anak anak` (10) · `terima kasih` (10) · `orang tua` (9) · `luar biasa` (9) · `semoga segera` (7) · `pangan lokal` (6) · `makanan bergizi` (6)

---

## 7. Kata Khas per Platform

| Platform | Kata Khas Teratas |
|----------|------------------|
| **TikTok** | kucing, telor, request, mbgnya, tender, burger, ngofa (Ternate) |
| **Twitter/X** | dinas, kegiatan, tenggara, update, gerakan, inflasi, stunting, polda |
| **YouTube** | meninggal, dosa, mencuri, miskin, mampu, omon-omon (informal) |

**TikTok**: konten lebih informal dan personal (reaksi terhadap menu MBG)
**Twitter**: lebih banyak konten institusional/berita resmi (dinas, kegiatan, inflasi)
**YouTube**: lebih emosional dan kritis (meninggal, dosa, mencuri) terkait isu MBG

---

## 8. Integrasi dengan Analisis Spasial (X27 -- Krisis Senyap)

Volume wacana digital per kabupaten (digunakan sebagai variabel X27 dalam model GWR/GTWR):
- Wacana digital tinggi di kota-kota besar (Ambon, Ternate) vs rendah di kabupaten terpencil
- 7 kandidat "krisis senyap": kabupaten dengan PoU tinggi tapi volume wacana rendah
  (Kepulauan Sula, Buru Selatan, Halmahera Barat, Pulau Taliabu, Seram Bagian Timur, Kepulauan Aru, Pulau Morotai)
- Topik prioritas tinggi (harga beras, kelaparan masyarakat adat) konsisten dengan PoU tertinggi

---

## 9. Fakta Unik NLP & Korelasi Lintas Metode (Cross-Method Insights)

Sintesis hasil NLP teks (sentimen & BERTopic) dengan 4 metode lainnya (Autokorelasi LISA, Clustering K-Means, Regresi GTWR/SEM, dan Forecasting XGBoost) mengungkapkan beberapa **fakta unik dan temuan spesifik** yang sangat penting bagi pengambil kebijakan:

### 1. 🚨 Peta Sentimen Negatif Kab/Kota & Korelasinya dengan Model Spasial & Forecasting

| Kabupaten / Kota | Total Dokumen | % Negatif | % Neutral | % Positive | Korelasi & Temuan Lintas Metode |
|------------------|---------------|-----------|-----------|------------|----------------------------------|
| **Kepulauan Tanimbar** | 73 | **65.75%** | 19.18% | 15.07% | **#1 Negatif Terbanyak.** Wilayah maritim terluar selatan; gejolak harga pangan & kelangkaan pasokan kapal ($X_{13b}$ di SEM) memicu amarah warga tertinggi di media. |
| **Seram Bagian Barat** | 56 | **58.93%** | 26.79% | 14.29% | **#2 Negatif Terbanyak.** 100% konsisten dengan posisi Seram Bagian Barat di **Kuadran Paradoks Clustering K-Means** dan proyeksi **rebound PoU naik (2025→2028)** di XGBoost. |
| **Buru Selatan** | 107 | **56.07%** | 26.17% | 17.76% | **#3 Negatif Terbanyak.** Berkolerasi langsung dengan **volatilitas PoU tertinggi se-Maluku (StdDev = 7.26)** dan residual paradoks produksi (+6.22). |
| **Buru** | 127 | **55.91%** | 13.39% | 30.71% | **#4 Negatif Terbanyak.** Masuk Kuadran Paradoks K-Means; keluhan tinggi atas harga beras lokal meskipun kabupaten ini adalah lumbung padi. |
| **Kepulauan Sula** | 78 | **53.85%** | 23.08% | 23.08% | **#5 Negatif Terbanyak.** Wilayah PoU tertinggi (#1); terverifikasi sebagai *High-High Hotspot* LISA dan terisolasi fisik (GTWR $X_{13c} = +2.75$). |

---

### 2. 🤫 Fenomena "Krisis Senyap Super" (Under-Reporting Digital)

- **Pulau Taliabu — Sentimen Negatif Terendah (5.00%) vs PoU Sangat Tinggi (34.19%–49.84%):**
  - Dari 2.266 dokumen, Pulau Taliabu hanya terwakili oleh **20 dokumen** dengan persentase **sentimen negatif hanya 5.00%** (didominasi narasi doa & harapan lokal).
  - *Integrasi Spasial:* Padahal secara geografis, Taliabu terbukti masuk *High-High Hotspot* LISA bersama Kepulauan Sula dan memiliki koefisien GTWR Jarak ke Pelabuhan Induk terbesar (**+3.05**).
  - *Kesimpulan:* Hal ini membuktikan keterisolasian saluran aspirasi digital warga (*Krisis Senyap Super*) di pulau terluar Maluku Utara, sehingga tingginya kerawanan pangan tidak terpancar di media digital arus utama.

---

### 3. 📱 Dikotomi Saluran Media (YouTube vs TikTok vs Twitter/X)

| Platform | Vol. Dokumen | % Negatif | % Neutral | % Positive | Karakteristik Wacana Publik |
|----------|--------------|-----------|-----------|------------|-----------------------------|
| **YouTube** | 520 | **63.08%** | 14.42% | 22.50% | **Wadah Luapan Emosi & Jeritan Warga.** Komentar video berita daerah dipenuhi kecaman atas kenaikan harga beras, isu kelaparan pedalaman, dan terhentinya kapal perintis. |
| **TikTok** | 1.068 | **42.70%** | 32.30% | 25.00% | **Arena Kesadaran Sosial & Viral.** Didominasi reaksi anak muda/ibu pasar terkait ulasan harga kebutuhan pokok lokal dan Program Makan Bergizi Gratis (MBG). |
| **Twitter / X** | 678 | **9.44%** | **86.73%** | 3.83% | **Kanal Berita Resmi & Institusional.** Dominan rilis pers dinas pemerintah, statistik inflasi formal, dan bot berita, sehingga steril dari emosi negatif warga. |

---

### 4. 📌 Topik Kritis Berdasarkan Prioritas Kebijakan

1. **Topik 0 — Kenaikan Harga Beras dan Pangan:**
   - Skor Prioritas = **0.7235** (Teratas #1 di seluruh 88 topik).
   - Volume 81 dokumen dengan **55.56% sentimen negatif**. Isu ini menjadi pemicu utama kegelisahan publik se-Kepulauan Maluku.
2. **Topik 10 — Kelaparan dan Kehidupan Masyarakat Adat:**
   - Proporsi Sentimen Negatif mencapai **86.49%** (tertinggi di antara topik utama) dengan Skor Prioritas **0.5997** (#3).
   - Menyoroti penderitaan masyarakat adat di pedalaman pulau-pulau besar (Halmahera & Seram) yang terisolasi dari akses logistik pangan dasar.
3. **Topik 51 & 54 — Pemborosan Makanan & Kritik Penanganan MBG:**
   - Proporsi Sentimen Negatif ekstrem mencapai **> 94.0%**.
   - Publik mengecam kelemahan rantai dingin (*cold chain*) dan keterlambatan distribusi MBG di pulau-pulau kecil yang menyebabkan makanan tiba dalam kondisi basi/rusak.

---

## 10. Temuan Utama

1. **Kenaikan harga beras** adalah isu dengan volume terbesar DAN skor prioritas tertinggi (0.72) -- 55.6% negatif
2. **Program MBG mendominasi wacana** (topik 1, 4, 6, 7, 8, 9 dalam top 10) dengan sentimen campuran; isu keracunan makanan basi sangat viral di 3-gram
3. **Kelaparan masyarakat adat** punya sentimen negatif tertinggi (86.5%) meski volume relatif kecil (37 dok)
4. **Keracunan makanan** (kasus MBG) menjadi trigram viral: "keracunan makanan basi", "makanan basi mbg"
5. **Twitter** paling informatif secara institusional; **TikTok** paling besar volume; **YouTube** paling kritis
6. Model sentimen mencapai **F1=0.926** di test set -- kualitas label andal untuk analisis downstream
7. **Koherensi topik 0.67** dan diversitas **0.91** -- model topik berkualitas baik (di atas threshold umum 0.5)

---

## 11. Daftar Berkas Tabel, Gambar, dan Output (Available Files)

Seluruh output eksperimen NLP tersimpan dalam folder `output/` dan `output/rag_documents/`:

### 📊 Berkas Tabel & Dataset (`output/`) — 16 Berkas CSV/TXT
1. `dataset_final_dengan_topik_sentimen.csv` (~1.9 MB) — Dataset utama 2.266 dokumen lengkap dengan label topik BERTopic, probabilitas, sentimen, dan metadata platform.
2. `skor_prioritas_topik_final.csv` — Skor prioritas kebijakan 88 topik berbasis kombinasi volume, proporsi sentimen negatif, dan tingkat keparahan isu.
3. `skor_prioritas_topik.csv` — Ranking awal prioritas topik sebelum pembobotan final.
4. `topic_info.csv` — Metadata lengkap seluruh 88 topik BERTopic (Count, Name, Representation, Key Words).
5. `topics_over_time.csv` — Matriks evolusi frekuensi dan volume topik per tahun (2021–2026).
6. `contoh_representatif_topik.csv` — Sampel dokumen paling representatif untuk setiap topik isu.
7. `top20_kata.csv` — Daftar 20 kata paling sering muncul dalam korpus wacana pangan.
8. `top15_bigram.csv` — 15 frase 2-kata (bigram) paling dominan.
9. `top15_trigram.csv` — 15 frase 3-kata (trigram) paling dominan.
10. `top15_bigram_positif.csv` — 15 bigram paling umum pada dokumen ber-sentimen positif.
11. `top15_bigram_negatif.csv` — 15 bigram paling umum pada dokumen ber-sentimen negatif (kelaparan, kenaikan harga, dll).
12. `kata_khas_platform.csv` — Ekstraksi kata-kata unik yang menjadi ciri khas platform (TikTok vs Twitter vs YouTube).
13. `riwayat_pelatihan_sentimen.csv` — Training/validation loss dan F1-score per epoch fine-tuning IndoBERT.
14. `eksperimen_perbandingan_backend.csv` — Komparasi performa 9 variasi arsitektur & embedding BERTopic.
15. `analisis_kesalahan_sentimen.csv` — Error analysis klasifikasi sentimen pada test set (false positive / false negative).
16. `ringkasan_analisis.txt` — Ringkasan statistik eksekutif otomatis hasil ekstraksi NLP.

---

### 🖼️ Berkas Visualisasi (`output/`) — 36 Berkas PNG & HTML

#### a. EDA & Distribusi Awal (Fig 01–07)
1. `01_distribusi_platform_raw.png` — Chart distribusi jumlah dokumen mentah per platform.
2. `02_distribusi_tahun_platform_raw.png` — Breakdown dokumen per platform lintas tahun 2021–2026.
3. `03_panjang_teks_raw.png` — Histogram sebaran panjang dokumen (karakter/kata).
4. `04_missing_value_raw.png` — Audit missing values pada atribut metadata dokumen.
5. `05_relevansi_wilayah_informasional.png` — Sebaran dokumen yang lolos filter relevansi geografi Kepulauan Maluku.
6. `06_distribusi_platform_setelah_cap.png` — Distribusi dokumen final setelah balancing/capping.
7. `07_heatmap_bulan_tahun.png` — Heatmap intensitas postingan per bulan dan tahun.

#### b. Analisis Kata & Sentimen (Fig 08–19)
8. `08_wordcloud_keseluruhan.png` — Wordcloud frekuensi kata dari seluruh korpus 2.266 dokumen.
9. `09_top20_kata.png` — Bar plot 20 kata teratas dalam korpus.
10. `10_top15_bigram.png` — Bar plot 15 bigram teratas.
11. `11_top15_trigram.png` — Bar plot 15 trigram teratas.
12. `12_jumlah_token_platform.png` — Perbandingan rata-rata token per postingan antar platform.
13. `13_kesepakatan_voting.png` — Tingkat kesepakatan pseudo-labeling antar model.
14. `14_kurva_pelatihan_sentimen.png` — Learning curves fine-tuning model IndoBERT sentimen.
15. `15_confusion_matrix_test_set.png` — Confusion matrix evaluasi klasifikasi 3 kelas sentimen (Positif, Netral, Negatif).
16. `15b_perbandingan_baseline_finetune.png` — Bar chart perbandingan F1-score baseline Lexicon vs IndoBERT Fine-tuned.
17. `16_proporsi_sentimen_final.png` — Pie chart proporsi akhir sentimen (37.4% Negatif).
18. `17_sentimen_per_tahun.png` — Tren pergeseran proporsi sentimen tahun 2021–2026.
19. `18_sentimen_per_platform.png` — Komparasi distribusi sentimen di TikTok vs Twitter vs YouTube.
20. `19_perbandingan_backend_embedding.png` — Plot evaluasi nilai coherence & diversity 9 backend embedding.

#### c. Pemodelan Topik BERTopic (Fig 20–35 & HTML Interaktif)
21. `20_top_topik_jumlah_dokumen.png` — Bar chart 10 topik terbesar berdasarkan jumlah dokumen.
22. `21_barchart_topik.html` — Visualisasi interaktif BERTopic bar chart bobot c-TF-IDF kata per topik.
23. `22_intertopic_distance_map.html` — Peta interaktif MDS Intertopic Distance Map 88 topik.
24. `23_hierarchy_topik.html` — Dendrogram hirarki pengelompokan topik BERTopic.
25. `24_topik_seiring_waktu.html` — Chart interaktif evolusi kemunculan topik seiring waktu.
26. `25_bigram_per_sentimen.png` — Comparison bar chart bigram khusus sentimen positif vs negatif.
27. `26_wordcloud_kontras_sentimen.png` — Wordcloud kontras kata positif vs negatif.
28. `27_heatmap_topik_sentimen.png` — Heatmap silang antara 88 topik dengan 3 kelas sentimen.
29. `28_kuadran_topik_sentimen.png` — Quadrant plot volume topik vs persentase sentimen negatif.
30. `29_treemap_topik.html` — Treemap interaktif proporsi ukuran topik.
31. `30_radar_sentimen_topik.png` — Radar chart emosi/sentimen pada topik-topik kunci.
32. `31_sankey_platform_topik_sentimen.html` — Diagram Sankey interaktif alur hubungan Platform → Topik → Sentimen.
33. `32_evolusi_topik_tahun.png` — Heatmap evolusi topik utama per tahun.
34. `33_skor_prioritas_topik.png` — Ranking visual 15 topik dengan skor prioritas tertinggi.
35. `34_kata_khas_platform.png` — Visualisasi kata kunci khas per platform.
36. `35_dashboard_eksekutif.png` — Executive summary dashboard infografis rangkuman hasil NLP.

---

### 📄 Berkas Dokumentasi RAG (`output/rag_documents/`) — 90 Berkas
- `ringkasan_umum_rag.json` — Metadata dan ringkasan eksekutif korpus teks untuk RAG pipeline.
- `semua_topik_rag.jsonl` — File JSON Lines berisi 88 ringkasan topik terstruktur siap di-ingest LLM/RAG.
- `topic_0.txt` s.d. `topic_87.txt` — 88 berkas deskripsi naratif individual untuk masing-masing topik isu (harga beras, kelaparan adat, keracunan MBG, dll).

---
*Dihasilkan dari pipeline NLP tim GEMASTIK XIX / 2026 (run: 2026-07-31 15:02:36)*
