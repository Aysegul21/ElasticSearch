# Elasticsearch’e Giriş

> **Bu çalışma öncelikle şu makaleden özellikle yararlanılmıştır:**
> [JRSE-2022-4-11\_7](https://web.archive.org/web/20221203011409id_/https://www.bryanhousepub.org/src/static/pdf/JRSE-2022-4-11_7.pdf)

## Elasticsearch Nedir?

<img width="1800" height="878" alt="Image" src="https://github.com/user-attachments/assets/6062b2cd-fc9d-4e99-992e-90af43a7146c" />


Dijital çağda veri miktarı her geçen gün artıyor. Farklı kaynaklardan gelen büyük veri kümelerini **hızlıca aramak, sorgulamak ve analiz etmek** geleneksel ilişkisel veritabanlarıyla giderek daha karmaşık ve maliyetli hale geliyor. 

Bu noktada, **geleneksel ilişkisel veritabanlarının aksine**, ölçeklenebilir, dağıtık ve gerçek zamanlı bir çözüm sunan **Elasticsearch** öne çıkar. Metin, sayısal veri, zaman serileri, coğrafi veriler ve yarı/yapılandırılmamış içeriklerle (ör. log kayıtları, JSON belgeleri) çalışabilen, açık kaynaklı bir **arama ve analiz motoru**dur.

**"Dağıtık" olması**, veriyi birden fazla sunucuya (node) yayarak hem **yüksek erişilebilirlik** (high availability) sağladığı hem de işlem yükünü paylaştırarak çok **yüksek performans** sunduğu anlamına gelir.

Lucene üzerine inşa edilen Elasticsearch, bu ölçeklenebilir mimarisi sayesinde büyük veri üzerinde milisaniyeler içinde tam metin arama ve gerçek zamanlı analiz yapabilme gücü sunar.

---

## Elasticsearch’ün Temel Özellikleri — Ayrıntılı İnceleme

Aşağıda Elasticsearch’ün en önemli yapı taşlarını teknik ama anlaşılır bir biçimde ele alıyoruz. Her bölüm, hem kavramsal açıklama hem de gerçek kullanım avantajlarını içerir.

### 1. Dağıtık Mimari (Cluster, Node, Shard)

<img width="2732" height="2048" alt="Image" src="https://github.com/user-attachments/assets/7dc0ff41-73b8-4995-a6a4-34e793bd7594" />

* **Cluster (Küme):** Bir Elasticsearch kümesi, birden çok node’dan (sunucu/instance) oluşur ve tek bir mantıksal sistem gibi davranır.
* **Node (Düğüm):** Küme içindeki her makine veya sanal instance bir node’dur. Node’lar farklı roller üstlenebilir:

  * *Master Node:* Küme yönetiminden sorumludur.
  * *Data Node:* Veri depolar ve sorguları işler.
  * *Ingest Node:* Veri ön işleme ve pipeline görevlerini yürütür.
  * *Coordinator Node:* İstekleri koordine eder, sonuçları birleştirir.
* **Shard (Parça):** Her indeks, birden çok parçaya (shard) bölünür. Shard’lar veriyi paralel olarak saklar ve sorgular; bu da ölçeklenebilirlik ve hız kazandırır.
* **Pratik Fayda:** Büyük veri setleri farklı sunuculara bölünerek hem depolama kapasitesi hem de sorgu performansı artırılır.

**Örnek:** Bir indeks için `5` primary shard ve her biri için `1` replica tanımlarsanız, toplamda `10` shard (5 primary + 5 replica) elde edilir ve bu shard’lar kümedeki farklı node’lara otomatik dağıtılır.

<img width="1856" height="736" alt="Image" src="https://github.com/user-attachments/assets/b037d03e-c147-4368-b739-b0cdc3928d92" />
---

### 2. Replikasyon ve Yüksek Erişilebilirlik (High Availability)

* **Replica Shard:** Her primary shard’ın bir veya birden fazla kopyası bulunabilir. Replica’lar hem okuma performansını artırır hem de node arızalarında verinin kaybolmasını engeller.
* **Otomatik Yeniden Dağıtım:** Bir node devre dışı kaldığında veya yeni bir node eklendiğinde, Elasticsearch shard’ları otomatik olarak yeniden dağıtır.
* **Pratik Fayda:** Beklenmedik donanım arızalarında bile indekslere kesintisiz erişim sağlar.

---

### 3. Yakın Gerçek Zamanlılık (Near Real-Time, NRT)

<img width="907" height="347" alt="Image" src="https://github.com/user-attachments/assets/41a91ae0-dc2e-4353-8b65-5ef28332ac99" />

* **İndeksleme → Sorgu Süresi:** Elasticsearch neredeyse gerçek zamanlı çalışır. Yeni eklenen belgeler milisaniyeler içinde aranabilir hale gelir.
* **Pratik Fayda:** Canlı log analizi, gerçek zamanlı dashboard ve arama önerisi gibi düşük gecikme gerektiren senaryolar için idealdir.

---

### 4. JSON Tabanlı REST API

<img width="1712" height="748" alt="Image" src="https://github.com/user-attachments/assets/d24a7da6-11e4-49ee-aae0-1e4c05cb5f79" />

Elasticsearch, tüm etkileşimlerin HTTP protokolü üzerinden JSON formatı ile yapıldığı esnek ve geliştirici dostu bir REST API sunar.  
Bu sayede herhangi bir ek kütüphane kullanmadan **cURL**, **Postman**, tarayıcı veya herhangi bir programlama dili ile kolayca istek göndermek mümkündür.

* **Temel Özellikler:**
  * **CRUD İşlemleri:** Veri ekleme (Create), okuma (Read), güncelleme (Update) ve silme (Delete) işlemleri tek satırlık HTTP istekleriyle gerçekleştirilebilir.
  * **Stateless Mimari:** Her istek bağımsızdır; istemci ve sunucu arasında sürekli bağlantı gerekmez.
  * **JSON Yanıtları:** Dönen sonuçlar her zaman JSON formatındadır, bu da verilerin programatik olarak işlenmesini ve entegrasyonunu kolaylaştırır.
  * **Çapraz Platform Desteği:** Python, JavaScript, Java, Go gibi farklı dillerde kolayca kullanılabilir.

```http
GET /kitaplar/_search?q=yazar:Orwell
```

Bu örnek, kitaplar indeksinde yazar alanı Orwell olan belgeleri sorgular ve sonuçları JSON formatında döndürür.
Daha Gelişmiş Sorgu Örneği (Request Body ile):

```http
POST /kitaplar/_search
{
  "query": {
    "match": {
      "icerik": "distopya"
    }
  },
  "size": 5,
  "sort": [
    { "tarih": "desc" }
  ]
}
```
Bu örnekte:

- **match** sorgusu ile `icerik` alanında “distopya” kelimesini içeren belgeler aranır.  
- **size** parametresi ile en fazla **5** sonuç döndürülmesi istenir.  
- **sort** ile sonuçlar `tarih` alanına göre **azalan** sırada listelenir.  

Bu yapı, Elasticsearch’ün yalnızca basit anahtar–değer aramaları değil, aynı zamanda karmaşık filtreleme, sıralama ve tam metin arama senaryoları için de güçlü bir çözüm sunduğunu gösterir.

---

### 5. Elastic Stack Entegrasyonu (ELK Stack)

<img width="1800" height="878" alt="Image" src="https://github.com/user-attachments/assets/6062b2cd-fc9d-4e99-992e-90af43a7146c" />

* **Kibana:** Verilerin görselleştirilmesi ve dashboard oluşturma aracı.
* **Logstash:** Farklı kaynaklardan gelen verilerin toplanıp işlenmesini sağlar.
* **Beats:** Hafif veri toplayıcı ajanlar; log, metrik ve network verilerini Elasticsearch’e iletir.
* **Pratik Fayda:** Elasticsearch tek başına güçlüdür, ancak Elastic Stack bileşenleri ile log analizi, metrik takibi, alarm mekanizmaları ve görselleştirme gibi kapsamlı bir veri ekosistemi oluşturabilirsiniz.

---

### 6. Lucene Tabanlı Güçlü Arama

<img width="1600" height="840" alt="Image" src="https://github.com/user-attachments/assets/acb6d1ce-2581-4815-bf83-c402f322ebc0" />

* **Lucene Motoru:** Elasticsearch, veriyi depolamak ve sorgulamak için yüksek performanslı Lucene kütüphanesini kullanır.
* **İndeksleme:** Her belge, ters indeks (inverted index) yapısına dönüştürülür. Bu sayede tam metin arama (full-text search) hızlı ve ölçeklenebilir olur.
* **Pratik Fayda:** Milyarlarca belgeyi milisaniyeler içinde tarayabilir, arama sonuçlarını gerçek zamanlı sunabilir.

---

### 7. Ölçeklenebilirlik ve Performans

<img width="750" height="225" alt="Image" src="https://github.com/user-attachments/assets/2891df4a-000d-4df1-b024-9b5e770a329d" />

<img width="750" height="225" alt="Image" src="https://github.com/user-attachments/assets/c3a5375e-a233-46f5-9993-47b5f0c2a58b" />

* **Yatay Ölçeklenme:** Yeni node ekleyerek cluster kapasitesini artırabilirsiniz.
* **Yük Dengeleme:** Sorgu ve indeksleme yükü otomatik olarak node’lar arasında paylaştırılır.
* **Pratik Fayda:** Veri hacmi büyüdükçe donanım yatırımı minimum kesintiyle genişletilebilir; büyük veri kümelerinde bile yüksek performans sağlanır.

---

### 8. Veri Tipleri ve Analiz Yeteneği

* **Çeşitli Veri Türleri:** Elasticsearch, sayısal, metin, tarih, coğrafi ve yapılandırılmamış verileri destekler.
* **Aggregations (Toplulaştırmalar):** Karmaşık veri analizleri, gruplamalar ve istatistiksel hesaplamalar yapılabilir.
* **Pratik Fayda:** Sorgulamanın ötesinde, veri analizi ve raporlama için de kullanışlıdır.

---

### 9. Yüksek Hız ve Düşük Gecikme

<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/b67fa236-6f7b-4a63-9e54-cd38faa26c16" />

* **Near Real-Time ve Paralel İşlem:** Hem indeksleme hem de sorgu süreçleri paralel olarak çalışır.
* **Pratik Fayda:** Gerçek zamanlı analitik, canlı arama önerileri ve dashboard uygulamalarında düşük gecikme sağlar.

---

### 10. Hata Toleransı ve Dayanıklılık

<img width="2880" height="1578" alt="Image" src="https://github.com/user-attachments/assets/5549c069-d032-4f05-a483-6c49be1e0c08" />

* **Shard Replikasyonu:** Primary shard’lar bir veya birden fazla replica shard ile çoğaltılır.
* **Node Arızaları:** Herhangi bir node kapanırsa, veriler replica’lar üzerinden erişilmeye devam eder ve sistem otomatik olarak yeniden dengeye girer.
* **Pratik Fayda:** Üretim ortamında veri kaybı riski minimumdur; yüksek kullanılabilirlik (HA) sağlar.

---

Bu başlıklar, Elasticsearch’ün sadece bir arama motoru olmadığını, aynı zamanda yüksek hacimli veriyi hızlı ve güvenilir bir şekilde analiz edebilen güçlü bir veri platformu olduğunu gösterir. Sonraki bölümlerde, indeksleme süreçleri, ters indeks (inverted index) mantığı, tokenization ve analyzer’lar gibi daha teknik detaylar ele alınacaktır.

## Temel Kavramlar (Key Concepts)

Elasticsearch'ün Lucene üzerine getirdiği en önemli yeniliklerden biri **dağıtık yapısı**dır. Bu yapı, Elasticsearch'ün clusterlar üzerinde ölçeklenebilmesini sağlar. Aşağıda bu temel kavramları açıklıyoruz:

### 1. Document (Belge)

<img width="1279" height="637" alt="Image" src="https://github.com/user-attachments/assets/f2da187c-cd89-4f08-8cdf-d943a4ed7d71" />

- Elasticsearch’te veri **JSON objeleri** olarak saklanır ve her belge en küçük veri birimi olarak kabul edilir.
- Belge, ilişkisel veritabanlarındaki satırlara (row) benzer. Her belge bir veya daha fazla alan (field) içerir.
- Bir belge indekslendiğinde, **ters indeks (inverted index)** oluşturulur ve belge gerçek zamanlı olarak aranabilir hale gelir.

### 2. Type (Tür)

- Eskiden belgeler birden fazla türe (_type) sahip olabiliyordu. Örneğin aynı indeks içinde `roman` ve `makale` türleri farklı mapping ile saklanabiliyordu.  
- Ancak **Elasticsearch 6.x ve sonrası sürümlerde**, bir indeks **sadece tek bir `_type`** kullanır. Artık aynı indeks içinde farklı mapping ve türlerde belgeler saklanamaz.  
- Farklı belgeleri ayırmak için, genellikle bir **alan (ör. `tur`)** eklenir ve bu alan belgelerin türünü belirtir. Örnek:
  

<img width="823" height="236" alt="Image" src="https://github.com/user-attachments/assets/81cc51ce-2a13-4fe7-a534-63905813805e" />

### 3. Index (İndeks)
- Elasticsearch’te indeks, SQL’deki veritabanına benzer.
- Bir indeks bir veya daha fazla belge içerir; belgeler farklı tür ve mapping’lere sahip olabilir.
- İndeks, bir veya daha fazla **primary shard** üzerinde saklanır ve her primary shard için **replica shard** oluşturulur.

### 4. Mapping (Alan Tanımı)
- Mapping, belgelerin yapısını (alanlar, veri tipleri ve ilgili metadata) tanımlar.
- İlişkisel veritabanlarındaki tablo şemasına benzer.
- İki tür mapping vardır: **Dynamic** (otomatik alan tanımı) ve **Explicit** (manuel tanım). Kullanıcı her iki yaklaşımı da birleştirerek esnek mapping oluşturabilir.

### 5. Node (Düğüm)
- Node, veri saklamak ve indeksleme yapmakla görevli Elasticsearch instance’ıdır.
- Node’lar farklı roller üstlenebilir: master, data, client, tribe, ingest, machine learning.
- Master node: cluster yönetimi  
- Data node: veri saklama ve işlemler  
- Client node: istekleri yönlendirme ve yük dengeleme  
- Tribe node: birden fazla cluster’ı birleştirir  
- Ingest node: indeksleme öncesi veri işleme  
- ML node: makine öğrenimi görevleri

### 6. Cluster (Küme)
- Bir veya daha fazla node’un birleşmesi ile oluşur.
- Dağıtık yapısı sayesinde tek bir node üzerindeki yük diğer node’lara dağıtılır.

### 7. Shard (Parça)
- İndeksler bir veya daha fazla shard’a bölünür.
- Shard’lar node’lar arasında dağıtılır; primary ve replica olmak üzere iki tür shard vardır.
- Yük arttığında shard’lar diğer node’lara taşınarak veri dengesi sağlanır.

## 3. Metin Arama (Text Search) ve Analiz

<img width="1418" height="1102" alt="Image" src="https://github.com/user-attachments/assets/13506651-2cff-4b10-ab94-5e63831a7b8b" />

Elasticsearch’te **metin arama**, belgelerdeki metin alanlarının önceden işlenmesi ile mümkündür. Bu işleme süreci **analysis** (metin analizi) aşamasında gerçekleşir. Belgeler indekslenirken metinler bir **analyzer** tarafından analiz edilir ve sonuç, aramayı hızlandıracak veri yapılarında saklanır.

### Analyzer Bileşenleri

<img width="1336" height="736" alt="Image" src="https://github.com/user-attachments/assets/83fa26a4-a939-4afb-ad08-41de4ac40e1d" />

Bir analyzer üç temel yapıdan oluşur: **character filters, tokenizers, token filters**.

1. **Character Filters:** Orijinal metni alır ve karakterleri ekleyip, çıkararak veya değiştirerek metni dönüştürür. Bir analyzer bir veya daha fazla character filter içerebilir ve bunlar belirli bir sırayla uygulanır.

2. **Tokenizer:** Metni parçalara (token) ayırır. Örneğin bir cümleyi kelimelere böler; boşluk ve noktalama işaretleri bu aşamada kaldırılabilir. Tokenizer ayrıca her token’ın karakter konumunu (offset) kaydeder. Analyzer yalnızca bir tokenizer içerebilir.

3. **Token Filters:** Tokenizer’dan çıkan token’ları alır ve token üzerinde ekleme, çıkarma veya değiştirme işlemleri uygular. Örneğin lowercase filter, tüm token karakterlerini küçük harfe çevirir. Analyzer bir veya daha fazla token filter içerebilir ve bunlar belirli bir sırayla uygulanır.

### Ön Tanımlı ve Özelleştirilmiş Analyzer
- Elasticsearch birçok **built-in analyzer, character filter, tokenizer ve token filter** sunar.
- Kullanıcılar kendi analyzer’larını oluşturmak için farklı kombinasyonlar kullanabilir.
- **Varsayılan Analyzer:** Standard Analyzer; karakter filtresi içermez, Standard Tokenizer kullanır (boşluk ve noktalama işaretlerini kaldırır) ve lowercase token filter uygular.

### Ters İndeks (Inverted Index)

<img width="962" height="465" alt="Image" src="https://github.com/user-attachments/assets/cc5e69f9-c16d-489a-80d7-cc26ffb92c48" />

- Analyzer’dan çıkan token’lar, farklı veri tiplerine göre farklı veri yapılarında saklanır.
- Metin alanları için oluşturulan veri yapıları **inverted index** olarak adlandırılır ve full-text search işlemini hızlandırır.
- Ters indeks, **terimler (tokens) ile bu terimleri içeren belgeler** arasındaki eşlemeyi içerir.
- Terimler alfabetik olarak sıralanır ve **relevance scoring** bilgisi içerir. Bu sayede arama sorgusu, en uygun belgeleri öncelikli olarak döndürür.

> Not: Metin dışındaki veri tipleri (sayısal, tarih vb.) için farklı veri yapıları (ör. BKD trees) kullanılır.

### Mapping
- Indexlenen belgeler **mapping** ile yapılandırılır.
- Mapping, belge alanlarının veri tiplerini ve indekslenme biçimlerini tanımlar; ilişkisel veritabanlarındaki tablo şemasına (schema) benzer.
- Mapping, kullanıcı tarafından **explicit** olarak veya Elasticsearch tarafından **implicit** olarak oluşturulabilir.

## 4. Elasticsearch’ün Öne Çıkan Özellikleri (Features)

<img width="706" height="434" alt="Image" src="https://github.com/user-attachments/assets/2b00bd97-c38f-4e2d-a8cb-b6dd2b053100" />

 

Elasticsearch, güçlü bir arama ve analiz motoru olmasının yanı sıra aşağıdaki özelliklere sahiptir:

### 1. Yatay Ölçeklenebilirlik (Highly Scalable)
- Elasticsearch, yapılandırılmış ve yapılandırılmamış veriyi birkaç petabayta kadar **yatay olarak ölçekleyebilir**.
- Depolama kapasitesini artırmak için cluster’a daha fazla node eklenebilir.
- Her shard için önerilen üst limit: **50 GB** (üzerinde shard’lar daha verimli yönetilir).

### 2. Yüksek Güvenlik (Highly Secure)
- Tüm veriler parola ile korunabilir; yetkisiz erişimler engellenir.
- Diğer güvenlik mekanizmaları:
  - Role-based access control (RBAC)  
  - Attribute-based access control  
  - Audit logging  
  - IP filtreleme  
  - İletişim şifrelemesi

### 3. Yüksek Erişilebilirlik (Highly Available)
- Cluster konsepti ile bir veya daha fazla node bir araya gelerek tüm veriyi tutar ve indeksleme ile arama işlevini tüm cluster üzerinde sağlar.
- **Primary ve Replica shard** yapısı sayesinde failover sağlanır:
  - Primary shard’ı olan node kapanırsa, replica shard primary olarak atanır.
  - Bu sayede cluster sürekli erişilebilir olur.

### 4. Tam Metin Arama Motoru (Full-Text Search Engine)
- Geleneksel SQL veritabanları büyük veri üzerinde full-text arama için optimize edilmemiştir.
- Elasticsearch, yapılandırılmış, yapılandırılmamış, coğrafi ve metrik veriler üzerinde **gelişmiş full-text search** yetenekleri sunar.
- Farklı arama türlerini birleştirerek kompleks sorgular yapılabilir.

### 5. Analitik (Analytics)
- Elasticsearch, sadece metin arama için değil, sayısal ve yapılandırılmış veriyi sorgulamak ve toplulaştırmak için de kullanılabilir.
- Line chart, pie chart gibi görselleştirmelerle veriyi analiz etmek mümkündür.
- Kibana ile entegrasyon sayesinde görselleştirme ve raporlama yapılabilir.

### 6. İndeks Yönetimi (Index Management)
- Elasticsearch, indeksleri izleme ve yönetme araçları sunar.
- **Index State Management (ISM):**  
  - Özel politika tanımlama  
  - İndeks optimizasyonu  
  - İzleme ve yönetim  
  - İndeks yaşı, boyutu veya belge sayısına göre otomatik işlemler
- ISM sayesinde harici sistemlere ihtiyaç duymadan, periyodik bakım ve yönetim yapılabilir.

## 5. Elastic Stack ve İlgili Teknolojiler

Elasticsearch, tek başına güçlü bir motor olsa da, **Elastic Stack** (eski adıyla ELK Stack) ile birlikte kullanıldığında veri toplama, işleme, görselleştirme ve analiz süreçlerini bütünsel olarak destekler. Elastic Stack, Elastic NV tarafından geliştirilen ve Elasticsearch ile sıkı bir entegrasyona sahip teknolojilerden oluşur.

### Elastic Stack Bileşenleri

<img width="979" height="390" alt="Image" src="https://github.com/user-attachments/assets/32832166-d5fd-4dbc-a5e8-650de88335ea" />

1. **Kibana**  
   - Analytics ve görselleştirme platformudur.  
   - Elasticsearch verilerini grafikler, çizelgeler ve haritalar üzerinden görselleştirmeyi sağlar.  
   - Örnek: Pie chart, line chart, area chart, bar chart, coordinate map, region map.

2. **Logstash**  
   - Açık kaynak ve hafif bir veri işleme pipeline’ıdır.  
   - Farklı kaynaklardan gelen verileri alır, işler ve Elasticsearch veya diğer hedeflere gönderir.  
   - Pipeline üç aşamadan oluşur: **Input → Filter → Output**.  
   - Birden fazla pipeline aynı Logstash instance’ında çalıştırılabilir ve yatay olarak ölçeklenebilir.

3. **X-Pack**  
   - Elasticsearch ve Kibana’ya ek özellikler ekleyen bir uzantıdır.  
   - Özellikleri: Güvenlik, izleme, makine öğrenmesi, uyarılar, raporlama.  
   - Kibana üzerinden makine öğrenmesi ile anomali tespiti ve veri tahminleri yapılabilir.  
   - Graph özelliği ile veri ilişkilerini analiz edip, interaktif grafikler oluşturabilir.

4. **Beats**  
   - Hafif veri toplama ajanlarıdır ve Logstash veya Elasticsearch’e veri gönderir.  
   - Örnek Beats:
     - Filebeat: Log dosyalarını toplar  
     - Metricbeat: Sistem ve servis metriklerini toplar (CPU, RAM)  
     - Packetbeat: Ağ verilerini toplar  
     - Auditbeat: Linux audit verilerini toplar  
     - Winlogbeat: Windows event log verilerini toplar

---
## 6. Sonuç (Conclusion)

Arama, Elasticsearch’ün en temel özelliklerinden biridir.  
- Çeşitli kriterlere göre doküman aramayı destekler ve **near real-time** arama imkânı sağlar.  
- **Inverted index** kullanımı sayesinde arama süreci çok hızlıdır.  
- Kullanıcılar, arama sonuçlarının **relevance score** (alaka derecesi) ile sıralanmasını sağlayabilir.  
- Dağıtık mimari yapısı sayesinde, cluster içindeki herhangi bir node arızalansa bile veri kaybı yaşanmaz; bu da Elasticsearch’ü **yüksek erişilebilirlik** sağlayan bir sistem hâline getirir.


## Kaynakça
https://www.elastic.co/docs/deploy-manage/maintenance/add-and-remove-elasticsearch-nodes
https://www.knowi.com/blog/what-is-elastic-search/#replicas
https://web.archive.org/web/20221203011409id_/https://www.bryanhousepub.org/src/static/pdf/JRSE-2022-4-11_7.pdf
