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

## 9. Temuan Utama

1. **Kenaikan harga beras** adalah isu dengan volume terbesar DAN skor prioritas tertinggi (0.72) -- 55.6% negatif
2. **Program MBG mendominasi wacana** (topik 1, 4, 6, 7, 8, 9 dalam top 10) dengan sentimen campuran; isu keracunan makanan basi sangat viral di 3-gram
3. **Kelaparan masyarakat adat** punya sentimen negatif tertinggi (86.5%) meski volume relatif kecil (37 dok)
4. **Keracunan makanan** (kasus MBG) menjadi trigram viral: "keracunan makanan basi", "makanan basi mbg"
5. **Twitter** paling informatif secara institusional; **TikTok** paling besar volume; **YouTube** paling kritis
6. Model sentimen mencapai **F1=0.926** di test set -- kualitas label andal untuk analisis downstream
7. **Koherensi topik 0.67** dan diversitas **0.91** -- model topik berkualitas baik (di atas threshold umum 0.5)

---

## 10. File Output

### Data Utama
- `dataset_final_dengan_topik_sentimen.csv` (~1.9MB) -- dataset lengkap dengan label topik + sentimen
- `skor_prioritas_topik_final.csv` -- skor prioritas semua 88 topik
- `topic_info.csv` -- metadata lengkap semua topik BERTopic
- `topics_over_time.csv` -- evolusi topik per tahun 2021-2026
- `contoh_representatif_topik.csv` -- contoh dokumen per topik

### Leksikon dan N-gram
- `top20_kata.csv`, `top15_bigram.csv`, `top15_trigram.csv`
- `top15_bigram_positif.csv`, `top15_bigram_negatif.csv`
- `kata_khas_platform.csv`

### Model dan Diagnostik
- `riwayat_pelatihan_sentimen.csv` -- training curves model sentimen
- `eksperimen_perbandingan_backend.csv` -- perbandingan 9 konfigurasi BERTopic
- `analisis_kesalahan_sentimen.csv` -- error analysis model sentimen
- `ringkasan_analisis.txt` -- ringkasan eksekutif otomatis

### Visualisasi (35 gambar/file)
- Fig 01-10: EDA, frekuensi kata, wordcloud, distribusi
- Fig 11-20: Sentimen per topik, tren temporal, platform
- Fig 21-25: BERTopic visualisasi interaktif (HTML) + dendogram
- Fig 26-35: Dashboard eksekutif, radar sentimen, Sankey platform-topik, evolusi topik

### RAG Documents
- 88 file `topic_X.txt` -- representasi tekstual per topik untuk RAG pipeline

---
*Dihasilkan dari pipeline NLP tim GEMASTIK 2026 (run: 2026-07-31 15:02:36)*
