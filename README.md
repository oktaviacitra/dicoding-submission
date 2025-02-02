# Laporan Proyek Machine Learning - Nama Anda

## Domain Proyek

Cuaca memainkan peran penting dalam kehidupan sehari-hari manusia. Prediksi cuaca yang akurat sangat penting untuk berbagai sektor, mulai dari pertanian, transportasi, hingga perencanaan bencana karena dapat memperkecil dampak yang terjadi akibat keadaan cuaca yang tidak menentu. Dalam beberapa dekade terakhir, kemajuan teknologi informasi dan kemampuan komputasi telah membuka peluang baru untuk meningkatkan akurasi prediksi cuaca melalui penggunaan machine learning [1].

## Business Understanding

### Problem Statements

Berdasarkan latar belakang di atas, rincian masalahnya adalah 
- Bagaimana mengolah data agar bisa masuk ke pemodelan prediksi agar meningkatkan ketepatan perkiraan guna antisipasi jenis cuaca yang akan terjadi?
- Bagaimana membuat sistem prediksi cuaca yang dengan peforma seakurat mungkin (minimal akurasi 85%) agar sedikit kesalahan prediksi yang mengakibatkan biaya antisipasi banyak terbuangb sia-sia ?

### Goals

Untuk menangani rincian masalah di atas, maka tujuan yang saya ajukan adalah membuat sistem prediksi jenis cuaca dengan akurasi minimal 85% sehingga mengerti bagaimana antisipasi yang dilakukan agar tidak menimbulkan masalah lingkungan yang lebih besar.

### Solution statements
Solusi yang dapat dilakukan untuk memenuhi goals proyek ini diantaranya sebagai berikut:
- Membandingkan 7 algoritma sekaligus dalam bentuk tabel yang berisi metrik evaluasi.
- Melakukan hyperparameter tuning terhadap 1 algoritma yang memiliki nilai paling unggul di metrik evaluasi.

## Data Understanding
Dataset yang digunakan pada proyek kali ini dibuat oleh Nikhil Narayan yang di upload ke Kaggle pada Juni 2024. Sumber dataset: [Weather Type Classification](https://www.kaggle.com/datasets/nikhil7280/weather-type-classification). Pada dataset ini terdiri dari 13200 baris dan 11 kolom data. Kondisi khusus dari data:
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

![Outlier data](https://github.com/user-attachments/assets/aacd855c-8103-4fb9-8a11-a7ae4bf0ad6b)


Weather type merupakan variabel yang menjadi target pada proyek ini. Proyek ini menggunakan balanced dataset sehingga hal ini membantu mencegah overfitting pada kelas mayoritas selama pengembangan model machine learning sebab model tidak akan terlalu terfokus pada kelas mayoritas dan mengabaikan kelas minoritas.

![download](https://github.com/user-attachments/assets/7468049f-aaff-4f25-b9e6-d64d8de448ef)


### Multivariate

![corr](https://github.com/user-attachments/assets/6d65021e-ef33-4213-9686-b5b05b6523db)


Berdasarkan heatmap diatas dapat diketahui bahwa

| Kolom | Korelasi | Skor | 
| --- | ----- | ------ | 
| Humidity - Precipitation (%) | Positif | 0.638631 |
| Wind Speed - UV Index | Tidak ada | -0.068147 |
| Humidity - Visibility (km) | Negatif | -0.479969 |

## Data Preparation
Berikut merupakan tahapan-tahapan dalam Data Preparation:
- Mengganti nilai data outliers menggunakan imputasi berbasis [KNN](https://scikit-learn.org/stable/modules/generated/sklearn.impute.KNNImputer.html) yang mengganti dengan nilai terdekatnya

![viloin plot](https://github.com/user-attachments/assets/90cae236-f7ea-49e2-984e-6f54be881ea5)


- Melakukan encoding terhadap variabel-variabel kategorikal

  | Kolom | Encoding | Alasan |
  | --- | ----- | ------ |
  | Location | Label | nilai perlu menunjukkan tingkat dataran |
  | Cloud cover | Label | nilai perlu menunjukkan tingkat kerapatan awan di langit |
  | Season | One hot | nilai menunjukkan tidak ada urutan khusus |
  
- Melakukan normalisasi data ke semua variabel menggunakan [Standar Scaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html) agar menyeragamkan rentang nilai setiap kolom.

![describe](https://github.com/user-attachments/assets/8684b98d-4984-4a25-a9be-9b03744f6617)


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

Tahapan yang dilakukan:
- Melakukan looping for mencari algoritma machine learning dengan parameter default yang memiliki performa paling unggul dalam memprediksi kategori di dataset ini
- Setelah menemukan algoritma machine learning paling unggul, maka algoritma machine learning tersebut akan dimasukkan ke hyperparameter tuning dengan [GridSearchCV](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html) untuk mencari parameter terbaik yang meningkatkan performa model
- Selain melakukan hyperaparemeter tuning untuk mencari parameter terbaik pada algoritma machine learning, dilakukan pencarian fitur terbaik yang paling berperan pada penentuan prediksi menggunakan [SelectKBest](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.SelectKBest.html).
- Seleksi fitur dan pencarian parameter terbaik untuk algoritma machine learning dilakukan secara bersamaan menggunakan [Pipeline](https://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html).

Berikut merupakan penjelasan setiap parameter yang digunakan:
1. Decision Tree
- criterion='gini', teknik pemisahan data untuk kategorisasi, 'gini' sering kali lebih cepat dalam komputasi
- max_depth=None, kedalaman maksimum pohon, karena tidak membatasi kondisi yang terbagi untuk bisa sedetail mungkin
- min_samples_split=2, jumlah minimum sampel yang diperlukan untuk membagi node internal, karena 2 mengindikasi kondisi yang detail dan tidak bisa dibagi lagi.
2. Random Forest
- n_estimators=100, jumlah pohon keputusan dalam hutan, 100 karena angka besar tapi tidak terlalu membebankan waktu komputasi
- max_depth=None, kedalaman maksimum pohon, karena tidak membatasi kondisi yang terbagi untuk bisa sedetail mungkin
- max_features='sqrt', jumlah fitur maksimum yang dipertimbangkan untuk membagi setiap node,  dengan mempertimbangkan hanya akar kuadrat dari total fitur pada setiap split, setiap pohon dalam hutan cenderung melihat subset fitur yang berbeda
3. Support Vector Machine
- C=1.0, pengontrol seberapa banyak kesalahan yang diizinkan dalam model yang mempengaruhi trade-off antara bias dan varians, memberikan keseimbangan yang wajar antara memaksimalkan margin pemisah dan meminimalkan kesalahan klasifikasi
- kernel='rbf', menentukan ruang fitur di mana data akan diproyeksikan untuk menemukan hyperplane yang memisahkan kelas, rbf memiliki mekanisme menangani data yang tidak dapat dipisahkan secara linear dengan memproyeksikannya ke ruang dimensi yang lebih tinggi
- gamma='scale', menentukan seberapa jauh pengaruh dari satu contoh pelatihan, memiliki mekanisme menyesuaikan nilai gamma berdasarkan jumlah fitur dalam data.
4. K Nearest Neighbors
- n_neighbors=5, menentukan jumlah tetangga terdekat yang akan digunakan untuk melakukan prediksi, outlier kurang bisa mempengaruhi hasil prediksi karena jika n_neighbors=1 bisa menyebabkan overfitting.
- metric='minkowski', perhitungan jarak mempengaruhi cara jarak dihitung dan bagaimana kesamaan antara titik data diukur, metrik jarak lebih sensitif terhadap perbedaan dalam skala fitur.
- weights='uniform', menentukan apakah semua tetangga memiliki bobot yang sama atau apakah tetangga yang lebih dekat memiliki pengaruh lebih besar, untuk menghindari overfitting bisa memberikan bobot yang sama terhadap semua tetangga terdekat
5. Gradient Boosting
- max_features=None, jumlah fitur maksimum yang dipertimbangkan untuk membagi setiap node,  untuk tidak membatasi fitue sebanyak-banyak dipertimbangkan dalam membagi setiap node
- learning_rate=0.1, laju pembelajaran, 0.1 agar pembelajaran semakin teliti
- loss='log_loss', mengukur seberapa baik probabilitas yang diprediksi sesuai dengan label kelas yang sebenarnya, diperuntukkan untuk klasifikasi
- max_depth=3, kedalaman maksimum dalam membagi node, karena memelurkan komputasi yang cepat dan analisis model yang general
6. Ada Boost
- learning_rate=0.1, laju pembelajaran, 0.1 agar pembelajaran semakin teliti
- n_estimators=50, jumlah pohon keputusan dalam hutan, berbeda dengan random forest memiliki kinerja lebih cepat, ada boost lebih kompleks sehingga memilih 50 untuk berada di tengah-tengah antara analisis model secara detail dan general
- estimator=None, model dasar, tidak menggunakan model dasar yang lain untuk bisa mempercepat komputasi
7. Extra Trees
- n_estimators=100, jumlah pohon keputusan dalam hutan, 100 karena angka besar tapi tidak terlalu membebankan waktu komputasi
- max_features='sqrt', jumlah fitur maksimum yang dipertimbangkan untuk membagi setiap node,  dengan mempertimbangkan hanya akar kuadrat dari total fitur pada setiap split, setiap pohon dalam hutan cenderung melihat subset fitur yang berbeda
- max_depth=None, kedalaman maksimum pohon, karena tidak membatasi kondisi yang terbagi untuk bisa sedetail mungkin

## Evaluation
Proses evaluasi model pada proyek ini menggunakan 4 metrik berikut ini

| Metrik | Pengertian | Rumus | 
| --- | ----- | ------ | 
| Akurasi | mengukur seberapa sering model prediksi benar secara keseluruhan | $`Jumlah Prediksi Benar / Jumlah Seluruh Prediksi`$ |
| Precision | mengukur seberapa tepat model dalam memprediksi kelas positif | $`True Positive / True Positive + False Positive`$  |
| Recall | mengukur seberapa baik model dalam menemukan semua contoh kelas positif | $`True Positive / True Positive + False Negative`$ |
| F1 Score | mengukur keseimbangan antara precision dan recall | $`2 * ((precision * recall)/(precision+recall))`$ |

Hasil eksperimen semua model:

![table](https://github.com/user-attachments/assets/15baf439-3002-435d-829e-f8a0872fc806)


Gradient boost mendapatkan nilai performa yang unggul dibanding dengan metode lain, sehingga untuk proses peningkatan performa menggunakan hyperparameter tuning, perlu berfokus pada Gradient boost saja. Berikut merupakan parameter-parameter yang dikombinasikan supaya mendapatkan performa terbaik. *berikut merupakan konfigurasi yang digunakan dalam hyperparameter tuning menggunakan GridSearchCV*.

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
| learning_rate | 1.0, 0.1 | GradientBoostingClassifier|
| criterion | 'squared_error' | GradientBoostingClassifier|
| max_features | 'sqrt' | GradientBoostingClassifier|
| loss | 'log_loss' | GradientBoostingClassifier|
| max_depth | 3 | GradientBoostingClassifier|

Setelah menerapkan parameter-parameter tersebut dalam model Gradient Boost, maka diperoleh metrik performa sebagai berikut:

![confusionmatrix](https://github.com/user-attachments/assets/365b2438-6c75-4f2c-bc29-6839243f2423)


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

Jika tidak tertampil gambarnya, mohon dibuka di https://github.com/oktaviacitra/dicoding-submission/blob/main/README.md


**Referensi**
[1] [Rhasyid, D. Y. L. A., Pramudita, B. A., & Istiqomah, I. (2023). Sistem Pemantauan Cuaca Berdasarkan Kecepatan Angin, Suhu dan Kelembaban Udara Berbasis Internet of Things. eProceedings of Engineering, 10(4).](https://openlibrarypublications.telkomuniversity.ac.id/index.php/engineering/article/download/20764/20289) 
**---Ini adalah bagian akhir laporan---**

