Film Tavsiye Sistemi Part-2: Matris Faktörizasyon

Bu projede, Matris Faktörizasyon (MFAdvanced) modeli kullanarak kullanıcılar için film derecelerini tahmin eden bir film tavsiye sistemi oluşturacağız. Burada ilk projeye ek olarak modelimize kullanıcı ve öğe önyargısı, ofset, init ve sigmoid aralığı uygulayacağız. Aynı zamanda modelimizin performansını ölçmek için F1, Recall , Precision metrikleri kullanacağız.



Özellikler

Matris Faktörizasyon kullanılarak iş birliğine dayalı filtreleme

PyTorch kullanımı

Kategorik özellikler için Label Encoding ile veri ön işleme

Model değerlendirmesi için eğitim-doğrulama ayrımı

Dataset ve DataLoader oluşturma

Model ve eğitim için konfigürasyon dosyasının tanımlanması (CFG)

Model oluşturma

Modelin eğitilmesi

Ortalama Kare Hata (MSE) kaybı ile model değerlendirmesi

Model başarısını ölçmek için metrik kullanılması

Eğitim ve doğrulama kayıplarının görselleştirilmesi

Sonuçların Kontrol Edilmesi



Kullanılacak dosyalar:

movies.csv: Film detayları (ör. film Id'leri, isimleri ve türleri)

ratings.csv: Kullanıcıların filmlere verdikleri puanlar





Adımlar


Veri Yükleme ve Ön İşleme

movies.csv ve ratings.csv dosyalarını yüklüyoruz.
Ardından movieId sütununa göre iki dosyayı birleştiriyoruz.
Daha sonra verileri userId ve timestamp'e göre sıralıyoruz.
Label Encoding kullanarak userId ve movieId sütunlarını sayısal indekslere dönüştürüyoruz.Yani userId ve movieId'nin benzersiz değerlerine ulaşırız.Bu bellek kullanımını azaltır ve sinir ağlarının gömme katmanları için giriş boyutunu küçültür.




Model değerlendirmesi için veri setinin ayrılması

Veri setinde 610 benzersiz kullanıcı olduğu ve her kullanıcının en az 20 filmi puanladıklarını biliyoruz. Kullanıcı kimliğine göre gruplar oluşturuyoruz. Kullanıcıların son 5 puanlaması hariç tamamını eğitim setine dahil ediyoruz. Genellikle eğitim ve test verisini %80'e %20 bölmek daha iyi olabilir.

Dataset ve DataLoader
Dataset:

Kullanıcı-film-puan verilerini hazırlamak için özelleştirilmiş bir PyTorch Dataset sınıfı tanımlıyoruz. Kullanıcıların (userId), filmlerin (movieId) ve bu filmlere verdikleri puanların olduğu bir veri kümesini alıyoruz. Bu verileri model eğitiminde kullanılabilir hale getiriyoruz.

DataLoader:

DataLoader, genellikle PyTorch kütüphanesinde kullanılan bir veri yükleme aracıdır. Veri kümesini işlerken modeli eğitmek ve test etmek için verileri küçük parçalara (mini-batch) böler, karıştırılmasını (shuffle) ve işlemeye uygun bir şekilde yüklenmesini sağlar.




Sigmoid Aralığı Tanımlama 

Sigmoid fonksiyonun çıkış değerlerini (low,high) aralığına ölçekliyoruz.
Filmimizin puan aralığı 0 ile 5 arasında olduğu için 0 ile 5.5 aralığına ölçekliyoruz.
5.5 aralığına ölçeklememizin sebebi bilimsel olarak modelin daha iyi tahminde bulunmasını sağlamasıdır.






Model Tanımı

PyTorch'un nn.Module sınıfını kullanarak Matris Faktörizasyon (MFAdvanced) modelini tanımlıyoruz. Bu sınıf modelin öğrenilebilir parametrelerini ve ileri geçiş (forward) işlemini tanımlar.
Modelin katmanlarını ve parametrelerini tanımlıyor. Kullanıcılar ve filmler için için gömme (embedding) tablosu oluşturacak ve her kullanıcı ve film embedding_dim boyutunda bir vektörle temsil edilecek.
Ve bazı ek özellikler tanımlayacağız.
Kullanıcı ve öğe önyargılarını dahil edeceğiz.
Çıkışı belirlediğimiz aralık içinde sınırlandıracağız.

Modelin kullanıcılar ve filmler için ne kadar embeddings matrisi oluşturması gerektiği belirtiyoruz.(n_users, n_items)
Modelin parametrelerini tanımlıyoruz.





Yuvarlama

Round_to_0p5 fonksiyonu, verilen bir sayı listesindeki her sayıyı en yakın 0.5 'e yuvarlar.





Eğitim

Matrix Factorization modelini PyTorch kullanarak bir veri kümesi üzerinde eğitmek ve doğrulamak için bir eğitim döngüsü tanımlıyoruz..




Loss ve Metriklerin hesaplanması

Eğitim sırasında sadece kayıp değerleri (Loss) inceliyoruz.
Buna ek olarak metrik hesaplayacağız.
Ancak metrikleri hesaplamak için doğrulama veri setinin (val) tahminlerini (lpreds) ve gerçek değerlerini (lratings) bir araya getirmemiz gerekir.





Sonuçların kontrolü

Kullanıcı ve filmlerin gömme ağırlıklarının min/max değerleri 
Tahminlerin min/max değerleri 
Gerçek puanların min/max değerleri
Sonuçlarımızı kontrol ettiğimizde ek iyileştirmeler sağlayarak modelin tahmin aralığı  [-0.88 , 5.23 ] aralığına çekmiş bulunmaktayız. 

Dilara KAYAOĞLU