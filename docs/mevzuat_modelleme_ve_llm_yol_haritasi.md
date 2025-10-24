# Mevzuat Modelleme ve LLM Yol Haritası

> **Bu doküman kimin için?** Hukuk, iş geliştirme ve yazılım ekiplerinin aynı resmi görebilmesi için hazırlandı. Teknik detayları saklı tutarken yöneticilerin “Bu proje neden önemli, hangi adımlar bize ne kazandıracak?” sorularına net yanıt verir.  
> **Ana hedef:** Bugün zaten tespit ettiğimiz “yeminli mali müşavirlerin mevzuat değişikliklerini atlaması nedeniyle şirketlerin kaybettiği teşvikleri geri kazanma” işini ölçeklenebilir hale getirmek. Mevzuatı önce güvenilir bir karar motoru şeklinde modelliyor, ardından bu sağlam veriyi kullanarak arama (RAG), danışman Ajanlar ve gerekirse ince ayarlı dil modelleri ile zenginleştirilmiş yeni hizmetler kuruyoruz.

---

## 1. Hızlı Bakış: Lehe Kazanım Yolculuğu

| Faz | Ne Yapıyoruz? | İş Etkisi | Teknik Not |
| --- | --- | --- | --- |
| 1. Statik Model | Mevzuat maddelerini kod haline getiriyoruz; tüm hesaplamalar denetlenebilir. | Atlanan teşvik ve vergi avantajlarını hızlıca saptayıp “hak kaybını” gösteriyoruz; “Teşviklerden doğru oranda faydalanılmış mı?” sorusuna net cevap veriyoruz. | OpenFisca değişkenleri, YAML parametreler, test setleri |
| 2. Bilgi Tabanı & RAG | Ürettiğimiz sonuçları ve mevzuat referanslarını arama motoruna dönüştürüyoruz. | Müşteriye ya da iç ekibe “bu hak hangi maddeye dayanıyor?” sorusuna saniyede yanıt. | Vektör arama + doküman ETL + kalite metrikleri |
| 3. Agent & LLM | Hesaplama motoru + mevzuat aramasını kullanan akıllı asistanlar geliştiriyoruz. | Teşvik fırsatlarını proaktif öneren, mevzuat değişikliği uyarısı veren otomasyon. | Agent orkestrasyonu, LLM ince ayarı, üretim izleme |

**Özet:** Şu anda manuel emekle tespit ettiğimiz hak kayıplarını otomatikleştiriyor, ölçekli; daha açık bir ifadeyle yüzlerce müşteri/veri setini aynı doğrulukla yönetebilen bir servis haline getiriyoruz. 

İlk faz, doğruluğu ispatlanmış bir “hesaplama çekirdeği” kurarak sonraki fazlardaki yapay zekâ çözümlerini besleyecek, ona ihtiyacı olduğu ölçüde sürekli olarak veri girişi sağlayacak doğal bir güven zemini sağlar; böylelikle her faz birbiri üzerine inşa edilir.
---

## 2. Kavramlar Sözlüğü (Karma Dil)

- **OpenFisca:** Mevzuat kurallarını yazılıma çeviren açık kaynak motor. Bir formül değiştiğinde sonuçlar otomatik güncellenir.  
  _Teknik not:_ Python tabanlı; değişken ve parametre dosyalarıyla çalışır.
- **Parametre (YAML):** “Şu tarihlerde şu oranlar geçerli” gibi bilgileri tutan tablolar.  
  _Yönetsel bakış:_ Kim, hangi tarihte hangi değişikliği yaptı açıkça görülür.
- **Trace:** Motorun yaptığı her hesap adımının logu.  
  _Yönetsel bakış:_ Denetim geldiğinde “Bu karar hangi maddeye dayanıyor?” sorusunun cevabı.
- **RAG (Retrieval-Augmented Generation):** Önce doğru mevzuat pasajını bulup sonra LLM’e başvurmak.  
  _Yönetsel bakış:_ Yapay zekâ yanıt verirken “kaynağı” da beraber getirir.
- **Agent:** Soruyu anlayıp doğru adımları (hesaplama, arama, raporlama) sırayla yapan otomasyon.  
  _Teknik not:_ OpenFisca API + RAG araması gibi araçları adım adım çağırır.
- **Fine-tuning:** Dil modelini kendi kurumumuzun diline ve mevzuatına göre eğitmek.  
  _Yönetsel bakış:_ Genel amaçlı modelleri bizim jargonumuza uyarlamak.

---

## 3. Neden Önemli? 

<details>
<summary><strong>Hak kaybı önleme</strong></summary>

**Hak kaybı önleme:** Mevzuat değişikliklerini kaçıran YMM süreçleri yüzünden şirketlerin elinden kaçan teşvikleri sistematik şekilde geri kazandırırız. Hukukun en temel ve teknik konularından biri olan lehe kazanım kavramını, bu aşamada gerçekleştirdiğimiz çalışmayla yalnızca 2012/3305 için değil bundan sonra modelleyeceğimiz her nevi mevzuat açısından değerlendireceğimiz bir tecrübe alanı yaratmış oluruz.  
</details>

<details>
<summary><strong>Hız</strong></summary>

**Hız:** Mevzuat değiştiğinde manuel rapor beklemek yerine aynı gün güncelleme ve geri dönüş sağlarız. Projenin statik aşaması açısından hız kaygımız olmaz, bize ulaşmış bir teşvik belgesinin içindeki unsurları kendi kural motorlarımızla saniyeler içerisinde simule edilir, raporlanması sağlanır.
</details>

<details>
<summary><strong>Şeffaflık</strong></summary>

**Şeffaflık:** Her hesaplama mevzuat referansı ve sistem loguyla desteklenir; denetim yükünü azaltır. Simule edilmiş her teşvik belgesinin doğruluğu, aşağıda detaylandırılacak olan trace ve log mekanizmalarıyla analiz edilir. Tespit edilen lehe kazanımlara giden yol, kural motorunda takip edilen adımlar ve neden söz konusu kuralların takip edildiği gözleme ve analize açık olur.
</details>

<details>
<summary><strong>Ölçeklenebilirlik</strong></summary>

**Ölçeklenebilirlik:** Aynı motoru farklı programlara (KOSGEB, TUBITAK, AB teşvikleri) uyarlamak kolaylaşır. Gerektiğinde farklı mevzuatlar için oluşturacağımız veri yapılarını modulerleştirilmiş kural motoruyla oluşturabiliriz. Öte yandan ekibin, 2012/3305 modellemesiyle edindiği tecrübe sayesinde oluşturacağı çalışma prensipleri, buradan elde ettiği veriler sayesinde besleyeceği yapay zeka sisteminin kullanılmasıyla diğer mevzuatları/teşvikleri modellemek daha hızlı olur. 
</details>

<details>
<summary><strong>Bilgi Sürdürülebilirliği</strong></summary>

**Bilgi Sürdürülebilirliği:** Uzman bilgisi dijital hale gelir; kurum hafızası korunur. Oluşturulan sistemin her yapı taşı ekip tarafından dökumente edileceğinden tıpkı açık kaynak olarak çalışan bir sistem gibi, yalnızca kendi içimizde kullanacağımız şematik öğrenme ve geri bildirim prensipleriyle IT ile business tarafı arasında hem bir "anayasa" hem de bir "köprü" kurulur.
</details>

<details>
<summary><strong>AI Hazırlığı</strong></summary>

**AI Hazırlığı:** Faz 1’de üretilen güvenilir veri, faz 2 ve 3’teki yapay zekâ projelerinin “yakıtı” olur. Bilindiği üzere en hafif yapay zekanın 8 ila 10 milyar arası parametresi mevcuttur. Yapay zeka eğitmek / fine tune etmek / deep learning sağlamak milyarlarca parametrenin arasında hedeflenen "idare hukukçusu" LLM'i yaratmak büyük veri kümeleri ile, simule edilmiş somut olay kurguları ile mümkündür. Bu sistemde, 12.000'den fazla sektör kodunun, modellenmiş mevzuatın yüzlerce parametre ve değişkeni iterasyona sokulduğunda ekibin elinde Türkiye'de daha önce bu denli spesifik bir konuda simule edilmemiş kadar hukuki "hazine" bulunmuş olur. Bu veri kümeleriyle eğitilen yapay zeka ile bir sonraki mevzuat sistemleri için yeni stratejik araçlar sağlanır.
</details>

---

## 4. Faz 1 – Statik Mevzuat Modelleme (6 Hafta)

### 4.1 Ne Yapıyoruz?
- YMM süreçlerinde gözden kaçan lehe hükümleri saptayabilmek için mevzuatı madde madde çıkarıyor, her maddeyi “kodda karşılığı olan” kurala çeviriyoruz.
- Parametre dosyalarıyla oranları, tutarları, tarihleri saklıyoruz.
- Test senaryoları ile kuralın doğru çalıştığını otomatik kanıtlıyoruz.
- Trace kayıtları ile kararın dayandığı maddeyi gösteriyoruz.

### 4.2 Yönetsel Kazanımlar
- Her teşvik ve şartın “tek doğru kaynağı” oluşur.
- Hukuk onay süreci dijital izle desteklenir (kim onayladı, hangi belge referans alındı).
- İş birimleri kendi senaryolarını çalıştırıp anlık çıktı alabilir.

### 4.3 Teknik Notlar (Özet Dil)
- **Dosya yapısı:** `parameters/` (YAML), `variables/` (Python), `tests/` (senaryolar).  
- **Kodlama prensibi:** Bir değişken tek bir sorumluluk taşır; gerekirse alt bileşenlere ayrılır.  
- **Resolver mantığı:** İl kodu, sektör kodu gibi verileri parametre tablolarına eşler.  
- **Test paketi:** `openfisca test` + `pytest`; hem olumlu hem olumsuz senaryoları içerir.

### 4.4 Kontrol Listesi
```
[ ] Parametre dosyasında referans mevzuat ve onay sahibi yazıyor
[ ] Kritik tüm senaryolar (hak kazanır / kazanmaz) test edildi
[ ] Trace örnekleri audit klasörüne kaydedildi
[ ] Hukuk ve teknik ekip çift kontrol yaptı
[ ] CI pipeline (otomatik test) yeşil
```

---

## 5. Faz 2 – Bilgi Tabanı ve RAG Hazırlığı (4 Hafta)

### 5.1 Ne Yapıyoruz?
- Faz 1’den çıkan güvenilir verileri, aranabilir bir bilgi tabanına dönüştürüyoruz.
- Mevzuat metinleri, parametre açıklamaları, trace logları tek formatta toplanıyor.
- Vektör arama ve anahtar kelime araması ile “soru → doğru paragraf” eşleşmesi kuruluyor.

### 5.2 Yönetsel Kazanımlar
- Uzman beklemeden mevzuat sorularına yanıt verebilen self-servis ekranlar.
- Bilgi kirliliği azalır; aynı soruya herkes aynı sayfayı referans gösterir.
- Raporlama ve denetim taleplerinde yanıt süresi düşer.

### 5.3 Teknik Notlar (Özet Dil)
- **ETL süreci:** Parametre dosyası değişince otomatik devreye giren veri aktarımı.  
- **Standart kayıt yapısı:** Her bilgi parçası için `source_type`, `effective_date`, `regulation_id`.  
- **RAG mimarisi:** Vektör veritabanı (Milvus/Qdrant vb.) + metin araması (Elasticsearch) hibrit.  
- **Kalite ölçümü:** Golden set (uzman doğruladığı sorular) ile recall ve doğruluk izlenir.

### 5.4 Kontrol Listesi
```
[ ] Mevzuat, parametre ve trace kayıtları aynı şemada tutuluyor
[ ] ETL günlük olarak başarılı şekilde koşuyor
[ ] Golden set sonuçları raporlandı (geri çağırma ≥ %85)
[ ] Gizlilik taraması yapıldı (kişisel veri yok)
[ ] Arama sonuçları mevzuat referansını gösteriyor
```

---

## 6. Faz 3 – Agentik Otomasyon ve LLM (6 Hafta)

### 6.1 Ne Yapıyoruz?
- Kullanıcı, chatbot/portal üzerinden soru soruyor.
- Ajan soru tipini anlıyor; gerekirse OpenFisca hesaplaması yapıyor.
- RAG’den mevzuat paragrafını çekiyor, sonucu ve referansı tek yanıtta birleştiriyor.
- Gerekirse kurum verisiyle ince ayar (fine-tuning) yaparak modelin dilini bize uyduruyoruz.

### 6.2 Yönetsel Kazanımlar
- Müşteri veya iç ekip sorularına “referanslı ve hesaplanmış” cevaplar saniyeler içinde döner.
- İnsan onayı gerektiren kritik cevaplarda yöneticiyi uyarır; risk kontrolü güçlenir.
- Proaktif rapor ve uyarı sistemi (agent) ile mevzuat değişiklikleri anında bildirilir.

### 6.3 Teknik Notlar (Özet Dil)
- **Grounded AI:** LLM çıktısı, mutlaka OpenFisca sonucu + RAG kaynağı ile desteklenir.  
- **Tool zinciri:** OpenFisca API, RAG araması, doküman üretici, insan onayı modülü.  
- **Fine-tuning:** Kurumun onaylı soru-cevapları ile model eğitilir; PEFT/LoRA gibi hafif yöntemlerle maliyet düşürülür.  
- **İzleme:** Yanıt doğruluğu, gecikme, başarısız tool çağrıları dashboard’da takip edilir.

### 6.4 Kontrol Listesi
```
[ ] Agent senaryoları için insan-onay eşiği tanımlandı
[ ] Her yanıt kaynak gösteriyor (knowledge_id, mevzuat linki)
[ ] LLM değerlendirme raporu (hallucinasyon < %2) hazır
[ ] Rollback planı ve eski modele dönüş testi yapıldı
[ ] Operasyon logları KVKK/GDPR gerekliliklerine uygun
```

---

## 7. Veri Yönetişimi & Güvenlik

- **KVK/GDPR uyumu:** Girişlerde kişisel veri varsa maskelenir; üretim loglarında tutulmaz.
- **Yetkilendirme:** Parametre dosyalarını sadece yetkili ekip değiştirebilir; onay kaydı zorunlu.
- **Denetim izi:** Her hesaplama için trace ve kullanılan mevzuat parçası saklanır.
- **Çeviklik + kontrol:** Değişiklikler önce test ortamında denenir, feature flag ile canlıya alınır.
- **Alarm sistemi:** Yanıt kalitesi düşerse veya hatalı veri tespit edilirse yöneticilere uyarı gider.

---

## 8. Roller ve İş Bölümü

- **Mevzuat Analisti:** Metinleri yorumlar, parametrelerin hukuki dayanağını yazar, onay verir.
- **OpenFisca Geliştiricisi:** Kuralları kodlar, testleri yazar, trace çıktılarının doğruluğunu sağlar.
- **Veri Mühendisi:** Faz 2’deki ETL ve arama altyapısını kurar, kalite raporlarını üretir.
- **ML/AI Mühendisi:** Agent akışlarını ve LLM ince ayarını yönetir, performansı izler.
- **Ürün Sahibi/Yönetici:** Öncelikleri belirler, faz geçiş onaylarını verir, paydaş iletişimini sağlar.
- **Güvenlik & Uyumluluk:** Gizlilik kontrollerini yapar, denetim raporlarını takip eder.

---

## 9. Zaman Çizelgesi ve Kilometre Taşları

| Hafta | Aktivite | Teslimat |
| --- | --- | --- |
| 1-2 | Mevzuat analizi, parametre taslağı | Madde-fıkra ayrıştırma, onay matrisi |
| 3-4 | Kodlama + testler | Çalışan OpenFisca değişkenleri, test raporu |
| 5-6 | Trace arşivi, Faz 1 kontrol listesi | Hukuk onayı, audit seti |
| 7-8 | ETL kurulumu, veri modeli | RAG bilgi tabanı ilk versiyon |
| 9-10 | RAG kalite ölçümü | Golden set raporu, iyileştirme planı |
| 11-12 | Agent prototipi | Hesaplama + açıklama veren MVP |
| 13-14 | LLM ayarı ve üretim hazırlığı | İzleme dashboard’u, rollback testi |

> Süreler örnektir; mevzuat kapsamı büyüdükçe Faz 1 ek sprintler gerektirebilir.

---

## 10. Riskler ve Çözüm Adımları

- **Mevzuat yorumu farkı:** Hukuk onayı zorunlu; anlaşmazlıkta karar kayıt altına alınır.  
- **Parametre drift’i:** Otomatik testler her gece koşar, beklenmeyen fark varsa alarm üretir.  
- **RAG hatalı sonuç:** Geri çağırma raporları düzenli incelenir; eşik altına düşerse manuel müdahale.  
- **LLM gizlilik riski:** Model eğitimi için kullanılan veri maskelenir; sözleşme ve erişim kontrolleri yapılır.  
- **Operasyonel yük:** Agent çıktıları gözlemlenir; beklenmedik hatada otomatik olarak eski sürüme dönülür.

---

## 11. Sonraki Adımlar

1. Faz 1 kapsamındaki mevcut mevzuat modellerini bu dokümanın kontrol listesi ile kıyaslayın.  
2. Trace ve parametre metadata’larını günlük ETL sürecine aktaran küçük bir PoC hazırlayın.  
3. RAG pilotu için 10 mevzuat maddesi ve 20 test sorusundan oluşan mini veri seti oluşturun.  
4. Agent mimarisinde kullanılacak araçları (OpenFisca API, arama, raporlama) sözleşmeleriyle tanımlayın.  
5. Dil modeli alternatiflerini (Türkçe kapsama, lisans, maliyet) kıyaslayan kısa bir değerlendirme raporu hazırlayın.

Bu yol haritası, hukuki doğruluğu ispatlanmış bir çekirdek üzerine adım adım yapay zekâ destekli karar desteği kurmak için rehberlik eder. Her faz tamamlandığında kurum hem teknik hem iş tarafında ölçülebilir bir değer kazanır.
