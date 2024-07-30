# Laporan Proyek Machine Learning - Nama Anda

## Domain Proyek

Cuaca memainkan peran penting dalam kehidupan sehari-hari manusia. Prediksi cuaca yang akurat sangat penting untuk berbagai sektor, mulai dari pertanian, transportasi, hingga perencanaan bencana karena dapat memperkecil dampak yang terjadi akibat keadaan cuaca yang tidak menentu. Dalam beberapa dekade terakhir, kemajuan teknologi informasi dan kemampuan komputasi telah membuka peluang baru untuk meningkatkan akurasi prediksi cuaca melalui penggunaan machine learning.

[Sistem Pemantauan Cuaca Berdasarkan Kecepatan Angin, Suhu dan Kelembaban Udara Berbasis Internet of Things](https://openlibrarypublications.telkomuniversity.ac.id/index.php/engineering/article/download/20764/20289) 

## Business Understanding

### Problem Statements

Berdasarkan latar belakang di atas, rincian masalahnya adalah diperlukan sistem model prediksi jenis cuaca yang akan datang dengan setepat mungkin.

### Goals

Untuk menangani rincian masalah di atas, maka tujuan yang saya ajukan adalah membuat sistem prediksi jenis cuaca dengan akurasi minimal 85% sehingga mengerti bagaimana antisipasi yang dilakukan agar tidak menimbulkan masalah lingkungan yang lebih besar.

### Solution statements
Solusi yang dapat dilakukan untuk memenuhi goals proyek ini diantaranya sebagai berikut:
- Membandingkan 7 algoritma sekaligus dalam bentuk tabel yang berisi metrik evaluasi.
- Melakukan hyperparameter tuning terhadap 1 algoritma yang memiliki nilai paling unggul di metrik evaluasi.

## Data Understanding
Dataset yang digunakan pada proyek kali ini dibuat oleh Nikhil Narayan yang di upload ke Kaggle pada Juni 2024. Sumber dataset: [Weather Type Classification](https://archive.ics.uci.edu/ml/datasets/Restaurant+%26+consumer+data](https://www.kaggle.com/datasets/nikhil7280/weather-type-classification)). Pada dataset ini terdiri dari 13200 baris dan 10 kolom data. Kondisi khusus dari data:
- Data tidak memiliki baris atau kolom yang nilai hilang
- Data tidak memiliki baris yang terduplikasi

### Variabel-variabel pada dataset adalah sebagai berikut:
| Nama | Jenis | Keterangan| Variabel |
| --- | ----- | ------ | ------ |
| Temperature | Numerik Kontinu | Suhu dalam derajat Celsius | Dependent |
| Humidity | Numerik Diskrit | Persentase kelembapan | Dependent |
| Wind Speed | Numerik Kontinu | Kecepatan angin dalam kilometer per jam | Dependent |
| Precipitation | Numerik Diskrit | Persentase curah hujan | Dependent |
| Cloud Cover | Kategorikal Ordinal | Kondisi awan | Dependent |
| Atmospheric Pressure | Numerik Kontinu | Tekanan atmosfer dalam hPa | Dependent |
| UV Index | Numerik Diskrit | Kekuatan radiasi ultraviolet | Dependent |
| Season | Kategorikal Nominal | Kondisi musim | Dependent |
| Visibility | Numerik Kontinu | Jarak maksimum di mana objek dapat terlihat dan dikenali dengan mata telanjang | Dependent |
| Location | Kategorikal Ordinal | Jenis lokasi | Dependent |
| Weather Type | Kategorikal Nominal | Jenis cuaca | Independent |

### Exploratory Data Analysis
### Univariate
Menggunakan 1.5xIQR rule, ditemukan 4 variabel mengandung outlier data, diantaranya:

[Gambar Violion Plot yang Menunjukan Outlier](https://drive.google.com/file/d/1xwln9EgAaJpI60pvvMb8PZQOlJM8WpBy/view?usp=sharing)

Weather type merupakan variabel yang menjadi target pada proyek ini. Proyek ini menggunakan balanced dataset sehingga hal ini membantu mencegah overfitting pada kelas mayoritas selama pengembangan model machine learning sebab model tidak akan terlalu terfokus pada kelas mayoritas dan mengabaikan kelas minoritas.

[weather type](https://drive.google.com/file/d/1t4Qz7rqCpnAgxthHpp9p5kajBmn4pPZn/view?usp=sharing)

### Multivariate

[correlation matrixx](https://drive.google.com/file/d/14tpZL0fqTtIYWL93APBp3NN9QXopLLCz/view?usp=sharing)

Berdasarkan heatmap diatas dapat diketahui bahwa

| Kolom | Korelasi | Skor | 
| --- | ----- | ------ | 
| Humidity - Precipitation (%) | Positif | 0.638631 |
| Wind Speed - UV Index | Tidak ada | -0.068147 |
| Humidity - Visibility (km) | Negatif | -0.479969 |

## Data Preparation
Berikut merupakan tahapan-tahapan dalam Data Preparation:
- Mengganti nilai data outliers menggunakan imputasi berbasis [KNN](https://scikit-learn.org/stable/modules/generated/sklearn.impute.KNNImputer.html) yang mengganti dengan nilai terdekatnya
  
  [Violion plot setelah KNN Imputation](https://drive.google.com/file/d/1_x0EhA8N6ntewuPMDy3nIiuWpOrKiXR9/view?usp=sharing)

- Melakukan encoding terhadap variabel-variabel kategorikal

  | Kolom | Encoding | Alasan |
  | --- | ----- | ------ |
  | Location | Label | nilai perlu menunjukkan tingkat dataran |
  | Cloud cover | Label | nilai perlu menunjukkan tingkat kerapatan awan di langit |
  | Season | One hot | nilai menunjukkan tidak ada urutan khusus |
  
- Melakukan normalisasi data ke semua variabel menggunakan [Standar Scaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html) agar menyeragamkan rentang nilai setiap kolom.

  <img width="1522" alt="Screenshot 2024-07-21 at 00 04 58" src="https://github.com/user-attachments/assets/3f67f838-7198-4bc0-96af-8fdd8472c60c">

- Memisahkan data menjadi dua jenis menggunakan [Train Test Split](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html).

  | Jenis | Persentase | Jumlah Baris |
  | --- | ----- | ------ |
  | Train | 90% | 11880 |
  | Test | 10% | 1320 |

## Modeling
Setelah data siap diproses lebih lanjut, maka akan dilanjutkan pada memilih metode terbaik untuk dapat memprediksi cuaca seakurat mungkin dengan bereksperimen menggunakan 7 metode berikut ini:

| Nama | Kelebihan | Kekurangan |
| --- | ----- | ------ |
| Decision Tree | Mampu menangani hubungan non-linear antara fitur dan target | Struktur menjadi kompleks dan lambat untuk dibangun jika menangani data besar |
| Random Forest | Mengurangi varians dan meningkatkan generalisasi model dengan menggabungkan prediksi dari banyak pohon | Membutuhkan banyak memori karena menghasilkan banyak decision tree | 
| Support Vector Machine | Memiliki fleksibilitas dalam menangani data kompleks karena penggunaan kernel tricks | Kurang optimal pada dataset yang tidak seimbang karena cenderung fokus pada margin yang memaksimalkan kelas mayoritas | 
| K Nearest Neighbors	 | dapat menangani data multikelas tanpa perlu modifikasi khusus | mudah terpengaruh oleh data yang noisy dan outliers yang dapat mengurangi akurasi model |
| Gradient Boosting | Menghasilkan estimasi pentingnya fitur selama masa pembelajaran | Sensitif terhadap fitur-fitur yang tidak berkorelasi. |
| Ada Boost | Mudah beradaptasi dengan data baru dan berubah dari waktu ke waktu karena sifatnya yang iteratif | sensitif terhadap noise dan outliers karena mempengaruhi pemberian bobot yang tidak sesuai pada iterasi berikutnya| 
| Extra Trees | lebih cepat dalam pelatihan karena membagi node berdasarkan split points yang dipilih secara acak tanpa melakukan pencarian split optimal | kurang optimal pada dataset yang tidak seimbang tanpa penyesuaian tambahan | 

Setelah memasukkan data yang telah diproses ke metode-metode tersebut dengan parameter default, maka yang dilakukan selanjutnya adalah membuat [Pipeline](https://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html) untuk diproses dengan [GridSearchCV](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html) agar bisa memilih berapa banyak fitur terbaik menggunakan [SelectKBest](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.SelectKBest.html) dan mendapatkan parameter yang menghasilkan performa terbaik pada modelnya.

## Evaluation
Proses evaluasi model pada proyek ini menggunakan 4 metrik berikut ini

| Metrik | Pengertian | Rumus | 
| --- | ----- | ------ | 
| Akurasi | mengukur seberapa sering model prediksi benar secara keseluruhan | $`Jumlah Prediksi Benar / Jumlah Seluruh Prediksi`$ |
| Precision | mengukur seberapa tepat model dalam memprediksi kelas positif | $`True Positive / True Positive + False Positive`$  |
| Recall | mengukur seberapa baik model dalam menemukan semua contoh kelas positif | $`True Positive / True Positive + False Negative`$ |
| F1 Score | mengukur keseimbangan antara precision dan recall | $`2 * ((precision * recall)/(precision+recall))`$ |

Hasil eksperimen semua model:

<img width="521" alt="Screenshot 2024-07-21 at 01 46 22" src="https://github.com/user-attachments/assets/90f57a0a-e752-46c5-a2d4-e2e3d08550ae">

Gradient boost mendapatkan nilai performa yang unggul dibanding dengan metode lain, sehingga untuk proses peningkatan performa menggunakan hyperparameter tuning, perlu berfokus pada Gradient boost saja. Berikut merupakan parameter-parameter yang dikombinasikan supaya mendapatkan performa terbaik.

| Parameter | Nilai | Modul |
| --- | ----- | ------ |
| k | 10, 11, 12, 13 | SelectKBest |
| learning_rate | 1.0, 0.1 | GradientBoostingClassifier|
| criterion | 'friedman_mse', 'squared_error' | GradientBoostingClassifier|
| max_features | 'sqrt', 'log2' | GradientBoostingClassifier|
| loss | 'log_loss', 'exponential' | GradientBoostingClassifier|
| max_depth | 3, 4 | GradientBoostingClassifier|

Setelah mengkombinasikan parameter-parameter yang ada sebanyak 288 kali, maka diperoleh parameter sebagai berikut

| Parameter | Nilai | Modul |
| --- | ----- | ------ |
| k | 12 ('Temperature', 'Humidity', 'Wind Speed', 'Precipitation (%)', 'Cloud Cover', 'Atmospheric Pressure', 'UV Index', 'Visibility (km)', 'Location', 'Spring', 'Summer', 'Winter'], dtype='object') | SelectKBest |
| learning_rate | 0.1 | GradientBoostingClassifier|
| criterion | 'squared_error' | GradientBoostingClassifier|
| max_features | 'sqrt' | GradientBoostingClassifier|
| loss | 'log_loss' | GradientBoostingClassifier|
| max_depth | 3 | GradientBoostingClassifier|

Setelah menerapkan parameter-parameter tersebut dalam model Gradient Boost, maka diperoleh metrik performa sebagai berikut:
[Confusion Matrix](https://drive.google.com/file/d/1oDnaCtUkwUgS_CrOq1_ERHSIXWZREVrC/view?usp=sharing)

Pada proyek ini, metrik performa menggunakan rata-rata **micro** karena ingin mengetahui performa secara global dan general saja. Berikut merupakan perbandingan sebelum dan sesudah dilakukan hyperparameter tuning terhadap model Gradient Boost.

| Metrik | Sebelum | Skor | 
| --- | ----- | ------ | 
| Accuracy | 0.901515 | 0.902272 |
| Precision | 0.901515 | 0.902272 |
| Recall | 0.901515 | 0.902272 |
| F1 Score | 0.901515 | 0.902272 |

Proyek ini menggunakan balanced dataset sehingga metrik performa menunjukkan nilai yang sama semua. Untuk perbandingan sebelum dan sesudah hyperparameter bisa disimpulkan bahwa peningkatan performa tidak signifikan karena hanya bertambah 0,000757 saja.

**Catatan:**
- solusi sudah menjawab problem statement karena telah membuat model untuk memprediksi jenis cuaca yang akan datang berdasarkan data yang ada
- sudah mencapai goals yang diharapkan karena berhasil membangun model yang memiliki akurasi lebih dari 85%
- solusi yang direncakan berdampak pada hasil karena dapat mengetahui mana model yang paling maksimal untuk dataset ini dalam tugas prediksi kategorikal

**---Ini adalah bagian akhir laporan---**

