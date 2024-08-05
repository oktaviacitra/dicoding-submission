# Laporan Proyek Machine Learning - Oktavia Citra Resmi Rachmawati

## Project Overview

Pada proyek kali ini akan dibangun sistem rekomendasi film yang memberikan hasil berupa daftar film yang memiliki kemiripan (output) dengan karakteristik film yang diberikan (input). Membuat sistem rekomendasi film berdasarkan genres dan content ratings penting karena memungkinkan rekomendasi yang lebih relevan dan sesuai dengan preferensi serta batasan individu pengguna. Genre membantu mengidentifikasi jenis film yang diminati pengguna, seperti komedi, drama, atau aksi, sehingga sistem dapat menawarkan pilihan yang sesuai dengan selera mereka. Sementara itu, content ratings memastikan bahwa rekomendasi film sesuai dengan preferensi usia atau sensitivitas terhadap konten tertentu, menjaga pengalaman menonton yang nyaman dan aman. Dengan mempertimbangkan kedua aspek ini, sistem rekomendasi menjadi lebih akurat, personal, dan bertanggung jawab, meningkatkan kepuasan pengguna secara keseluruhan.

### Background

Seiring berjalannya waktu, ribuan film dirilis di seluruh dunia dari semua platform dan negara, sehingga menemukan film yang cocok menjadi bagian dari kehidupan hiburan dari sejak awal munculnya banyak industri film [1]. Terdapat berbagai genre di industri film yang membuat orang-orang tidak sempat menjelajah banyak genre karena keterbatasan waktu atau biaya [2]. Salah satu fitur umum yang disediakan oleh layanan aplikasi streaming adalah sistem yang menampilkan semua film yang berasal dari beragam tahun dan negara produksi, tidak hanya menampilkan film-film terbaru sehingga pengguna akan tertarik memanfaatka fiturnya[3]. Sistem rekomendasi bermanfaat bagi pelanggan dan perusahaan pemilik aplikasi, karena semakin puas pelanggan, semakin besar kemungkinan pengguna ingin menggunakan sistem untuk kemudahan dalam mengoperasikan aplikasi, sehingga akan berpeluang menambah pendapatan perusahaan [4].

## Business Understanding

Dalam merumuskan proyek ini dibangun penyataan masalah, tujuan, dan pernyataan solusi seperti di bawah ini.

### Problem Statements

Berdasarkan latar belakang di atas, rincian masalahnya adalah **bagaimana memanfaatkan data film agar dapat meningkatkan kepuasan penonton mengembangkan sistem rekomendasi film yang lebih relevan dengan genre kesukaan pengguna?**

### Goals

Untuk menjawab pertanyaan masalah di atas, maka tujuan dari proyek ini adalah **mampu mengembangkan model yang sepresisi mungkin dalam memberikan daftar film sesuai genre yang disukai oleh penonton dengan model yang berbasis dengan konten (content-based filtering) dengan model yang mencapai metrik precision@N 85%**

### Solution statements

Solusi yang dapat dilakukan untuk memenuhi goals proyek ini diantaranya sebagai berikut:
- Mengolah data teks berkaitan dengan informasi film menjadi numerikal yang bisa dihitung nilai kemiripannya
- Menampilkan detail data yang diuji dengan hasilnya untuk validasi kebenaran dari nilai metriknya

## Data Understanding
Dataset yang digunakan pada proyek kali ini dibuat oleh Yueming yang di upload ke Kaggle pada tahun 2017. Sumber dataset: [IMDB 5000 Movie Dataset](https://www.kaggle.com/datasets/carolzhangdc/imdb-5000-movie-dataset). Pada dataset ini terdiri dari 5043 baris dan 28 kolom data. Kondisi khusus dari data:
- memiliki jenis tipe data yang beragam untuk kolom-kolom yang ada diantaranya float64, int64, dan object
- terdapat 6 kolom yang memiliki nilai yang penuh atau tidak memiliki nilai hilang
- terdapat data yang duplikat terutama pada kolom movie_title

Pada proyek ini, hanya melakukan EDA Univariate terhadap dua kolom yang menjadi dependent variable untuk model sistem rekomendasi film menggunakan content based filter berdasarkan genre dan content rating. Karena nilai unik berjumlah sangat banyak di 2 kolom tersebut, maka ditampilkan horizontal bar chart untuk nilai yang memiliki kemunculan paling banyak

|Nama|Chart|Analisis|
|----|-----|--------|
|genres|![genre chart](https://github.com/user-attachments/assets/c0180fd2-9940-4062-bc99-3ad2edc955ff)|Sebagian besar film yang memiliki genre drama|
|rating|![rating chart](https://github.com/user-attachments/assets/16f8b66c-94cf-4d48-ad39-9b128c05de71)|Sebagian besar film yang memiliki rating content R|

Yang dilakukan pada tahap ini diantaranya:
- Melihat ringkasan informasi struktur data dengan fungsi .info()
- Menghapus kolom-kolom yang lainnya selain movie_title, genres, dan content_rating
- Memeriksa jumlah nilai unik pada setiap kolom

## Data Preparation
Pada tahap ini perlu mempersiapkan data agar mudah diproses oleh model, apalagi jika masih mengandung kolom-kolom yang bertipe data object yang perlu dilakukan encoding untuk mengubahnya menjadi numerik supaya dapat dihitung nilai cosine similarity nya.

Karena terdapat 28 kolom, sedangkan pembangun model pada proyek ini menggunakan content-based filter yang menyeleksi data yang mirip berdasarkan isinya, maka perlu dilakukan seleksi kolom dan menghasilkan dataframe yang memiliki 3 kolom penting berikut ini:

|Nama|Jenis|Tipe Data|Alasan|Jumlah Nilai Unik|
|----|-----|---------|------|-----------------|
|movie_title|independent|object|diperlukan untuk mencari film-film yang mirip dan mengembalikan nilai dalam bentuk nama-nama filmnya|4917|
|genres|dependent|object|mewakili representasi dari sebuah film memiliki jalan cerita seperti apa|914|
|content_rating|dependent|object|identifikasi mengenai segmen target penonton film|19|

Berikut merupakan aktifitas lainnya dilakukan pada tahap ini:

|Aktifitas|Alasan|Ukuran dataset semula|Ukuran dataset sesudah preproses|
|---------|------|---------------------|--------------------------------|
|menghapus nilai \xa0 pada kolom movie_title|agar tidak menjadi noise ketika pengguna mau mencari rekomendasi berdasarkan film yang sudah ditontonnya|(5043,3)|(5043,3)|
|menghapus baris duplikat|karena nilai unik pada kolom movie_title sejumlah 4917 sehingga terdapat kemungkinan banyak baris data yang duplikat|(5043,3)|(4919,3)|
|menghapus baris data yang memiliki nilai hilang|tidak bisa dilakukan imputasi karena akan menyebabkan kesalahan analitik|(4919,3)|(4618,3)|
|melakukan one hot encoding terhadap kolom genres|supaya dapat dihitung nilai cosine similaritynya dengan representasi vektornya|(4618,3)|(4618,26)|
|melakukan one hot encoding terhadap kolom content_rating|supaya dapat dihitung nilai cosine similaritynya dengan representasi vektornya|(4618,26)|(4618,43)|

## Modeling
Yang dilakukan pada tahap ini diantaranya:
- Kolom genres dan content_rating telah melalui proses encoding yang membuat bentuk datanya seperti telah melalui proses Binary Count Vectorizer dari modul sklearn. Pada proyek kali ini, tidak perlu menggunakn term-frequency karena dalam satu baris tidak ada perulangan kata, apalagi pada kolom genres, berbeda dengan kolom yang berkonteks paragraf memerlukan term-frequency untuk mengubah kata menjadi vektor.
- Menghitung nilai kemiripan antar baris data film menggunakan cosine similariy lalu menyimpannya dalam bentuk dataframe. jika nilai kemiripan mendekati 1 berarti dua item memiliki banyak kemiripan. nilai kemiripan mendekati 0 berarti dua item tidak memiliki banyak kemiripan. dan nilai kemiripan mendekati -1 berarti 2 item saling berlawanan.
- Membuat fungsi get_recommendation() untuk mengeluarkan daftar nama-nama film yang disertai dengan urutan film yang paling mirip yang memiliki kemiripan dengan nama film yang dimasukkan sebagai parameter. Cara kerja dari fungsi ini adalah yang pertama memanfaatkan fungsi argpartition untuk mengambil sejumlah nilai k tertinggi dari similarity data. Kemudian mengambil data dari tingkat kesamaan tertinggi ke terendah lalu dimasukkan ke dalam variabel closest dan yang terakhir ialah menghapus movie_title yang dicari menggunakan fungsi drop() agar tidak muncul dalam daftar rekomendasi karena sesama nilai akan menghasilkan nilai kesamaan tertinggi yaitu 1. Berikut merupakan penjelasan parameter dari fungsi get_recommendations() adalah sebagai berikut:
|nama|deskripsi|tipe data|
|----|---------|---------|
|movie_title|judul film yang ingin mencari film-film lainnya yang mirip|str|
|similarity_data|dataframe yang berisi nilai cosine similarity setiap film yang ada|object|
|items|dataframe yang asli untuk mendapatkan nilai sebenarnuya|object|
|k|banyaknya jumlah film yang mirip|int|

Berikut hasilnya yang diperoleh:

**Nama film yang dimasukkan**
![input](https://github.com/user-attachments/assets/a7b58aa5-7072-4ebf-bbe1-5c9c7ea5b643)

**Daftar film yang direkomendasikan**
![output](https://github.com/user-attachments/assets/d152f771-0d10-4c0d-a42c-fac5629ebf30)

Berdasarkan pengamatan input dan output diatas menunjukkan bahwa daftar film yang direkomendasikan memiliki genre yang hampir semuanya sama. semakin bawah, semakin sedikit kesamaan nilainya.

## Evaluation
Yang dilakukan pada tahap ini diantaranya:
- menampilkan pengamatan input dan output di sistem rekomendasi dengan membuat fungsi get_indices() untuk mencari letak-letak genre yang dimiliki film yang dimasukkan yang nanti diterapkan pada daftar hasil rekomendasi juga.
- Menghitung performa dengan metrik Top-N Precision yang menilai seberapa baik sistem dalam merekomendasikan item yang relevan dalam daftar teratas (top-N) dari hasil rekomendasi.  $`Precision @ K = Number of Relevant Items in K / Total Number of Items in K`$
  
![top-n precision](https://github.com/user-attachments/assets/2d5cc4b5-2d56-4d48-a94a-fcc09bfc9f4a)

Berdasarkan evaluasi diatas dapat diketahui bahwa posisi teratas akan memang seharusnya memiliki nilai precision@K yang paling tinggi dibanding dengan yang posisi bawah.

Catatan:
- solusi sudah menjawab problem statement karena telah membuat model untuk memberikan daftar film yang mirip dengan film yang dimasukkan
- sudah mencapai goals yang diharapkan karena berhasil membangun model yang memiliki presisi lebih dari 85%
- solusi yang direncakan berdampak pada hasil karena dapat model bisa berjalan dengan cepat dengan penentuan kolom genres dan content_ratings yang telah berbentuk binary count vector

## References
[1] P. Kumar, S. G. Kibriya, Y. Ajay, and N. Ilampiray, “Movie Recommender System Using Machine Learning Algorithms,” Journal of Physics Conference Series, vol. 1916, no. 1, p. 012052, May 2021, doi: 10.1088/1742-6596/1916/1/012052.

[2] G. Ramadhan and E. B. Setiawan, “Collaborative Filtering Recommender System Based on Memory Based in Twitter Using Decision Tree Learning Classification (Case Study: Movie on Netflix),” Nov. 2022, doi: 10.1109/icacnis57039.2022.10055248.

[3] P. P. Adikara, Y. A. Sari, S. Adinugroho, and B. D. Setiawan, “Movie recommender systems using hybrid model based on graphs with co-rated, genre, and closed caption features,” Register Jurnal Ilmiah Teknologi Sistem Informasi, vol. 7, no. 1, p. 31, Jan. 2021, doi: 10.26594/register.v7i1.2081.

[4] M. Gupta, A. Thakkar, N. Aashish, V. Gupta, and D. P. S. Rathore, “Movie Recommender System Using Collaborative Filtering,” 2020 International Conference on Electronics and Sustainable Communication Systems (ICESC), Jul. 2020, doi: 10.1109/icesc48915.2020.9155879.

**---Ini adalah bagian akhir laporan---**


