ikroservis mimarisi kullanan bir **yazılım mimari örneği** olarak **bir e-ticaret sistemi** 

### **E-Ticaret Sistemi (Mikroservis Mimarisi Örneği)**  

E-ticaret platformu, kullanıcıların ürünleri görüntülemesi, sipariş vermesi ve ödemeleri gerçekleştirmesi gibi işlevleri sağlar. Bu işlemleri mikroservis mimarisiyle tasarlayalım.  

#### **Servislerin Yapısı**  
1. **Kullanıcı Yönetim Servisi (User Service)**  
   - **Amaç**: Kullanıcıların kayıt olma, giriş yapma, profil yönetimi gibi işlemlerini gerçekleştirir.  
   - **Bağımsız Veri Tabanı**: Kullanıcı bilgileri (isim, e-posta, şifre vb.) için ayrı bir veritabanı kullanır.  
   - **Teknoloji**: Örneğin Python (Flask) + PostgreSQL.  
   - **API Endpointleri**:  
     - POST `/register`: Kullanıcı kaydı.  
     - POST `/login`: Kullanıcı giriş işlemi.  

2. **Ürün Yönetim Servisi (Product Service)**  
   - **Amaç**: Ürünlerin listelenmesi, detaylarının gösterilmesi ve stok yönetimi işlevlerini sağlar.  
   - **Bağımsız Veri Tabanı**: Ürün bilgileri (isim, fiyat, stok miktarı, açıklama).  
   - **Teknoloji**: Java (Spring Boot) + MongoDB.  
   - **API Endpointleri**:  
     - GET `/products`: Tüm ürünleri listele.  
     - GET `/products/{id}`: Belirli bir ürünün detaylarını getir.  

3. **Sipariş Yönetim Servisi (Order Service)**  
   - **Amaç**: Kullanıcı siparişlerini oluşturur ve yönetir. Siparişlerin durumunu takip eder.  
   - **Bağımsız Veri Tabanı**: Sipariş bilgileri (kullanıcı, ürün, toplam fiyat, sipariş durumu).  
   - **Teknoloji**: Node.js (Express) + MySQL.  
   - **API Endpointleri**:  
     - POST `/orders`: Yeni sipariş oluştur.  
     - GET `/orders/{id}`: Sipariş durumunu sorgula.  

4. **Ödeme Servisi (Payment Service)**  
   - **Amaç**: Ödeme işlemlerini gerçekleştirir ve siparişlere ödeme durumunu ekler.  
   - **Bağımsız Veri Tabanı**: Ödeme bilgileri (kredi kartı, ödeme durumu, toplam tutar).  
   - **Teknoloji**: Go (Golang) + Redis.  
   - **API Endpointleri**:  
     - POST `/payments`: Ödeme işlemini gerçekleştir.  

5. **Bildirim Servisi (Notification Service)**  
   - **Amaç**: Kullanıcıya e-posta veya SMS ile bildirim gönderir (örneğin sipariş onayı).  
   - **Bağımsız Veri Tabanı**: Bildirim kayıtları.  
   - **Teknoloji**: Python (FastAPI) + RabbitMQ.  
   - **API Endpointleri**:  
     - POST `/notify`: Bildirim gönder.  

---

#### **İletişim ve Veri Akışı**
- Mikroservisler **REST API** veya **gRPC** üzerinden birbiriyle haberleşir.  
- **Kuyruk sistemi** (ör. RabbitMQ veya Kafka) sayesinde bazı işlemler asenkron yapılabilir (örneğin sipariş sonrası bildirim gönderimi).  
- Her servis kendi veritabanına sahip olduğundan, veriler loosely coupled bir şekilde yönetilir.  

---

#### **Kullanıcı Akışı (Bir Sipariş Örneği)**  
1. **Kullanıcı**, "Ürün Listesi" sayfasına gelir.  
   - Kullanıcı tarayıcısı **Ürün Yönetim Servisi**'ne bir GET isteği gönderir ve ürünler listelenir.  

2. Kullanıcı bir ürünü seçip sepete ekler ve siparişi tamamlar.  
   - Tarayıcı, **Sipariş Yönetim Servisi**'ne POST isteği göndererek siparişi oluşturur.  

3. Sipariş oluşturulunca, **Ödeme Servisi** çağrılır ve ödeme işlemi yapılır.  
   - Eğer ödeme başarılıysa, sipariş durumu güncellenir.  

4. Sipariş tamamlandığında, **Bildirim Servisi** kullanıcıya bir onay e-postası gönderir.  

---

#### **Mimari Avantajları**
1. **Bağımsız Geliştirme**: Örneğin, ödeme sistemini değiştirmek istediğinizde yalnızca Ödeme Servisi güncellenir.  
2. **Esneklik**: Ürün servisi çok yoğun kullanılınca yalnızca o servisin ölçeklendirilmesi sağlanabilir.  
3. **Hata İzolasyonu**: Bildirim servisi hata alsa bile sipariş oluşturma işlemi devam eder.  
4. **Teknoloji Çeşitliliği**: Her servis en uygun teknoloji ile geliştirilebilir (örneğin Go, Python, Java).  

---

#### **Mikroservis İlişkileri Diyagramı**  

```
[User Service] -> [Order Service] -> [Payment Service]
                        |                    |
                [Product Service]      [Notification Service]
```  

Bu mimari, gerçek dünya e-ticaret sistemleri (Amazon, eBay gibi) tarafından kullanılmaktadır. 
