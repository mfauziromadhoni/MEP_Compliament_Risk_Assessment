# Internal Audit Analytics — MEP Compliance & Risk Assessment
### Bagian 1 dari 3: Compliance Analysis & Risk Identification (Google Colab)

Proyek ini merupakan bagian pertama dari rangkaian analisis audit internal terhadap peralatan **MEP (Mechanical, Electrical, Plumbing)** — Lift, Escalator, Sprinkler, Genset, dan HVAC — menggunakan Python di Google Colab. Fokus bagian ini adalah dua fungsi inti pekerjaan Internal Audit: **pemeriksaan kesesuaian implementasi prosedur** dan **identifikasi serta evaluasi potensi risiko/pelanggaran**.

## Latar Belakang Bisnis

Peralatan MEP yang tidak dirawat sesuai prosedur berisiko menyebabkan kegagalan fungsi kritis — lift terhenti, sprinkler tidak aktif saat kebakaran, genset gagal saat listrik padam. Dampaknya bukan hanya finansial, tapi juga menyangkut keselamatan jiwa. Analisis ini membantu tim audit internal menentukan **site dan kategori peralatan mana yang membutuhkan perhatian paling besar**, berdasarkan data historis — bukan asumsi.

## Dataset

| Detail | Keterangan |
|---|---|
| Sumber | `MEP_Internal_Audit_Dataset.csv` |
| Jumlah observasi | 1.251 catatan audit |
| Jumlah kolom | 21 kolom (22 setelah penambahan kolom bantu) |
| Cakupan | 217 unit peralatan di 10 site, periode Q1 2023 – Q4 2024 |
| Missing values | `Days_To_Resolve` (870 baris kosong — wajar, hanya relevan untuk kasus dengan temuan yang memerlukan penyelesaian) |

**Kolom kunci:**

| Kolom | Deskripsi |
|---|---|
| `Audit_Date` / `Audit_Quarter` | Dimensi waktu audit |
| `Site_Name` / `Site_Type` | Lokasi dan jenis bangunan yang diaudit |
| `Equipment_Category` / `Equipment_Age_Years` | Kategori dan usia peralatan MEP |
| `Maintenance_Compliance` | Target Analisis 1 — status kepatuhan (`Compliant`/`Non-Compliant`) |
| `Risk_Level` | Target Analisis 2 — tingkat risiko (`Low`/`Medium`/`High`/`Critical`) |
| `Finding_Category` | Kategori temuan audit (dokumentasi, defek teknis, deviasi prosedur, dll.) |

## Metodologi

1. **Data Understanding** — Memeriksa struktur, tipe data, dan missing value untuk memastikan kolom-kolom kunci analisis bebas dari data kosong.
2. **Data Cleaning** — Mengonversi `Audit_Date` ke tipe `datetime`, membuat kolom bantu `Audit_Quarter_Sort` (tipe `Period`) agar urutan kuartal pada visualisasi tren selalu kronologis, serta memvalidasi nilai unik pada kolom kategorikal utama.
3. **Analisis 1 — Compliance Analysis**, mencakup:
   - Distribusi compliance keseluruhan
   - Non-compliance rate per site
   - Non-compliance rate per kategori peralatan
   - Tren non-compliance dari kuartal ke kuartal
4. **Analisis 2 — Risk Identification & Evaluation**, mencakup:
   - Distribusi tingkat risiko keseluruhan (dengan `Risk_Level` dipaksa berurutan: Low → Medium → High → Critical menggunakan `pd.Categorical`)
   - **Risk Matrix** (heatmap Site × Equipment Category) berdasarkan proporsi risiko High/Critical
   - Hubungan usia peralatan dengan tingkat risiko (boxplot)
   - Kategori temuan yang paling sering memicu risiko tinggi

## Temuan Utama

### 📋 Compliance Analysis

- **Compliance keseluruhan hampir seimbang** — sekitar 50% Compliant vs 50% Non-Compliant, menunjukkan ruang perbaikan yang signifikan secara organisasi.
- **Tiga site dengan non-compliance tertinggi:** RS Harapan Sehat (65,06%), Gudang Logistik Cikarang (63,28%), dan Apartemen Green Valley (61,98%) — mengindikasikan masalah kepatuhan yang terkonsentrasi pada lokasi tertentu, bukan tersebar merata.
- **Kategori peralatan paling bermasalah:** Genset (56,19%) dan HVAC (56,18%) memiliki non-compliance rate tertinggi, sementara Escalator terendah (34,50%).
- **Tren kuartalan berfluktuasi:** Non-compliance rate naik dari 41,72% (Q1 2023) ke puncaknya 57,05% (Q3 2023) — titik kritis yang perlu diinvestigasi — lalu membaik bertahap hingga 46,41% di Q4 2024.

### ⚠️ Risk Identification & Evaluation

- **Risk Matrix (Site × Equipment Category)** mengidentifikasi lima kombinasi *hotspot* risiko High/Critical tertinggi:
  - Gudang Logistik Cikarang → Genset (63,6%), Sprinkler (57,1%)
  - RS Harapan Sehat → Genset (53,9%), Sprinkler (46,0%)
  - Apartemen Green Valley → Genset (50,0%), Sprinkler (50,0%)
  - Menara Bisnis Selatan → Sprinkler (54,5%)
  - RS Medika Utama → Genset (50,0%)
- **Usia peralatan berkontribusi terhadap risiko** — peralatan dengan Risk_Level High/Critical cenderung memiliki median usia yang sedikit lebih tua, mendukung dugaan perlunya evaluasi capex untuk penggantian alat yang sudah menua.
- **Kategori temuan pemicu risiko tinggi:** Dokumentasi Tidak Lengkap, Defek Teknis/Safety, dan Deviasi Prosedur Maintenance adalah tiga pemicu utama di balik temuan berisiko High/Critical.

## Rekomendasi Prioritas Audit

1. **Site:** RS Harapan Sehat, Gudang Logistik Cikarang, dan Apartemen Green Valley — diprioritaskan untuk audit tambahan di luar jadwal reguler.
2. **Kategori Peralatan:** Genset dan Sprinkler — kombinasi site+peralatan ini paling sering muncul sebagai hotspot risiko tinggi pada Risk Matrix.
3. **Area Temuan:** Memastikan kelengkapan dokumentasi dan kepatuhan prosedur maintenance menjadi area intervensi dengan dampak terbesar terhadap penurunan risiko.
4. **Investigasi lanjutan untuk lonjakan Q3 2023** — perlu ditelusuri penyebab spesifik di balik kenaikan tajam non-compliance rate pada periode tersebut.

## Tools & Library

- **Python 3** (Google Colab)
- `pandas` — manipulasi, agregasi, dan pivot data
- `matplotlib`, `seaborn` — visualisasi (pie chart, bar chart, line chart, heatmap, boxplot)

## Struktur Notebook

| Tahap | Isi |
|---|---|
| Ringkasan Bisnis | Konteks proyek dan tujuan dua poin job description Internal Audit |
| Import Data | Memuat dataset audit MEP |
| Data Understanding | Penjelasan kolom kunci, pengecekan shape, tipe data, dan missing value |
| Data Cleaning | Konversi `Audit_Date`, pembuatan `Audit_Quarter_Sort`, validasi nilai kategorikal |
| Analisis 1: Compliance | Distribusi keseluruhan, per site, per kategori peralatan, dan tren waktu |
| Analisis 2: Risk Identification | Distribusi risk level, Risk Matrix (heatmap), usia vs risiko, kategori temuan kritis |
| Kesimpulan | Ringkasan temuan dan rekomendasi prioritas audit |

## Proyek Lanjutan

Proyek ini merupakan bagian pertama dari rangkaian portofolio Internal Audit MEP. Dua poin job description berikutnya akan dianalisis menggunakan:
- **Microsoft Excel** — konfirmasi ke pihak terkait dan efektivitas tindak lanjut (follow-up) audit
- **Power BI** — investigasi mendalam terhadap temuan berulang dan evaluasi dampak finansial

## Batasan (Limitations)

- Analisis bersifat deskriptif (EDA) dan belum mencakup pengujian statistik formal (mis. uji korelasi/regresi) untuk memvalidasi hubungan usia peralatan dengan risiko secara kuantitatif.
- Risk Matrix dihitung berdasarkan proporsi sederhana; kombinasi site-equipment dengan jumlah audit kecil berpotensi menghasilkan persentase yang kurang stabil secara statistik.
- Lonjakan non-compliance di Q3 2023 teridentifikasi dari data, namun penyebab akar masalahnya (root cause) belum tergali dari dataset ini dan memerlukan investigasi kualitatif lebih lanjut di lapangan.
