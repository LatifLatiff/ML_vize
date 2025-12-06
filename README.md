[README.md](https://github.com/user-attachments/files/23728340/README.md)
#  Student Performance – Overall Score Prediction

Bu proje, 25.000 öğrencinin demografik, akademik ve sosyal özelliklerinden yararlanarak **overall_score** değerinin tahmin edilmesini amaçlayan bir makine öğrenmesi çalışmasıdır. Proje boyunca veri analizi, veri ön işleme, model eğitimi ve model karşılaştırması gibi temel ML adımları uygulanmıştır.

---

##  Projenin Amacı

Öğrencilerin:

- yaş,
- çalışma süresi (study_hours),
- devamsızlık oranı (attendance_percentage),
- okul türü,
- aile eğitim seviyesi,
- internet erişimi,
- ders çalışma yöntemi,
- ek aktivitelere katılım

gibi faktörlere bakarak öğrencinin **overall_score** değerini tahmin etmek.

---

##  Veri Seti Hakkında

- **Örnek sayısı:** 25.000  
- **Sütun sayısı:** 16  
- **Hedef değişken:** `overall_score`

###  Data Leakage Engelleme

Aşağıdaki sütunlar hedef değişkeni doğrudan oluşturduğu için modele dahil edilmemiştir:

- `math_score`  
- `science_score`  
- `english_score`  
- `final_grade`  
- `student_id`

Bu sütunlar çıkarılarak modelin yapay şekilde yüksek performans göstermesinin önüne geçilmiştir.

---

##  Uygulanan Adımlar

### **1. Keşifsel Veri Analizi (EDA)**  
- Eksik veri kontrolü  
- Sayısal değişken dağılımları  
- Kategorik değişken frekans grafikleri  
- Korelasyon analizi  
- Hedef değişken incelemesi  

### **2. Veri Ön İşleme**  
- Gereksiz sütunların kaldırılması  
- Sayısal ve kategorik sütun ayrımı  
- One-Hot Encoding  
- StandardScaler ile ölçekleme  
- Train/Test split (%80 / %20)

### **3. Modelleme**  
Aşağıdaki modeller eğitilmiş ve değerlendirilmiştir:

- Linear Regression  
- Random Forest Regressor  
- KNN Regressor  

Performans metrikleri olarak MAE, RMSE ve R² kullanılmıştır.

---

##  Model Sonuçları

| Model             | MAE   | RMSE  | R² Score |
|-------------------|-------|-------|----------|
| Linear Regression | 5.00  | 5.77  | 0.908    |
| Random Forest     | 3.32  | 4.52  | 0.943    |
| KNN               | 8.98  | 11.16 | 0.656    |

---

##  En İyi Model: Random Forest Regressor

Random Forest modeli tüm metriklerde en iyi performansı göstermiştir:

- En düşük **MAE**  
- En düşük **RMSE**  
- En yüksek **R²** (0.94)

Bu modelin üstün performansı:

- doğrusal olmayan ilişkileri yakalayabilmesi,  
- kategorik + sayısal verilerle iyi çalışması,  
- ensemble yapısı sayesinde overfitting’e karşı dayanıklı olması  

gibi sebeplerle açıklanabilir.

Linear Regression güçlü bir baseline sunarken, KNN modeli bu veri setinde düşük performans göstermiştir.

---
##  Yapılan İşlemler ve Neden Yapıldıkları

Bu bölümde projede uygulanan her adımın **neden gerekli olduğu** detaylı şekilde açıklanmıştır. Böylece makine öğrenmesi sürecinin hem teknik hem de teorik arka planı anlaşılabilir hale gelmektedir.

---

###  1. Gereksiz ve Sızıntı (Leakage) Oluşturan Değişkenlerin Çıkarılması

Modelden çıkarılan değişkenler:
- `math_score`
- `science_score`
- `english_score`
- `final_grade`
- `student_id`

**Neden yapıldı?**

- `math_score`, `science_score` ve `english_score` doğrudan `overall_score` değerinin bileşenleridir. Bu değişkenler modele dahil edilirse model hedefi *gerçek anlamda tahmin etmez*, sadece hesaplar.  
  → Bu duruma **data leakage** denir ve yapay şekilde %100'e yakın doğruluk oluşturur.  
- `final_grade` değişkeni de `overall_score` ile yüksek oranda örtüşmektedir; bu da leakage riskini artırır.  
- `student_id` rastgele atanmış bir kimlik bilgisidir ve öğrenci başarısı hakkında hiçbir bilgi taşımaz.  
  → Bu tür anlamsız değişkenler modeli “gürültü” ile doldurur.

Bu nedenle modele sadece gerçekten tahmin gücü olan açıklayıcı değişkenler bırakılmıştır.

---

###  2. Keşifsel Veri Analizi (EDA)

EDA kapsamında:
- Histogramlar  
- Bar grafikleri  
- Korelasyon matrisi  
- Dağılım incelemeleri  

![](figures/sayisal_d.png)

---
![](figures/kategorik_d.png)

---
![](figures/korelasyon.png)

**Neden yapıldı?**

- Verinin dağılımlarının anlaşılması, outlier olup olmadığını görmemizi sağlar.  
- Kategorik değişkenlerin dengesiz olup olmadığı (ör. gender dağılımı) model performansını doğrudan etkiler.  
- Korelasyon matrisi, hangi değişkenlerin hedefle ilişkili olduğunu görmemizi sağlar.  
  → Örneğin study_hours ve attendance’ın yüksek korelasyona sahip olması, bunların modele mutlaka dahil edilmesi gerektiğini gösterir.  
- Bu adım veri setini “tanıma” aşamasıdır; doğru model kurmanın temelidir.

---

###  3. One-Hot Encoding Uygulanması

Kategorik değişkenler şu şekilde encode edildi:
- `gender`
- `school_type`
- `parent_education`
- `internet_access`
- `travel_time`
- `extra_activities`
- `study_method`

**Neden yapıldı?**

Makine öğrenmesi modelleri doğrudan metin verisiyle çalışamaz.  
Örneğin: school_type = "Public" → Model bunu yorumlayamaz.

Bu tür kategoriler **0/1 şeklinde** sayısal hale getirilir.

One-Hot Encoding:
- Kategoriler arasında yanlışlıkla sayısal bir ilişki kurulmasını engeller.  
- Her kategorinin modele ayrı bilgi olarak verilmesini sağlar.  

Bu işlem olmadan model çalışmaz veya hatalı çalışır.

---

###  4. Ölçekleme (StandardScaler)

Sayısal değişkenler:
- age
- study_hours
- attendance_percentage

**Neden yapıldı?**

Bazı modeller (özellikle KNN ve Linear Regression), farklı ölçeklerdeki değişkenlerden çok etkilenir.  

Örneğin:
- attendance_percentage → 0–100  
- age → 10–25  
- study_hours → 0–10  

Bu aralık farkları modeli yanıltır.

StandardScaler sayesinde:
- Ortalaması 0
- Standart sapması 1

olan düzgün bir veri dağılımı elde edilir.

Bu da model performansını artırır ve kararlı hale getirir.

---

###  5. Train/Test Split (%80 / %20)

**Neden yapıldı?**

- Modelin eğitildiği veri ile test edildiği veri *farklı olmalıdır*.  
- Aynı veri üzerinde test yapılırsa modelin gerçek performansı ölçülemez.  
- %80 eğitim, %20 test oranı ML projeleri için ideal bir dengedir.

---


##  Genel Değerlendirme

- Öğrenci başarısı; çalışma süresi, devamsızlık oranı, aile eğitim düzeyi ve çalışma yöntemi gibi değişkenlerden önemli ölçüde etkilenmektedir.  
- Veri ön işleme adımları, özellikle leakage engelleme ve encoding, model performansını ciddi ölçüde iyileştirmiştir.

- Random Forest, tahmin performansı açısından bu veri seti için en uygun model olmuştur.  
- Elde edilen R² değeri (~0.94), bağımsız değişkenlerin hedef değişkeni güçlü bir şekilde açıkladığını göstermektedir.

---
##  Projeyi Çalıştırma

Bu proje bir Jupyter Notebook içerir ve çalıştırma yöntemi aşağıda verilmiştir.

---

###   Google Colab'da Çalıştırma 

1. Depodaki `.ipynb` dosyasını açın.
2. Sağ üstteki **Open in Colab** butonuna tıklayın.  
   (Buton görünmüyorsa https://colab.research.google.com adresinden notebooku manuel yükleyebilirsiniz.)
3. Notebook'daki ilgili alana ML vize klasörünün içinde bulunan data klasörünün içindeki data dosyasını alın ve dosyanın yolunu yapıştırın.
4. Son olarak notebook'da hücreleri sırasıyla çalıştırın.




