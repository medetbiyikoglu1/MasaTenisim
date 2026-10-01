# MasaTenisim - Yazılım Kod Deposu Konumu

Projenin monorepo kaynak kodları Windows OneDrive dosya kilitlemelerini önlemek amacıyla aşağıdaki yerel dizine kurulmuştur:

📂 **Proje Kaynak Kodu:** `C:\Users\medet\.gemini\antigravity\scratch\MasaTenisim`

### Dizin İçeriği:
1. `packages/core/`: ELO derecelendirme algoritması, Best of 5 maç motoru, kura çekimi ve TypeScript tipleri.
2. `packages/database/`: PostgreSQL migration dosyaları, otomatik trigger'lar ve demo seed datası.
3. `apps/mobile/`: React Native (Expo) mobil uygulaması (Turnuva listesi, Liderlik tablosu, Maç onayı, Profil & Apple hesap silme).
4. `apps/admin/`: Next.js 14 Web Admin Paneli (KPI dashboard, yaş onay talepleri, turnuva kura sihirbazı, dinamik ELO uyarılı oyuncu yönetimi).

### Test ve Servis Çalıştırma Komutları:
PowerShell veya CMD terminalinde (`C:\Users\medet\.gemini\antigravity\scratch\MasaTenisim` dizininde):

```powershell
# 1. Çekirdek Birim Testlerini Çalıştırma (ELO & Turnuva Motoru)
npm run test
# (8 adet birim testi 12ms içinde başarıyla tamamlanmaktadır)

# 2. Neon.tech Veritabanı Migration & Seed Aktarımı
cd packages/database
npm run migrate

# 3. Next.js 14 Admin Web Panelini Başlatma (http://localhost:3000)
npm run dev:admin

# 4. React Native (Expo) Mobil Uygulamasını Başlatma
npm run dev:mobile
```

---

📄 **Detaylı Notlar & Canlı Sistem Durumu:** [GELISTIRME_NOTLARI.md](file:///c:/Users/medet/OneDrive/Masa%C3%BCst%C3%BC/MasaTenisim/GELISTIRME_NOTLARI.md)
