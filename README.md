# Customer Churn Intelligence: End-to-End Predictive Modeling and Risk-Based Segmentation

![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)
![Data-Status](https://img.shields.io/badge/Data-Cleaned%20%26%20Validated-green.svg)
![Machine-Learning](https://img.shields.io/badge/ML-XGBoost%20%7C%20RandomForest%20%7C%20LogReg-orange.svg)
![PowerBI](https://img.shields.io/badge/Power%20BI-Dashboard--Ready-yellow.svg)

## 📌 Project Overview
Proyek ini mengimplementasikan sistem kecerdasan prediktif komprehensif (*end-to-end*) untuk mendeteksi potensi *customer churn* secara dini pada industri telekomunikasi. 

Menggunakan pendekatan berbasis data, proyek ini tidak hanya mengklasifikasikan pelanggan yang akan keluar, melainkan membedah pola perilaku mereka melalui **K-Means Clustering**, merekayasa fitur-fitur baru (*advanced feature engineering*), menguji ketangguhan berbagai algoritma *Machine Learning* dalam menangani data tidak seimbang (*imbalanced data*), hingga mengekspor dataset yang terstruktur dan siap pakai (*dashboard-ready*) untuk kebutuhan visualisasi interaktif di **Power BI**.

---

## 💼 Business Framework

### Business Problem
*Customer churn* merupakan salah satu tantangan finansial terbesar bagi bisnis berbasis langganan (*telecommunication subscription*). Kehilangan pelanggan berdampak langsung pada penurunan stabilitas pendapatan berulang (*recurring revenue*) serta pembengkakan *Customer Acquisition Cost* (CAC), mengingat biaya menarik pelanggan baru jauh lebih mahal daripada mempertahankan pelanggan yang sudah ada. 

Perusahaan membutuhkan strategi retensi proaktif yang mampu mengidentifikasi pelanggan berisiko tinggi sebelum mereka memutus layanan (*early detection*).

### Project Objectives
1. **Identify Key Drivers:** Menemukan faktor fundamental pemicu customer churn menggunakan visualisasi dan uji statistik.
2. **Behavioral Analysis:** Menganalisis pola penggunaan layanan dan pengaruhnya terhadap loyalitas pelanggan.
3. **Risk-Based Segmentation:** Mengelompokkan pelanggan ke dalam segmen risiko (persona bisnis) yang bermakna bagi tim *Marketing*.
4. **Predictive Modeling:** Membangun model *Machine Learning* terbaik yang memprioritaskan nilai *Recall* dan *F1-Score*.
5. **Advanced Feature Engineering:** Memperluas dimensi prediktif data dengan menciptakan indikator risiko dan finansial baru.
6. **Dashboard-Ready Dataset:** Menyusun struktur data akhir yang bersih dan optimal untuk langsung diintegrasikan dengan Power BI Dashboard.

### Business Questions
* Karakteristik finansial dan kontraktual apa yang paling membedakan antara pelanggan yang *churn* dan tetap bertahan?
* Jenis infrastruktur internet dan kombinasi layanan apa yang paling rentan terhadap risiko atrisi?
* Bagaimana cara mengelompokkan pelanggan agar tim retensi dapat memprioritaskan anggaran intervensi secara efisien?
* Langkah strategis apa yang harus diambil perusahaan berdasarkan profil risiko masing-masing kelompok?

---

## 📊 Feature Reference & Data Pipeline

Dataset asli terdiri dari **7,043 baris** dan **21 kolom** yang mencakup profil demografis, kepemilikan layanan, dan metrik finansial pelanggan:

| Kategori Data | Nama Variabel | Deskripsi |
| :--- | :--- | :--- |
| **Demografis** | `customerID`<br>`gender`<br>`SeniorCitizen`<br>`Partner`<br>`Dependents` | Identitas unik, jenis kelamin, status lansia (0/1), status pernikahan, dan memiliki tanggungan finansial atau tidak. |
| **Layanan (Services)** | `PhoneService`<br>`MultipleLines`<br>`InternetService`<br>`OnlineSecurity`<br>`OnlineBackup`<br>`DeviceProtection`<br>`TechSupport`<br>`StreamingTV`<br>`StreamingMovies` | Jenis layanan telepon/internet yang digunakan serta ragam fitur proteksi, bantuan teknis, dan hiburan tambahan yang diaktifkan pelanggan. |
| **Akun & Finansial** | `tenure`<br>`Contract`<br>`PaperlessBilling`<br>`PaymentMethod`<br>`MonthlyCharges`<br>`TotalCharges` | Durasi berlangganan (bulan), jenis kontrak, metode penagihan, cara pembayaran, biaya bulanan, dan total akumulasi biaya. |
| **Target KPI** | `Churn` | Status apakah pelanggan keluar (Yes/1) atau bertahan (No/0). |

---

## 🛠️ Data Engineering & Quality Assessment

Kualitas data dinilai dan dibersihkan secara ketat untuk menjamin validitas model prediktif:
* **Handling Hidden Missing Values:** Menemukan adanya karakter spasi kosong (`""`) pada kolom `TotalCharges`. Karakter tersebut dikonversi secara paksa menjadi nilai numerik (`NaN`).
* **Filtering Logic:** Analisis menunjukkan bahwa data `NaN` pada `TotalCharges` berasal dari pelanggan baru dengan `tenure = 0` (belum mengumpulkan tagihan satu bulan penuh). Karena proporsinya sangat kecil, baris ini dihapus secara aman (`dropna`) sehingga menyisakan **7,032 baris** data berkualitas tinggi.
* **Statistical Validation:** Menggunakan uji **Chi-Square Contingency** untuk memvalidasi signifikansi hubungan antara seluruh variabel kategorikal terhadap status `Churn`. Fitur seperti `Contract` dan `InternetService` terbukti secara statistik memiliki nilai $P-Value < 0.05$ (Sangat Signifikan).

---

## 🧠 Risk-Based Customer Segmentation (K-Means Clustering)

Menggunakan penskalaan data (`StandardScaler`) pada fitur numerik serta reduksi dimensi melalui **Principal Component Analysis (PCA)**, algoritma **K-Means** (dengan *Elbow Method* optimal $K=3$) berhasil mengidentifikasi 3 persona pelanggan yang unik:

1. **Persona 0: Premium Loyal Customers**
   * **Profil:** Masa aktif (*tenure*) panjang, menggunakan banyak kombinasi layanan tambahan (*Value-Added Services*), kontribusi finansial (`TotalCharges`) tertinggi. Tingkat loyalitas sangat kuat.
2. **Persona 1: At-Risk Customers (Prioritas Utama Retensi)**
   * **Profil:** Masa aktif pendek (*new/early customers*), pengeluaran bulanan (`MonthlyCharges`) relatif tinggi, didominasi oleh pengguna kontrak fleksibel *Month-to-month* dan infrastruktur *Fiber Optic*. 
   * **Temuan Bisnis:** Kelompok ini mencatatkan **Churn Rate tertinggi sebesar 44.9%**. Ini membuktikan bahwa churn di perusahaan ini mayoritas merupakan masalah di fase awal siklus hidup pelanggan (*early-stage lifecycle problem*).
3. **Persona 2: Stable Budget Customers**
   * **Profil:** Pengeluaran bulanan rendah, cenderung menggunakan paket internet standar (DSL) atau tanpa internet, namun memiliki tingkat retensi yang stabil.

---

## 🚀 Advanced Feature Engineering & Optimization

Untuk mendongkrak performa model prediktif, diciptakan beberapa fitur turunan yang merepresentasikan interaksi risiko dan finansial:
* `AvgSpendPerMonth`: Rasio pemanfaatan finansial aktual pelanggan, dihitung dari `TotalCharges` / (`tenure` + 1).
* `ServiceCount`: Jumlah total layanan bernilai tambah yang diaktifkan pelanggan (fitur pengaman seperti `OnlineSecurity`, `TechSupport`, dll). Data menunjukkan pelanggan dengan `ServiceCount` rendah cenderung lebih mudah churn.
* `TenureGroup`: Pengelompokan masa aktif menjadi fase siklus (`New`, `Developing`, `Established`, `Loyal`).
* `ContractRisk` & `InternetRisk`: Pemetaan tingkat kerentanan risiko berdasarkan jenis kontrak dan infrastruktur jaringan internet yang digunakan.

---

## 🔬 Predictive Modeling & Imbalance Handling

Dataset ini memiliki tantangan ketidakseimbangan kelas biner (~27% Churn vs ~73% Retained). Evaluasi model sengaja diprioritaskan pada metrik **Recall** dan **F1-Score** guna meminimalkan kesalahan *False Negative* (pelanggan diprediksi bertahan padahal aslinya akan churn).

Proyek ini membangun arsitektur pipeline terstruktur (`ColumnTransformer` & `Pipeline`) untuk menguji 3 algoritma utama dengan berbagai teknik penyeimbangan data (*Class Weight*, *SMOTE*, dan *Random Over Sampler*):

1. **Logistic Regression** (Tuned dengan `class_weight='balanced'` & SMOTE)
2. **Random Forest Classifier** (Tuned dengan kedalaman pohon optimal untuk mencegah overfitting)
3. **XGBoost Classifier** (Optimasi parameter `scale_pos_weight` berdasarkan rasio kelas mayoritas terhadap minoritas)

*Dataset hasil prediksi akhir diekspor kembali ke dalam file `churn_clean.csv` lengkap dengan nilai probabilitas prediktifnya agar siap dianalisis secara visual pada tatap muka Power BI Dashboard.*

---

## 📂 Repository Structure
```text
├── data/
│   ├── WA_Fn-UseC_-Telco-Customer-Churn.csv  # Dataset Mentah (Raw)
│   └── churn_clean.csv                       # Dataset Bersih & Hasil Rekayasa Fitur (Dashboard Ready)
├── notebooks/
│   └── Customer_Churn_Intelligence.ipynb     # File Jupyter Notebook / Google Colab (End-to-End Code)
├── dashboard/
│   └── Churn_Intelligence_Dashboard.pbix    # File Desain Interaktif Power BI Dashboard
├── requirements.txt                          # Daftar Dependensi Library Python
└── README.md                                 # Dokumentasi Proyek
