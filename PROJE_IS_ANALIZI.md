# MasaTenisim - Proje İş Analizi ve Detaylı Gereksinim Dokümanı (SRS)

**Proje Adı:** MasaTenisim  
**Tarih:** 16 Eylül 2026  
**Rol:** Kıdemli İş Analisti (Senior Business Analyst)  
**Doküman Sürümü:** v1.2 (Mobil Teknoloji Kilidi & Store Onay/Yayın Rehberi)

---

## 1. Sürüm Kapsam Matrisi (Scope Breakdown)

| Modül / Özellik | Sürüm | Kapsam Detayı |
| :--- | :---: | :--- |
| **Grup + Eleme Turnuva Motoru** | **v1** | 4'erli gruplar, ilk 2 üst tura, Best of 5 set kuralı |
| **Dinamik ELO / Ranking Motoru** | **v1** | K=32/16 faktörü, turnuva 1.5x katsayısı, pasiflik kuralı |
| **Admin Esnek ELO Atama & Rehber**| **v1** | Adminin özel puan girebilmesi + seviye rehberi uyarısı |
| **Admin Hızlı Operasyon Paneli** | **v1** | Excel/CSV toplu aktarım, turnuva sihirbazı, audit log |
| **Oyuncu Kartı & İstatistikler** | **v1** | H2H, galibiyet oranı, form; **Ekipmanlar opsiyonel** |
| **Liderlik Tabloları (Leaderboard)**| **v1** | Genel, şehir ve kulüp bazlı filtreleme |
| **Maç Türü Seçimi (Ranked vs Fun)**| **v1** | İki oyuncu maç yaparken "ELO'lu" veya "Eğlencesine" seçebilecek |
| **Hedefli Push Bildirimleri** | **v1** | Turnuva duyurusu & etkinlik çağrısı (Örn: Cuma 19:00 turnuvası) |
| **Salonda TV / Projeksiyon Modu**| **v2** | Canlı skorboard ve duvara yansıtılabilir fikstür ekranı |
| **Salon ve Masa Rezervasyon Modülü**| **v2** | Harita bazlı salon arama, saatlik kiralama, online ödeme |
| **Topluluk, Blog & Eğitim Alanı** | **v2** | Eğitici makaleler, teknik videolar, forum ve kullanıcı yorumları |

---

## 2. Teknoloji Yığını (Kesinleşen Tercih)

- **Mobil Uygulama:** React Native (Expo SDK) + TypeScript + NativeWind (Tailwind CSS)
- **Web Admin Paneli:** Next.js (React) + TypeScript + Tailwind CSS + Shadcn UI
- **Backend Servisleri:** Node.js (NestJS / Fastify) veya Python (FastAPI) + PostgreSQL
- **Bulut & Dağıtım:** Expo EAS (Expo Application Services) + Vercel / Docker

---

## 3. App Store & Google Play Store Onay Rehberi (Reddi Önleme Stratejisi)

Apple ve Google mağazalarında ilk seferde ve en hızlı şekilde onay almak için v1 tasarımına entegre edilen kritik kurallar:

### 3.1. Apple App Store Kritik Gereksinimleri
1. **Hesap Silme Butonu (Guideline 5.1.1(v) - En Sık Red Sebebi):**
   - Uygulamada kullanıcı hesabı oluşturulabiliyorsa, profil ayarlarında mutlaka *"Hesabımı ve Verilerimi Sil"* butonu yer almalıdır. Bu buton doğrudan çalışmalı veya talebi anında işlemelidir.
2. **Apple ile Giriş Yap (Sign in with Apple - Guideline 4.8):**
   - Eğer uygulamaya "Google ile Giriş Yap" eklenirse, Apple zorunlu olarak "Apple ile Giriş Yap" butonunu da ister.
   - *Tavsiye edilen en temiz yöntem:* Başlangıçta Telefon Numarası (SMS/OTP) veya E-posta + Şifre ile giriş yapmak. Böylece bu zorunluluk bertaraf edilir.
3. **Dolu Test Hesabı (Guideline 2.1):**
   - Apple inceleme ekibine (Reviewer) verilecek test hesabı kesinlikle bomboş olmamalıdır. İçerisinde tamamlanmış maçlar, ELO geçmişi ve kayıtlı bir turnuva olan hazır bir demo hesap tanımlanmalıdır.
4. **Gizlilik Politikası (Privacy Policy) & Şartlar:**
   - Web üzerinde erişilebilir, geçerli bir Gizlilik Sözleşmesi URL'i gereklidir.
5. **İzin İsteme Zamanlaması:**
   - Bildirim (Push Notification) izni uygulama açılır açılmaz ekrana fırlatılmamalı; kullanıcı oturum açtıktan sonra *"Turnuva bildirimlerini kaçırmamak için bildirimlere izin ver"* şeklinde açıklayıcı bir ara ekranla sorulmalıdır.

### 3.2. Google Play Store Kritik Gereksinimleri
1. **20 Test Kullanıcısı / 14 Gün Kuralı (Bireysel Geliştirici Hesapları):**
   - Google, Kasım 2023'ten sonra açılan *bireysel* hesaplarda uygulamanın yayına çıkabilmesi için en az 20 kişinin 14 gün boyunca kapalı teste (Closed Testing) katılmasını şart koşuyor.
   - *Çözüm Stratejisi:* İnceleme süresini beklemeden önce masa tenisi topluluğundan/arkadaş çevresinden 20 kişilik bir test grubu oluşturulmalı veya süreç kurumsal (şirket) Play Console hesabı üzerinden yürütülmelidir.
2. **Veri Güvenliği Formu (Data Safety):**
   - Hangi verilerin toplandığı (İsim, Telefon, E-posta) konsolda eksiksiz beyan edilmelidir.

---

## 4. En Hızlı ve Sağlıklı Yayın Süreci: Expo EAS (Expo Application Services)

React Native + Expo tercih etmemizin mağaza yayınındaki en büyük avantajı **EAS (Expo Application Services)** altyapısıdır:

```
[React Native + TypeScript Kodu]
              │
              ▼
[eas build --platform all] (Bulutta Mac gerektirmeden iOS .ipa ve Android .aab üretimi)
              │
              ▼
[eas submit --platform all] (Tek komutla App Store Connect ve Google Play Console'a yükleme)
              │
              ▼
[eas update] (Mağaza onayını beklemeden anında arayüz/hata güncellemesi gönderme)
```

- **Mac Bilgisayar Bağımsızlığı:** iOS derlemesi (Build) için pahalı bir Mac gerekmez; Expo bulut sunucularında iOS paketini imzalar ve üretir.
- **Sertifika Otomasyonu:** Apple Developer sertifikalarını ve Google Keystore anahtarlarını Expo EAS otomatik yönetir; sertifika karmaşasını sıfırlar.

---

## 5. Admin Paneli & Turnuva Mimarisi (Kayıt Altına Alınanlar)
- 4'erli gruplar, Best of 5 maç formatı, ilk 2 üst tura (Ana Tablo).
- Admin dinamik seviye rehberi ile esnek ELO girişi.
- Excel / CSV toplu oyuncu yükleme şablonu.
- Çift taraflı onay mekanizmalı Ranked / Casual ikili maçlar.
- Şehir & ELO hedefli push bildirim merkezi.
- **Doğum Tarihi & Yaş Kategori Motoru:** Kullanıcılarda `birth_date` tutulması; turnuvalarda yaş filtreleri (`min_age`, `max_age` - Örn: U18 Gençler, +40 Veteranlar) ve cinsiyet kısıtlamaları (`open`, `men`, `women`).
- **Kademeli Profil Tamamlama (Progressive Profiling - Yaş Doğrulama):**
  - Kayıt esnasında doğum tarihi **zorunlu değildir** (kayıt sürtünmesini sıfıra indirmek için).
  - Yaş sınırı olmayan genel turnuvalara yaş girmeden doğrudan katılabilir.
  - Eğer yaş sınırı olan bir turnuvaya (`min_age` veya `max_age` dolu) yaş girmemiş bir kullanıcı başvurursa:
    1. Sistem uyarı modalı açar: *"Bu turnuva [X - Y] yaş aralığı için uygundur. Başvurabilmek için lütfen doğum tarihinizi girin."*
    2. Modaldaki buton kullanıcıyı doğrudan **Profil Bilgilerini Güncelleme** ekranına yönlendirir.
    3. Doğum tarihi kaydedildiği an sistem yaşı kontrol eder; uygunsa turnuvaya kaydını onaylar, uygun değilse neden katılamayacağını açıklar.
- **Doğum Tarihi Suistimal Önleme & Admin Onayı (Anti-Fraud Kuralı):**
  - **İlk Giriş (1. Kez):** Kullanıcı ilk kez doğum tarihi girdiğinde sistem anında kaydeder (Otomatik onay).
  - **İkinci ve Sonraki Değişiklikler:** Oyuncu doğum tarihini bir kez girdikten sonra tekrar değiştirmek isterse doğrudan güncelleyemez. Ekrana: *"Doğum tarihinizi daha önce belirttiniz. Turnuva adaleti gereği tekrar değiştirmek için admin onayı gerekmektedir"* uyarısı çıkar.
  - Sistem bir **Doğum Tarihi Güncelleme Talebi (`profile_change_requests`)** oluşturur ve Admin paneline düşürür. Admin onaylayana kadar mevcut yaş geçerli kalır.

---

## 6. Veritabanı Mimarisi (PostgreSQL)

Detaylı SQL tabloları, ilişkiler, ENUM tipleri ve performans indeksleri için oluşturulan dokümana bakınız:  
📄 **Veritabanı Şeması:** [VERITABANI_MIMARISI.md](file:///C:/Users/medet/OneDrive/Masa%C3%BCst%C3%BC/MasaTenisim/VERITABANI_MIMARISI.md)
