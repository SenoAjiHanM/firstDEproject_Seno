# ETL Pipeline Design: E-Commerce Orders

## 1. Overview
Pipeline ini memproses data transaksi e-commerce harian untuk membersihkan inkonsistensi format (seperti kapitalisasi dan angka abjad menyatu), membuang data duplikat serta invalid, dan menghasilkan ringkasan performa penjualan yang jelas dan bersih berdasarkan kategori produk.

## 2. Extract
- Sumber: File lokal raw_orders.csv (data transaksi utama) dan raw_products.csv (referensi master produk). Dua file ini adalah sumber atau data mentah yang digunakan. Data ini adalah data yang karakternya masih banyak kesalahan sebagaimana dijelaskan di Overview
- Format: CSV (Comma Separated Values).
- Volume: raw_orders.csv berisi 130 baris dan 11 kolom. raw_products.csv berisi 10 baris dan 4 kolom.
  
## 3. Transform
- Langkah 1: Deduplikasi data. Menghapus 10 baris yang terindikasi duplikat pada tabel pesanan supaya tidak terjadi kesalahan dalam perhitungan pendapatan.
- Langkah 2: Anomali nilai dibersihkan dengan cara menghapus 10 baris yang memiliki nilai total_harga kosong (NaN) atau bernilai negatif. Nilai - nilai ini tidak valid secara logika bisnis.
- Langkah 3: Imputasi nilai kosong. Mengisi kolom customer_email yang kosong (20 baris) dengan string "unknown@placeholder.com" untuk menjaga formalitas data transaksi yang tersisa tanpa membuang data pesanan.
- Langkah 4: Standarisasi teks. Mengonversi format penulisan pada kolom channel menjadi huruf kecil seragam (seperti 'marketplace', 'website', 'mobile_app') dan menerapkan standarisasi Title Case pada kolom kota.
- Langkah 5: Parsing format tanggal. Menyeragamkan berbagai format inkonsisten pada tanggal_order menjadi format berbentuk YYYY-MM-DD HH:MM:SS.
- Langkah 6: Feature engineering. Mengekstrak nama bulan dari tanggal_order untuk membuat kolom bulan, dan membuat kolom kategori_harga yang mengklasifikasikan transaksi menjadi "kecil", "sedang", atau "besar" berdasarkan rentang total_harga.

## 4. Load
- Tujuan: Penyimpanan data lokal untuk diakses oleh Data Analyst.
- Format output: "orders_clean.csv" (110 baris) untuk menyimpan data pada level transaksi setelah pembersihan dan "summary_report.csv" untuk menyimpan hasil agregasi jumlah pesanan, total pendapatan, dan rata-rata pendapatan berdasarkan kategori barang.

## 5. Orchestration
- Tool: Apache Airflow
- Schedule: @daily. Artinya, eksekusi dilakukan secara harian pada tengah malam untuk setiap batch data hari sebelumnya.
- DAG flow: Extract_CSVs -> Transform_Cleaning_Standardization -> Transform_Feature_Creation -> [Load_Orders_Clean, Load_Summary_Report].

## 6. Error Handling
- Skenario 1: File input tidak ditemukan pada direktori yang dituju (FileNotFoundError). Pipeline akan menghentikan proses ekstraksi secara otomatis, menggagalkan status eksekusi, dan pada akhirnya mencatat log error.
- Skenario 2: Skema data mentah berubah (misalnya hilangnya kolom total_harga). Eksekusi transformasi dihentikan menggunakan validasi skema atau blok try-except untuk mencegah korupsi pada data target.

## 7. Monitoring
- Bagaimana cara tahu pipeline sukses? Log sistem Airflow mengindikasikan status eksekusi DAG "Success". Hal ini juga dapat terlihat dengan munculnya orders_clean.csv dan summary_report.csv yang diperbarui di direktori tujuan.
- Bagaimana cara tahu data berkualitas? Memastikan file hasil tidak memiliki nilai null pada total_harga, menjamin order_id unik pada orders_clean.csv, dan seluruh baris mematuhi format tanggal standar.