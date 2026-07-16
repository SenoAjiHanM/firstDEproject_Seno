# End-to-End ETL Pipeline: E-Commerce Orders

## Deskripsi
Proyek ini merupakan dummy project dalam hal pipeline Extract, Transform, Load (ETL) end-to-end untuk memproses data transaksi e-commerce harian. Skrip ini dirancang untuk mengonsumsi data mentah, membersihkan inkonsistensi format, membuang duplikasi serta data invalid, dan menghasilkan ringkasan performa penjualan berdasarkan kategori produk.

## Alat yang Digunakan
* Python 3.8+
* Pandas
* SQL
* Apache Airflow (Orchestration Concept)

## Struktur Repositori
* `raw_orders.csv` - Data mentah yang berisi transaksi.
* `raw_products.csv` - Data mentah yang berisi referensi produk.
* `etl_pipeline.py` - Skrip Python utama yang mengeksekusi proses ETL.
* `etl_design.md` - Dokumen penjelasan desain rancangan arsitektur pipeline.
* `orders_clean.csv` - Output data transaksi yang telah melewati fase pembersihan.
* `summary_report.csv` - Output tabel agregasi performa penjualan.

## Eksekusi
1. Pastikan Python 3.8+ dan library Pandas telah terinstal di lingkungan eksekusi.
2. Jalankan perintah instalasi dalam Terminal jika belum terinstal:
