# Laporan Proyek Machine Learning - Oktavia Citra Resmi Rachmawati

## Project Overview

Pada proyek kali ini akan dibangun sistem rekomendasi film yang memberikan hasil berupa daftar film yang memiliki kemiripan (output) dengan karakteristik film yang diberikan 
(input).

### Background

Seiring berjalannya waktu, ribuan film dirilis di seluruh dunia dari semua platform dan negara, sehingga menemukan film yang cocok menjadi bagian dari kehidupan hiburan dari sejak awal munculnya banyak industri film [1]. Terdapat berbagai genre di industri film yang membuat orang-orang tidak sempat menjelajah banyak genre karena keterbatasan waktu atau biaya [2]. Salah satu fitur umum yang disediakan oleh layanan aplikasi streaming adalah sistem yang menampilkan semua film yang berasal dari beragam tahun dan negara produksi, tidak hanya menampilkan film-film terbaru sehingga pengguna akan tertarik memanfaatka fiturnya[3]. Sistem rekomendasi bermanfaat bagi pelanggan dan perusahaan pemilik aplikasi, karena semakin puas pelanggan, semakin besar kemungkinan pengguna ingin menggunakan sistem untuk kemudahan dalam mengoperasikan aplikasi, sehingga akan berpeluang menambah pendapatan perusahaan [4].

[1] https://iopscience.iop.org/article/10.1088/1742-6596/1916/1/012052

[2] https://ieeexplore.ieee.org/document/10055248/

[3] Movie recommender systems using hybrid model based on graphs with co-rated, genre, and closed caption features

[4] Movie Recommender System Using Collaborative Filtering

## Business Understanding

Dalam merumuskan proyek ini dibangun penyataan masalah, tujuan, dan pernyataan solusi seperti di bawah ini.

### Problem Statements

Berdasarkan latar belakang di atas, rincian masalahnya adalah
- Bagaimana mengolah data agar bisa masuk ke pemodelan rekomendasi film-film yang banyak memiliki kemiripan?
- Bagaimana membuat sistem rekomendasi film yang dengan peforma seakurat mungkin (minimal precision@N 85%) agar dapat meningkatkan ketertarikan pengguna?

### Goals

Untuk menjawab pertanyaan masalah di atas, maka akan dijabarkan sebagai berikut:
- Model yang cocok untuk menyelesaikan masalah tersebut adalah model yang berbasis dengan konten atau biasa disebut Content-Based Filtering.
- Melakukan evaluasi model sistem rekomendasi film dengan metrik precision@N 85%

### Solution statements

Solusi yang dapat dilakukan untuk memenuhi goals proyek ini diantaranya sebagai berikut:
- Mengolah data teks berkaitan dengan informasi film menjadi vektor yang bisa dihitung nilai kemiripannya
- Menampilkan detail data yang diuji dengan hasilnya untuk validasi kebenaran dari nilai metriknya

## Data Understanding
Dataset yang digunakan pada proyek kali ini dibuat oleh Nikhil Narayan yang di upload ke Kaggle pada Juni 2024. Sumber dataset: Weather Type Classification. Pada dataset ini terdiri dari 5043 baris dan 28 kolom data. Kondisi khusus dari data:
- memiliki jenis tipe data yang beragam untuk kolom-kolom yang ada diantaranya float64, int64, dan object
- terdapat 6 kolom yang memiliki nilai yang penuh atau tidak memiliki nilai hilang

Karena terdapat 28 kolom, sedangkan pembangun model pada proyek ini menggunakan content-based filter yang menyeleksi data yang mirip berdasarkan isinya, maka perlu dilakukan seleksi kolom dan menghasilkan dataframe yang memiliki 3 kolom penting berikut ini:

|Nama|Jenis|Tipe Data|Alasan|Jumlah Nilai Unik|
|----|-----|---------|------|-----------------|
|movie_title|independent|object|diperlukan untuk mencari film-film yang mirip dan mengembalikan nilai dalam bentuk nama-nama filmnya|4917|
|genres|dependent|object|mewakili representasi dari sebuah film memiliki jalan cerita seperti apa|914|
|content_rating|dependent|object|identifikasi mengenai segmen target penonton film|19|

Pada proyek ini, hanya melakukan EDA Univariate karena hanya terdapat 2 data kategorikal yang bisa amati distribusinya. Karena nilai unik berjumlah sangat banyak di 2 kolom tersebut, maka ditampilkan horizontal bar chart untuk nilai yang memiliki kemunculan paling banyak

|Nama|Chart|Analisis|
|----|-----|--------|
|genres|![genre chart](https://github.com/user-attachments/assets/c0180fd2-9940-4062-bc99-3ad2edc955ff)|Sebagian besar film yang memiliki genre drama|
|rating|![rating chart](https://github.com/user-attachments/assets/16f8b66c-94cf-4d48-ad39-9b128c05de71)|Sebagian besar film yang memiliki rating content R|

Yang dilakukan pada tahap ini diantaranya:
- Melihat ringkasan informasi struktur data dengan fungsi .info()
- Menghapus kolom-kolom yang lainnya selain movie_title, genres, dan content_rating
- Memeriksa jumlah nilai unik pada setiap kolom

## Data Preparation
Pada tahap ini perlu mempersiapkan data agar mudah diproses oleh model, apalagi jika masih mengandung kolom-kolom yang bertipe data object yang perlu dilakukan encoding untuk mengubahnya menjadi numerik supaya dapat dihitung nilai cosine similarity nya. Yang dilakukan pada tahap ini diantaranya:
|Aktifitas|Alasan|Ukuran dataset semula|Ukuran dataset sesudah preproses|
|---------|------|---------------------|--------------------------------|
|menghapus nilai \xa0 pada kolom movie_title|agar tidak menjadi noise ketika pengguna mau mencari rekomendasi berdasarkan film yang sudah ditontonnya|(5043,3)|(5043,3)|
|menghapus baris duplikat|karena nilai unik pada kolom movie_title sejumlah 4917 sehingga terdapat kemungkinan banyak baris data yang duplikat|(5043,3)|(4919,3)|
|menghapus baris data yang memiliki nilai hilang|tidak bisa dilakukan imputasi karena akan menyebabkan kesalahan analitik|(4919,3)|(4618,3)|
|melakukan one hot encoding terhadap kolom genres|supaya dapat dihitung nilai cosine similaritynya dengan representasi vektornya|(4618,3)|(4618,26)|
|melakukan one hot encoding terhadap kolom content_rating|supaya dapat dihitung nilai cosine similaritynya dengan representasi vektornya|(4618,26)|(4618,43)|

## Modeling
Tahapan ini membahas mengenai model sisten rekomendasi yang Anda buat untuk menyelesaikan permasalahan. Sajikan top-N recommendation sebagai output.

**Rubrik/Kriteria Tambahan (Opsional)**: 
- Menyajikan dua solusi rekomendasi dengan algoritma yang berbeda.
- Menjelaskan kelebihan dan kekurangan dari solusi/pendekatan yang dipilih.

## Evaluation
Pada bagian ini Anda perlu menyebutkan metrik evaluasi yang digunakan. Kemudian, jelaskan hasil proyek berdasarkan metrik evaluasi tersebut.

Ingatlah, metrik evaluasi yang digunakan harus sesuai dengan konteks data, problem statement, dan solusi yang diinginkan.

**Rubrik/Kriteria Tambahan (Opsional)**: 
- Menjelaskan formula metrik dan bagaimana metrik tersebut bekerja.

**---Ini adalah bagian akhir laporan---**

_Catatan:_
- _Anda dapat menambahkan gambar, kode, atau tabel ke dalam laporan jika diperlukan. Temukan caranya pada contoh dokumen markdown di situs editor [Dillinger](https://dillinger.io/), [Github Guides: Mastering markdown](https://guides.github.com/features/mastering-markdown/), atau sumber lain di internet. Semangat!_
- Jika terdapat penjelasan yang harus menyertakan code snippet, tuliskan dengan sewajarnya. Tidak perlu menuliskan keseluruhan kode project, cukup bagian yang ingin dijelaskan saja.
