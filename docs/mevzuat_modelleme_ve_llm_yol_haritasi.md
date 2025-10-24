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

## 3. Neden Önemli? (Yönetici Özeti)

### Hak kaybı önleme

Mevzuat değişikliklerini kaçıran YMM süreçleri yüzünden şirketlerin elinden kaçan teşvikleri sistematik şekilde geri kazandırırız. Hukukun en temel ve teknik konularından biri olan lehe kazanım kavramını, bu aşamada gerçekleştirdiğimiz çalışmayla yalnızca 2012/3305 için değil bundan sonra modelleyeceğimiz her nevi mevzuat açısından değerlendireceğimiz bir tecrübe alanı yaratmış oluruz.

### Hız

Manuel rapor beklemek yerine aynı gün güncelleme ve geri dönüş sağlarız. Projenin statik aşaması açısından hız kaygımız olmaz, bize ulaşmış bir teşvik belgesinin içindeki unsurları kendi kural motorlarımızla saniyeler içerisinde simule edilir, raporlanması sağlanır.

### Şeffaflık

Her hesaplama mevzuat referansı ve sistem loguyla desteklenir; denetim yükünü azaltır. Simule edilmiş her teşvik belgesinin doğruluğu, aşağıda detaylandırılacak olan trace ve log mekanizmalarıyla analiz edilir. Tespit edilen lehe kazanımlara giden yol, kural motorunda takip edilen adımlar ve neden söz konusu kuralların takip edildiği gözleme ve analize açık olur.

### Ölçeklenebilirlik

Aynı motoru farklı programlara (KOSGEB, TUBITAK, AB teşvikleri) uyarlamak kolaylaşır. Gerektiğinde farklı mevzuatlar için oluşturacağımız veri yapılarını modulerleştirilmiş kural motoruyla oluşturabiliriz. Öte yandan ekibin, 2012/3305 modellemesiyle edindiği tecrübe sayesinde oluşturacağı çalışma prensipleri ile buradan elde ettiği veriler ile besleyeceği yapay zeka sistemi sayesinde diğer mevzuatları/teşvikleri modellemek daha hızlı olur.

### Bilgi Sürdürülebilirliği

Uzman bilgisi dijital hale gelir; kurum hafızası korunur. Oluşturulan sistemin her yapı taşı iş analistleri tarafından dökumente edileceğinden tıpkı açık kaynak olarak çalışan bir sistem gibi, yalnızca kendi içimizde kullanacağımız şematik öğrenme ve geri bildirim prensipleriyle ekip ile business tarafı arasında hem bir "anayasa" hem de bir "köprü" kurulur.

### AI Hazırlığı

Faz 1’de üretilen güvenilir veri, faz 2 ve 3’teki yapay zekâ projelerinin “yakıtı” olur. Bilindiği üzere en hafif yapay zekanın 8 ila 10 milyar arası parametresi mevcuttur. Yapay zeka eğitmek / fine tune etmek / deep learning sağlamak milyarlarca parametrenin arasında hedeflenen "idare hukukçusu" LLM'i yaratmak büyük veri kümeleri ile simule edilmiş somut olay kurguları ile mümkündür. Bu sistemde, 12.000'den fazla sektör kodunun, modellenmiş mevzuatın yüzlerce parametre ve değişkeni iterasyona sokulduğunda ekibin elinde Türkiye'de daha önce bu denli spesifik bir konuda simule edilmemiş kadar hukuki "hazine" bulunmuş olur. Bu veri kümeleriyle eğitilen yapay zeka ile bir sonraki mevzuat sistemleri için yeni stratejik araçlar sağlanır.



---

## 4. Faz 1 – Statik Mevzuat Modelleme (4 Hafta)

### 4.1 Ne Yapıyoruz?
#### Kural modelleme

Karmaşık mevzuat metinlerindeki kuralları, gerçek hayattaki ihtiyaçları karşılayacak anlamlı parametre bütünlerine dönüştürüyoruz.

#### OpenFisca motoru

Parametre ve değişken setlerini OpenFisca’nın hesaplama motoruna işleyip bağımlılık zincirini otomatik hale getiriyoruz; böylece her kural tek doğruluk kaynağına bağlanıyor.

#### Veri harmanlama

Parametre dosyalarıyla oranları, tutarları, tarihleri saklıyoruz.

#### Test otomasyonu

Test senaryoları ile kuralın doğru çalıştığını otomatik kanıtlıyoruz.

#### İzlenebilirlik

Trace kayıtları ile kararın dayandığı maddeyi gösteriyoruz.

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

## 5. Hibrit Mimari: RAG + LLM ile Teşvik Hesaplama Sistemi

### 5.1 Genel Mimari ve Akış

Teşvik hesaplama sistemi, **LLM (Large Language Model) + RAG (Retrieval-Augmented Generation)** birleşiminden oluşan **hibrit bir mimari** ile çalışır.  
Amaç, yatırım teşvik mevzuatına dayalı hesaplamaların hem teknik hem hukuki açıdan izlenebilir, doğrulanabilir ve güvenilir bir biçimde yürütülmesini sağlamaktır.

**Yetkili bilgi kaynağı**, 2012/3305 sayılı Yatırımlarda Devlet Yardımları Hakkında Karar ve ekleriyle birlikte, sistemde kodlanmış **OpenFisca kural setidir**.  
Bu yapı, mevzuat hesaplamalarını, yasal dayanaklarıyla birlikte bütünleştirir.

#### Mimari İşleyiş

1. **Kullanıcı senaryosu**, OpenFisca üzerinde yürütülür ve mevzuat kurallarına göre hesaplanır.  
2. **Mevzuat metinleri**, RAG katmanı tarafından vektör arama yoluyla anlamsal olarak bulunur.  
3. **OpenFisca çıktısı** ve **ilgili mevzuat pasajları**, tek bir bağlam altında birleştirilir.  
4. Bu bağlam, kurum içinde barındırılan **LLaMA tabanlı LLM** modeline aktarılır.  
5. Model, hem sayısal hesaplamaları hem de hukuki atıfları içeren **kaynaklı açıklamalar** üretir.

Operasyonel veriler, parametreler ve kullanıcı girdileri **PostgreSQL** üzerinde saklanır.  
**Neo4j**, teşvik türleri–bölge–sektör–madde ilişkilerini görselleştirmek ve anlamlı bağlantıları keşfetmek için opsiyonel olarak kullanılabilir.  
Tüm parametre ve kural dosyaları **Git** ile versiyonlanır; her değişiklik izlenebilir ve gerektiğinde önceki sürümler geri alınabilir.

Bu mimari; mevzuat doğrulamalı, hesaplaması izlenebilir, **maliyet etkin** ve **ölçeklenebilir** bir karar destek altyapısı sunar.  
Kurum içi barındırma, erişim kontrolü, anonimleştirme ve loglama katmanları ile **KVKK ve siber güvenlik** standartları tam olarak karşılanır.

#### Risk – Etki – Tolerans Tablosu

| Risk | Etki | Tolerans / Önlem Mekanizması |
|------|------|-------------------------------|
| Kaynaksız cevap üretimi | Yüksek | RAG zorunlu politika: kaynak bulunamazsa LLM cevap vermez. Cevaplar “traceable” formatta üretilir. |
| Versiyon karmaşası (parametre–kural uyuşmazlığı) | Orta | Git tabanlı versiyon kontrolü, `parameters_version` etiketi ve otomatik sürüm eşlemesi kullanılır. |
| Gecikme (RAG + LLM zinciri uzunluğu) | Orta | Önbellekleme, kısa bağlam politikası, yeniden sıralama (re-ranking) ve top-k optimizasyonu uygulanır. |
| KVKK ihlali riski | Yüksek | Veriler yalnızca kurum içi sistemlerde işlenir. Erişim logları ve anonimleştirme zorunludur. |
| Donanım darboğazı (yüksek işlem maliyeti) | Düşük | Modüler mimari sayesinde iş yükü yatay olarak ölçeklenir. Gerektiğinde GPU geçici kiralama yapılabilir. |

---

### 5.2 RAG Tercihinin Gerekçesi

#### Şeffaflık
RAG, cevabın dayandığı mevzuat pasajlarını **doğrudan** gösterir.  
Her sonuç, hangi kanun veya karar maddesine dayandığını açık biçimde belirtir.  
Bu özellik, sistemin “kara kutu” olmasını engeller; hem kullanıcı hem denetçi için denetlenebilirlik sağlar.

**Tolerans:**  
- Tüm yanıtlar kaynak referanslı üretildiği için hatalı içerik kolayca tespit edilir.  
- Cevaplar, mevzuat kimliği (`regulation_id`) üzerinden loglanır.  

#### Güncellik
RAG mimarisi, modelin bilgi kesiti tarihinden bağımsız olarak **en güncel mevzuat değişikliklerini** yanıtlarında kullanabilir.  
Yeni tebliğ veya karar yayımlandığında bilgi tabanına eklenir ve sistem derhal bu verileri kullanmaya başlar.

**Tolerans:**  
- Günlük otomatik ETL çalışmalarıyla yeni mevzuatlar vektör tabanına yüklenir.  
- Her mevzuat kaydı `effective_date` alanıyla zaman damgalıdır, eski bilgiler otomatik olarak devre dışı kalır.  

#### Maliyet ve Çeviklik
RAG, modeli yeniden eğitmeden bilgiye erişim sağlar.  
Bu sayede GPU eğitimi ve API kullanımı gibi yüksek maliyetli işlemlerden kaçınılır.  
Yalnızca bilgi tabanına doküman eklenerek sistem güncel kalır.

**Tolerans:**  
- Genişleyen mevzuat hacmine karşılık, sorgu süresi artmaması için dinamik indeksleme yapılır.  
- Performans izleme panelleriyle sorgu gecikmeleri takip edilir.  

#### Doğruluk
RAG, halüsinasyon (uydurma bilgi) riskini azaltır.  
LLM yalnızca RAG tarafından getirilen doğrulanabilir içerikler üzerinden cevap üretir.

**Tolerans:**  
- Model, bağlam dışı bilgi üretmeye kalkarsa “no-answer” yanıtı döner.  
- Kullanıcıya gerekçeli hata mesajı sunulur: “Bu konuda kaynak bulunamadı.”  

#### Risk – Etki – Tolerans Tablosu

| Risk | Etki | Tolerans / Önlem Mekanizması |
|------|------|-------------------------------|
| Yanlış paragraf eşleşmesi | Orta | Hibrit arama (BM25 + vektör) kullanılır, her yeni mevzuat “altın set” testinden geçer. |
| İndeks gecikmesi | Düşük | “On-change” tetikleme mekanizması ve günlük toplu senkronizasyon. |
| Uzun bağlam → yüksek gecikme | Düşük | Re-ranking + top-k (5) sınırı, metin kısaltma politikası. |
| Eksik mevzuat verisi | Orta | Otomatik mevzuat kontrol listesi ve eksik belge uyarı raporları. |

---

### 5.3 Veri Orkestrasyonu ve ETL Süreci

Veri akışı, sistemin sürekliliğini ve güvenilirliğini sağlar.  
Her mevzuat değişikliği, otomatik olarak ETL sürecini tetikler ve yeni indeks oluşturur.

- **Kayıt yapısı:** `source_type`, `effective_date`, `regulation_id`, `knowledge_id`, `hash`, `version`.  
- **İzleme:** Her OpenFisca çalıştırması, kullanılan parametre sürümü ve mevzuat referanslarıyla birlikte loglanır.  
- **Kalite kontrol:** Eksik veya hatalı indeksleme durumunda sistem otomatik uyarı üretir.  
- **Denetim izlenebilirliği:** Tüm işlem geçmişi PostgreSQL log tablosunda saklanır.

**Tolerans:**  
- ETL başarısız olduğunda, eski sürüm veritabanı yedekleriyle sistem çalışmaya devam eder (failover).  
- Prometheus izleme sistemi kritik uyarıları teknik ekibe iletir.

---

## 6. LLM Fine-Tune Stratejisi

### 6.1 Amaç
Uzun vadede hedef, genel amaçlı bir LLM’i Türkçe teşvik mevzuatı ve hukuk diliyle özelleştirerek  
**“hukuk dilini anlayan ve gerekçeli cevap üreten yapay zekâ asistanı”** oluşturmaktır.

### 6.2 Yaklaşım

- **Model:** Meta LLaMA ailesi, kurum içi barındırma.  
- **Veri:** Türkçe teşvik mevzuatı, yönetmelikler, karar örnekleri, RAG tarafından doğrulanmış soru–cevap çiftleri.  
- **Teknik:** Hafif ince ayar (LoRA/PEFT) ile düşük GPU maliyeti.  
- **Gizlilik:** Tüm eğitim ve kullanım süreci kurum içinde gerçekleşir.  
- **Değerlendirme:** Terminoloji doğruluğu, kaynak gösterme ve bağlam tutarlılığı ölçülür.

### 6.3 Faydalar

- Alan uzmanlığı: Model, teşvik kavramlarını ve istisna mantığını doğal biçimde öğrenir.  
- Uyarlanabilirlik: Yeni mevzuatlar eklendikçe yeniden eğitilebilir.  
- Kuruma özel dil: Model, kurumun iletişim tarzına uygun yanıt üretir.  
- Maliyet avantajı: Açık model kullanımı, API bağımlılığını ortadan kaldırır.  
- Veri gizliliği: KVKK riski ortadan kalkar.

#### Risk – Etki – Tolerans Tablosu

| Risk | Etki | Tolerans / Önlem Mekanizması |
|------|------|-------------------------------|
| Eğitim verisi yetersizliği | Orta | RAG çıktılarından türetilmiş QA setleriyle veri artırımı yapılır. |
| Aşırı maliyet | Düşük | LoRA/PEFT yöntemiyle GPU maliyeti %90 düşürülür. |
| Model eskimesi | Orta | Her 6 ayda bir yeniden eğitme döngüsü uygulanır. |
| Gizlilik ihlali | Yüksek | Model ve veriler kurum içi güvenli ağda tutulur, dış bağlantı yoktur. |
| Düşük kalite çıktılar | Orta | Golden set değerlendirmesiyle kalite kontrol sağlanır. |

---

## 7. RAG + LLM Hibrit Stratejisi

### 7.1 Entegrasyon Yapısı

RAG ve fine-tune edilmiş LLM birlikte çalışarak hem **güncel bilgiye erişimi** hem **doğru yorumlamayı** sağlar:

1. **RAG** güncel mevzuat ve parametreleri getirir.  
2. **LLM**, bu bağlamı işleyip açıklayıcı yanıt üretir.  
3. Cevap, hem hesaplama sonucunu hem mevzuat referansını içerir.  

### 7.2 Bilgi Döngüsü

RAG’in doğrulanmış yanıtları, LLM’in eğitim verisine dönüştürülür.  
Model, geçmiş başarılı cevaplardan öğrenerek giderek daha yüksek isabet oranına ulaşır.  
Bu yapı, sistemin “kendi kendini geliştirebilen” bir hale gelmesini sağlar.

### 7.3 Operasyonel Denge

RAG, yeni mevzuatlara anında uyum sağlarken;  
LLM, kullanıcı deneyimini güçlendirir ve dilsel tutarlılığı artırır.  
Bu sayede sistem, teknik olarak **esnek**, **ölçeklenebilir** ve **denetlenebilir** kalır.

#### Risk – Etki – Tolerans Tablosu

| Risk | Etki | Tolerans / Önlem Mekanizması |
|------|------|-------------------------------|
| RAG–LLM senkronizasyonu kaybolması | Orta | `knowledge_id` tabanlı sürüm eşlemesi ve zaman damgası kontrolü. |
| Performans düşüşü | Orta | CI/CD entegrasyonu ve otomatik kalite testleri. |
| Halüsinasyon | Düşük | RAG kaynak zorunluluğu, grounded output politikası. |
| Model kararlılığı | Orta | Sürümleme politikası, rollback ve gözetimli test süreci. |

---

## 8. Karar ve Yol Haritası

### 8.1 Kısa Vade (0–6 Ay)

- Mevcut **RAG tabanlı sistem** üretimde kalacak.  
- OpenFisca–RAG veri senkronizasyonu sağlanacak.  
- LLaMA fine-tune eğitimi tamamlanacak, pilot testler yapılacak.  
- Hatalı sorgular için hata izleme mekanizması devreye alınacak.

**Tolerans:**  
Kritik hatalarda RAG fallback mekanizması devreye girer; sistem daima en son kararlı sürüme döner.

### 8.2 Orta Vade (6–12 Ay)

- **RAG + Fine-Tuned LLM hibrit sistem** üretime alınacak.  
- Performans, güvenlik ve doğruluk ölçütleri düzenli izlenecek.  
- Kullanıcı geri bildirimleri doğrultusunda model güncellemeleri planlanacak.

**Tolerans:**  
Senkronizasyon kaybı veya kalite düşüşü durumunda, yalnızca RAG moduna geçiş yapılabilir (geçici tekil mod).

### 8.3 Uzun Vade (12 Ay ve Sonrası)

- Sistem, KOSGEB, TÜBİTAK ve AB destek mekanizmalarını da kapsayacak şekilde genişletilecektir.  
- LLM, mevzuat değişikliklerini **proaktif özetleyen asistan** haline gelecektir.  
- Kurumsal bilgi birikimi, sürekli öğrenen yapay zekâ mimarisine dönüşecektir.

**Tolerans:**  
Yeni mevzuat modülleri eklendiğinde sistemin kararlılığını korumak için her genişleme öncesi A/B testleri yapılacaktır.

---

## 9. Sonuç

RAG + LLM hibrit mimarisi, kısa vadede **şeffaflık ve doğruluk**, orta vadede **derin analiz ve verimlilik**, uzun vadede ise **kurumsal öğrenme ve stratejik avantaj** sağlar.  
RAG bileşeni, sistemin denetlenebilir omurgasını oluştururken; fine-tune edilmiş LLM, yorumlama ve açıklama kalitesini güçlendirir.

Riskler tamamen yönetilebilir niteliktedir; her biri için açık tolerans ve önlem mekanizmaları tanımlanmıştır.  
Sonuç olarak, bu mimari yalnızca bir yazılım çözümü değil, kurumsal bilgi birikimini dijitalleştiren ve karar süreçlerini hızlandıran **akıllı bir teşvik danışmanlık altyapısı** sunmaktadır.
