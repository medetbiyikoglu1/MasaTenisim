# MasaTenisim - Geliştirme Notları ve Proje Durumu

**Son Güncelleme:** 16 Eylül 2026  
**Durum:** 🟢 Veritabanı ve Çekirdek Sistem Canlı / Web Admin Aktif

---

## 1. Veritabanı Entegrasyonu (Neon.tech Bulut PostgreSQL)

- **Sağlayıcı:** [Neon.tech](https://neon.tech) (Serverless PostgreSQL)
- **Proje Adı:** `masatenisim`
- **Bağlantı Türü:** Connection Pooler üzerinden SSL korumalı erişim
- **Yapılandırma Dosyaları:**
  - `packages/database/.env`: Migration ve veritabanı araçları için
  - `apps/admin/.env.local`: Next.js Admin Paneli için
  - Kök dizindeki `.env`: Genel monorepo ortamı için
- **Migration Betiği:**
  - `packages/database/migrate.js` geliştirildi.
  - `package.json` içerisine `npm run migrate` komutu eklendi.

### Aktarılan ve Doğrulanan Veritabanı Nesneleri:
* **Toplam Tablo Sayısı:** 15 adet ilişkisel tablo
  1. `users`: Kullanıcı kimlik, rol ve Apple onayına uyumlu **Soft-Delete (`deleted_at`)** yapısı.
  2. `player_profiles`: ELO puanları, K-faktörü, maç istatistikleri ve opsiyonel ekipmanlar (Tahta, Lastikler).
  3. `tournaments`: Turnuvalar (Yaş kısıtlamaları `min_age`, `max_age`, masa sayıları, statüler).
  4. `tournament_groups`: 4'erli grup organizasyonu.
  5. `tournament_participants`: Turnuva kayıtları, seribaşı bilgileri.
  6. `group_standings`: Grup puan durumu, galibiyet/mağlubiyet, averajlar.
  7. `matches`: Çift taraflı onay bekleyen maçlar ve maç tipleri (`tournament`, `friendly_ranked`, `friendly_casual`).
  8. `match_sets`: Best of 5 set skorları.
  9. `elo_history`: Silinemez ELO hareket denetim tablosu.
  10. `profile_change_requests`: Kademeli yaş doğrulama ve Admin onaylı sahtecilik önleme talepleri.
  11. `device_tokens`: Mobil cihaz FCM/APNS bildirim tokenları.
  12. `push_campaigns`: Hedefli turnuva ve etkinlik push bildirim kayıtları.
  13. `matches_history`: Skor veya statü değiştiğinde otomatik devreye giren audit log tablosu.
  14. `player_profiles_history`: Profil veya ELO değiştiğinde otomatik log tablosu.
  15. `tournaments_history`: Turnuva değişiklikleri log tablosu.

* **PL/pgSQL Audit Trigger'ları:**
  - `trg_matches_audit_change`: Maç güncellemelerini yakalar.
  - `trg_player_profiles_audit_change`: Profil/skor güncellemelerini yakalar.
  - `trg_tournaments_audit_change`: Turnuva değişikliklerini yakalar.

* **Yüklenen Başlangıç Seed Verileri:**
  - 6 Kullanıcı (1 Süper Admin, 1 Hakem, 4 farklı seviye ve yaşta Oyuncu)
  - 4 Oyuncu Profili (Butterfly Viscaria, Dignics/Tenergy, Donic vb. ekipmanlı ve gizli ekipmanlı)
  - 2 Turnuva (Cuma Akşamı Açık Kupası & Marmara +40 Veteranlar Turnuvası)

---

## 2. Çekirdek Motor (Core Package) & Test Durumu

- **Konum:** `packages/core/`
- **Algoritmalar:**
  - K=32 (ilk 10 maç için) ve K=16 (tecrübeli oyuncular için) dinamik ELO algoritması.
  - Turnuva maçları için **1.5x katsayı** çarpanı.
  - ELO Seviye Rehberi (Başlangıç, Orta, İleri, Yarı-Pro/Elit seviye hesaplamaları).
  - 4'erli grup seribaşı kura dağıtım algoritması (1. ve 2. seribaşıların farklı gruplara gitmesi).
  - Best of 5 set averaj ve puan sıralama motoru.
- **Birim Testleri:**
  - `8 pass, 0 fail` (12ms içinde tamamlanmaktadır).
  - Test çalıştırma komutu: `cd packages/core && node --experimental-strip-types tests/elo.test.ts`

---

## 3. Uygulama Servisleri Durumu

| Servis | Teknoloji | Port / Durum | Açıklama |
| :--- | :--- | :--- | :--- |
| **Admin Web Paneli** | Next.js 14, TailwindCSS, Lucide | `http://localhost:3000` (Aktif) | Turnuva sihirbazı, Yaş Onay Modülü, ELO Seviye Rehberi |
| **Mobil Uygulama** | React Native, Expo, TypeScript | `apps/mobile/` (Hazır) | `npm run dev:mobile` ile Expo başlatılabilir |
| **Veritabanı** | Neon.tech Serverless PostgreSQL | Bulut (Canlı) | 15 Tablo + Trigger'lar + Demo Veriler aktif |

---

## 4. Hızlı Başlatma ve Yönetim Komutları

PowerShell veya CMD terminalinde (`C:\Users\medet\.gemini\antigravity\scratch\MasaTenisim` dizininde):

```powershell
# 1. Admin Web Panelini Başlatma
npm run dev:admin
# Tarayıcıdan: http://localhost:3000

# 2. Mobil Uygulamayı (Expo) Başlatma
npm run dev:mobile
# Expo QR kodunu telefondan taratarak veya emülatörde çalıştırabilirsiniz

# 3. Çekirdek Birim Testlerini Çalıştırma
npm run test

# 4. Veritabanı Migration & Seed Yenileme
cd packages/database
npm run migrate
```

---

## 5. Sıradaki Geliştirme Adımları

1. **Admin Panelinde Canlı Veritabanı Entegrasyonu:**
   - Admin panelindeki oyuncu listesi ve turnuva sayfalarının doğrudan Neon.tech veritabanından veri çekmesini sağlamak.
   - Yaş onay taleplerinin (`profile_change_requests`) veritabanı üzerinden onaylanıp/reddedilmesi.
2. **Mobil Ekranların Canlıya Alınması:**
   - Turnuva kayıt ekranı ve kademeli yaş onay modalının mobil arayüzde test edilmesi.
   - Best of 5 maç giriş ve skor onay ekranı.
