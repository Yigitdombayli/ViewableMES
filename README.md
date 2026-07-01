# ViewableMES: Endüstri 4.0 Gerçek Zamanlı Üretim Yürütme ve SCADA Sistemi

Endüstri 4.0'ın getirdiği dijital dönüşüm gereksinimleri, üretim sahalarındaki verilerin anlık olarak izlenmesini, analiz edilmesini ve yönetilmesini zorunlu kılmaktadır[cite: 10]. Geliştirilen "ViewableMES" projesi, sahadaki endüstriyel cihazlardan (PLC) OPC UA protokolü aracılığıyla gerçek zamanlı veri toplayan, bu verileri yüksek performanslı zaman serisi veritabanlarında işleyen ve web tabanlı bir arayüz üzerinden kullanıcıya sunan yenilikçi bir Üretim Yürütme Sistemi (MES) ve SCADA platformudur[cite: 10].

![Dashboard](MachineGET.png)

## 🎯 Projenin Amacı
* Makine bazlı üretim takibi yapmak[cite: 10].
* OEE (Genel Ekipman Etkinliği) değerlerini anlık hesaplamak[cite: 10].
* İş emirlerini dijital ortama taşımak[cite: 10].
* Plansız duruşları (downtime) minimuma indirmektir[cite: 10].

---

## 🏗 Mimari Tasarım ve Kullanılan Teknolojiler
Proje, ölçeklenebilir ve sürdürülebilir bir yapı sağlamak amacıyla N-Katmanlı (N-Tier) mimari prensiplerine uygun tasarlanmıştır[cite: 10].

### Backend ve Veri Yönetimi Teknolojileri
* **ASP.NET Core 9.0 (MVC & Web API) ve EF Core 9.0:** Temel uygulama iskeleti ve Code-First ORM yapısı[cite: 10].
* **SignalR & OPC UA:** İstemci-sunucu arası anlık veri akışı ve otomasyon cihazlarıyla (PLC) haberleşme[cite: 10].
* **InfluxDB & MS SQL Server:** Yüksek hacimli zaman serisi sensör verileri (InfluxDB) ve ilişkisel sistem verileri (SQL)[cite: 10].
* **Background Services:** Veri toplama ve OEE hesaplama gibi asenkron arka plan görevleri[cite: 10].

---

## ⚙️ Temel Modüller ve İşlevler

### 1. Admin Paneli ve Sistem Konfigürasyonları
Sistem yöneticilerinin tesisteki tüm altyapıyı yönettikleri merkezi kontrol noktasıdır[cite: 10].

* **Kullanıcı ve Rol Yönetimi:** Sistem güvenliği ve erişim kontrolü, ASP.NET Core Identity altyapısı kullanılarak detaylı bir rol mimarisiyle kurgulanmıştır[cite: 10]. Sistemde PlantManager, Supervisor, Operator, MaintenanceTech, QualityInspector gibi üretim hiyerarşisine uygun roller tanımlanmıştır[cite: 10]. Yöneticiler, bu ekran üzerinden kullanıcılara çoklu rol ataması yapabilmekte ve sistem üzerindeki yetki sınırlarını belirleyebilmektedir[cite: 10].
* **Makine ve PLC Yönetimi:** Sahadaki fiziksel makinelerin ve bu makinelere bağlı PLC ünitelerinin dijital ikizlerinin (digital twin) oluşturulduğu bölümdür[cite: 10]. Tesis içerisindeki makineler, lokasyon bilgileri ve ID'leri ile sisteme tanımlanır[cite: 10]. Makinelerden veri alabilmek için gerekli olan OPC UA Endpoint URL'leri bu arayüzden konfigüre edilir ve bağlantı durumları anlık izlenir[cite: 10]. Her bir PLC'den okunacak spesifik adresler sisteme dinamik olarak eklenip çıkarılabilmektedir[cite: 10].

![User Roles](UserRoles.png)

### 2. İş Emri (Work Order) ve Operasyon Yönetimi
Üretim süreçlerinin dijitalleştirilmesi ve planlı bir şekilde yürütülmesi, iş emirleri ve bu emirlere bağlı operasyonların hiyerarşik yönetimi ile sağlanmaktadır[cite: 10].

* **İş Emri Oluşturma ve Takibi:** Üretim planlamasının ilk adımı sisteme yeni bir iş emri (Work Order) tanımlamaktır[cite: 10]. İş emirleri listesi ekranında, planlanan üretimlerin başlangıç/bitiş zamanları, kullanılacak hammadde materyalleri ve siparişlerin anlık statüleri şeffaf bir şekilde izlenebilmektedir[cite: 10].
* **Operasyonların Yönetimi ve Makine Atamaları:** İş emirleri oluşturulduktan sonra, bu siparişleri tamamlamak için gereken üretim adımları "Operasyonlar" olarak sisteme girilir[cite: 10]. İlgili ekranda tüm operasyonların sıralaması, atandığı makine, planlanan zaman çizelgesi, mevcut durumları ve üretilen/sağlam/fire miktarları özet halinde listelenmektedir[cite: 10]. Detay ekranında, seçilen bir operasyonun üretim ilerleme yüzdesi, üretilen ve fire verilen parça sayısı görselleştirilmiştir[cite: 10].

![Work Orders](WorkOrders.png)
![Operations Detail](OperasyonDetayMenüsü.png)

### 3. Gerçek Zamanlı PLC Entegrasyonu (SCADA & TIA Portal)
Sistemin en güçlü yanlarından biri, PLC programı (Siemens TIA Portal) ile web arayüzü arasındaki düşük gecikmeli (low-latency) senkronizasyondur[cite: 10].

* TIA Portal'da yazılan Ladder lojiğindeki bir çıkış sinyali (RunningTag), OPC UA üzerinden OpcUaManager servisiyle okunur ve SignalR vasıtasıyla anında frontend'e iletilir[cite: 10].
* Arayüzdeki "ÜRETİMDE" statüsü doğrudan bu fiziksel sinyale bağlıdır[cite: 10].
* PLC içerisindeki CTU (Counter Up) bloğunda sayılan ürün adedi, milisaniyeler içerisinde MES arayüzündeki vardiya üretim miktarı bölümüne yansımaktadır[cite: 10].
* Veritabanına (InfluxDB) yazma ve ekrana basma işlemleri eşzamanlı yürütülür[cite: 10].

![TIA Portal Sync](RunningTIAPortal.png)

### 4. Üretim Raporlama ve Master Data
Sistemde biriken veriler, yöneticiler için anlamlı raporlara dönüştürülür[cite: 10].

* Master Data modülü altında sunulan Üretim Raporu; makine, tarih ve vardiya (Sabah, Öğle, Akşam vb.) bazlı filtreleme sunar[cite: 10].
* Bu sayede hangi vardiyada ne kadar üretim yapıldığı, kaç adet fire verildiği ve toplam çalışma/duruş süreleri tarihsel olarak analiz edilebilir[cite: 10].

![Master Data](MasterData.png)

---

## 🚀 Sonuç
Geliştirilen ViewableMES platformu, ASP.NET Core 9'un modern yetenekleri ve InfluxDB/SignalR ikilisinin gücüyle, sahadaki fiziksel otomasyon sistemlerini (TIA Portal PLC'leri) başarılı bir şekilde buluta/web ortamına taşımıştır[cite: 10]. Hem yönetimsel (rol ve makine tanımlamaları) hem de operasyonel (iş emri ve anlık SCADA izleme) ihtiyaçları tek bir çatı altında çözen bu N-Katmanlı mimari, dijital fabrika vizyonu için sağlam bir altyapı sunmaktadır[cite: 10].
