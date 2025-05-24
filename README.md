# Café Dataset: Cleaning dan Analysis With Excel
---

## Overview  
Proyek ini mencakup **pembersihan data** dan **analisis** terhadap dataset transaksi Café tahun 2023. Fokus utama:  
- Identifikasi dan penanganan data tidak lengkap/ambigu.  
- Analisis tren pendapatan bulanan, produk terlaris, dan pola transaksi.  
- Korelasi harga-penjualan.
  
---

## Dataset  
**Sumber Data**: https://www.kaggle.com/datasets/ahmedmohamed2003/cafe-sales-dirty-data-for-cleaning-training
**Kolom **: `Transaction ID`, `Item`, `Quantity`, `Price Per Unit`, `Total Spent`, `Payment Method`, `Location`, dan `Transaction Date`.

**Pembersihan Data**:  
- Nilai kosong di kolom `Payment Method` dan `Location` diisi sebagai **"AMBIGUOUS_(nama kolom)"**.  
- Item ambigu (`AMBIGUOUS_ITEM`, `UNKNOWN`, `ERROR`) di beberapa baris kolom `Item`, nilainya tidak diubah karena ambiguitas nilai.
  
---

## Key Findings  
### 1. **Tren Pendapatan**  
- Total pendapatan tahunan: **\$84,868.50**.  
- **Bulan tertinggi**: Juni, Oktober, Januari.  
- **Bulan terendah**: Februari, September, Juli.  
- Pola **fluktuatif**: Penurunan Maret-Mei → Naik di Juni.  

### 2. **Produk Unggulan**  
- **Terlaris (kuantitas)**:  
  - ☕ **Kopi**: #1 di 6 bulan (Feb, Mar, Mei, Agt, Okt, Des).  
  - 🥗 **Salad**: #1 di 4 bulan (Apr, Jun, Jul, Nov).  
- **Kontributor pendapatan tertinggi**:  
  1. Salad 
  2. Sandwich  
  3. Smoothie  

### 3. **Pola Transaksi**  
- 🗓️ **Hari teraktif**: Jumat (puncak) → Senin → Minggu.  
- 📅 **Tipe hari**: Transaksi lebih tinggi di *weekday* vs. weekend/hari libur nasional.  

### 4. **Data Ambigu** 
- **Kolom Item**:
  - **450 transaksi ambigu (nilai kosong (AMBIGOUS ITEM), "ERROR" atau "UNKNOWN")**:  
    - 234 transaksi (harga \$3): Potensial menambah **Cake/Juice**.  
    - 216 transaksi (harga \$4): Potensial menambah **Sandwich/Smoothie**.  
  - **Dampak**: Peringkat 4–8 (Cookie, Juice, Cake, dll.) bisa berubah.  
- **Kolom Payment Method**:
  - Berdasarkan data, transaksi terbanyak tercatat pada kategori AMBIGOUS PAYMENT (nilai kosong), sehingga sulit mengidentifikasi metode pembayaran yang paling sering digunakan (total transaksi 2430.
  - Untuk analisis mengenai metode pembayaran, tidak dapat disimpulkan mengingat banyaknya data hilang dan tidak dapat diperbaiki sembarangan, juga tidak terlihat pola dari kolom lain.
- **Kolom Location**:
  - Berdasarkan data, transaksi terbanyak tercatat pada kategori AMBIGOUS PAYMENT (nilai kosong), sehingga sulit mengidentifikasi metode pembayaran yang paling sering digunakan (total transaksi 3110.
  - Sama seperti kolom payment, untuk analisis mengenai metode pembayaran, tidak dapat disimpulkan mengingat banyaknya data hilang dan tidak dapat diperbaiki sembarangan, juga tidak terlihat pola dari kolom lain.
  
### 5. **Korelasi Harga-Penjualan**  
- **Korelasi negatif lemah (r = -0.17)**: Penjualan cenderung turun saat harga naik.  
- **Pengecualian**: Salad tetap laris meski harga tinggi.  

---

## Recommendations  
1. **Optimasi Bulan Rendah**: Manfaatkan paket bundling unggulan+menengah, misalnya “Weekday Coffee Combo” (Coffee+Sandwich diskon 15%) dan “Salad Boost” (Salad+Smoothie harga spesial).
2. **Verifikasi Data Ambigu**: Mengatasi ambiguitas data dengan kasir wajib isi metode pembayaran & lokasi sebelum transaksi, audit 50 transaksi ambigu per minggu, serta optimasi database dan backup rutin.

---

## 📂 File Structure
```
cafe_dataset_cleaning_analysis/
├── analysis/
│   ├── analysis.xlsx        # File analisis
│   └── report.pdf           # Laporan analisis 
├── data/
│   ├── cleaned_data.xlsx    # Data bersih
│   ├── cleaned_process.xlsx # Proses pembersihan data
│   ├── cleaning_report.md   # Laporan pembersihan data
│   └── raw_Data.xlsx        # Data mentah
└── README.md           
```


---
