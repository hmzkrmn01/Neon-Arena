# 🎮 NEON ARENA — Google Play Yayın Rehberi

Oyun **PWA (Progressive Web App)** olarak tamamen hazır. Google Play'e yüklemek için
oyunu bir **TWA (Trusted Web Activity)** ile Android paketine (.aab) dönüştüreceğiz.

---

## 1. Oyunu Yayına Al (Hosting)

Build klasörünü (`dist/`) herhangi bir HTTPS hostinge yükle:

```bash
npm run build      # dist/ klasörünü üretir
```

`dist/` içeriği:
- `index.html` (oyunun tamamı tek dosyada)
- `manifest.webmanifest`
- `sw.js` (çevrimdışı oynama)
- `icon-192.png`, `icon-512.png`

Ücretsiz seçenekler: **Netlify**, **Vercel**, **Cloudflare Pages**, **Firebase Hosting**, **GitHub Pages**.

> ⚠️ Google Play TWA için adres **mutlaka HTTPS** olmalı. Örn: `https://neonarena.example.com`

---

## 2. Android Paketi Oluştur (Bubblewrap)

```bash
npm install -g @bubblewrap/cli

bubblewrap init --manifest https://SENIN-ADRESIN/manifest.webmanifest
# Sorulara yanıt:
#   Application name:        Neon Arena
#   Short name:              Neon Arena
#   Application ID:          com.seninsirketin.neonarena
#   Display mode:            fullscreen
#   Orientation:             any (veya landscape)
#   Status bar color:        #05060f
#   Include support for Play Billing: no

bubblewrap build
```

Çıktı: `app-release-bundle.aab` ← Play Console'a yükleyeceğin dosya.

**Alternatif:** [PWABuilder.com](https://www.pwabuilder.com) adresine sitenin URL'sini gir,
"Package for stores → Android" de, `.aab` dosyasını indir. (Kod yazmadan en kolay yol.)

---

## 3. Digital Asset Links (Adres Çubuğunu Gizler)

Bubblewrap sana bir `assetlinks.json` üretir. Bunu siteye koy:

```
https://SENIN-ADRESIN/.well-known/assetlinks.json
```

Bu dosya olmadan oyun açıldığında üstte tarayıcı adres çubuğu görünür.

---

## 4. Play Console Adımları

1. [play.google.com/console](https://play.google.com/console) → hesap aç (tek seferlik **25 USD**)
2. **Uygulama oluştur** → Ad: `Neon Arena`, Tür: **Oyun**, Kategori: **Aksiyon / Arcade**
3. **Üretim → Yeni sürüm oluştur** → `.aab` dosyasını yükle
4. Gerekli içerikleri doldur:

| Alan | Gereksinim |
|---|---|
| Uygulama simgesi | 512×512 PNG (`icon-512.png` hazır ✅) |
| Öne çıkan grafik | 1024×500 PNG |
| Telefon ekran görüntüsü | En az 2 adet (menü + oyun içi) |
| Kısa açıklama | Maks. 80 karakter |
| Tam açıklama | Maks. 4000 karakter |
| Gizlilik politikası | Zorunlu — bir URL gerekli |

### Hazır Mağaza Metinleri

**Kısa açıklama (80 karakter):**
```
Neon sürülere karşı hayatta kal! Dalga tabanlı 2D arcade aksiyon arena oyunu.
```

**Tam açıklama:**
```
⚡ NEON ARENA — Retro-fütüristik bir arenada hayatta kalma savaşı!

Sonsuz dalgalar halinde gelen neon düşman sürülerine karşı savaş, kombo yap,
coin topla ve gemini özelleştir.

🎯 ÖZELLİKLER
• Çift joystick kontrol — mobil için optimize, görünmez tuş modu dahil
• 8 farklı düşman tipi + dev GARDİYAN boss savaşları
• 5 özel yetenek: Nova Patlaması, Zaman Bükücü, Şimşek Fırtınası, Kara Delik, Koruyucu Dronlar
• 4 silah: Blaster, Çift Namlu, Taramalı, Plazma Topu
• 8 karakter deseni: Türk Bayrağı, Alev, Galaksi, takım logoları ve daha fazlası
• 11 mermi rengi (gökkuşağı dahil)
• Her dalgada değişen renk paletleri — 8 farklı arena teması
• Ödül sandıkları, coin ekonomisi ve TOP 10 skor tablosu
• Ayarlanabilir tuş düzeni, solak modu, hassasiyet ayarı
• Çevrimdışı oynanabilir — internet gerekmez

🏆 Rekorunu kır, arkadaşlarını geç, arenanın efsanesi ol!
```

**İçerik derecelendirmesi:** Fantastik şiddet içerir → genelde **7+ / PEGI 7**

---

## 5. Veri Güvenliği Formu

Oyun **tüm verileri cihazda** (`localStorage`) saklar, sunucuya hiçbir şey göndermez.

Play Console → **Veri güvenliği** bölümünde şunu işaretle:
- ✅ "Bu uygulama kullanıcı verilerini toplamıyor veya paylaşmıyor"

> Oyun içindeki "Google Play hesabı bağla" özelliği şu an **simülasyondur** (yerel yedek).
> Gerçek bulut kaydı istersen `src/game/meta.ts` içindeki `linkGooglePlay()` ve `syncNow()`
> fonksiyonlarını [Play Games Services Saved Games](https://developer.android.com/games/pgs/savedgames)
> API'sine bağlaman yeterli — arayüz zaten hazır.

---

## 6. Uygulama İçi Satın Alma — Reklamsız Sürüm (50 ₺)

Oyunda **tek seferlik satın alma** arayüzü hazır: ana menüde sağ üstteki **REKLAMSIZ** butonu.

### Play Console Tarafı
1. **Para kazanma → Ürünler → Uygulama içi ürünler → Ürün oluştur**
2. Bilgileri gir:

| Alan | Değer |
|---|---|
| Ürün kimliği (SKU) | `neon_arena_remove_ads` |
| Ad | Reklamsız Sürüm |
| Açıklama | Tüm reklamları kaldırır ve 500 bonus coin verir. Tek seferlik ödeme. |
| Fiyat | **50,00 TRY** |
| Durum | Etkin |

> SKU, kodda `src/game/meta.ts` içindeki `PREMIUM_SKU` sabitiyle birebir aynı olmalı.
> Fiyatı değiştirmek istersen `PREMIUM_PRICE_TRY` değerini güncelle.

### Kod Tarafı (Play Billing bağlama)
`src/components/PremiumModal.tsx` içindeki `buy()` fonksiyonu şu an **simülasyon**.
Gerçek satın alma için Capacitor + Play Billing kullan:

```bash
npm install @capacitor/core @capacitor/android
npm install @capacitor-community/in-app-purchases
npx cap init && npx cap add android
```

```ts
// PremiumModal.tsx -> buy()
const { transaction } = await InAppPurchases.purchaseProduct({
  productIdentifier: PREMIUM_SKU,
});
if (transaction?.transactionState === "purchased") onPurchase(); // grantPremium() çalışır
```

`onRestore` butonu için `InAppPurchases.restorePurchases()` çağırıp sonucu
`restorePremium(meta, owned)` fonksiyonuna ver.

### Premium Kullanıcıda Ne Değişiyor?
| Özellik | Ücretsiz | Premium |
|---|---|---|
| Oyun sonu geçiş reklamı | Var (2. turdan itibaren) | **Yok** |
| Sandık açma | 8 sn reklam | **Anında** |
| Coini 2 katla | 8 sn reklam | **Anında** |
| Canlanma | 8 sn reklam | **Anında** |
| Mağaza +50 coin | 8 sn reklam | **Anında** |
| Hoşgeldin bonusu | — | **500 coin** |

Premium durumu yedek koduna ve hesap senkronuna dahil edilir — cihaz değişse de kaybolmaz.

---

## 7. Reklamları Gerçek Hale Getirme

Şu an `src/components/AdModal.tsx` **simüle edilmiş** bir reklam gösteriyor.
Gerçek gelir için AdMob bağla:

1. [AdMob](https://admob.google.com) hesabı aç → "Ödüllü" (Rewarded) reklam birimi oluştur
2. TWA içinde reklam göstermek için projeyi **Capacitor**'a taşı:
   ```bash
   npm install @capacitor/core @capacitor/android
   npm install @capacitor-community/admob
   npx cap init && npx cap add android
   ```
3. `AdModal` bileşenindeki sahte sayaç yerine `AdMob.showRewardVideoAd()` çağır,
   ödül callback'inde mevcut `onReward()` fonksiyonunu tetikle.

Oyundaki 3 reklam noktası hazır:
- Mağaza → **+50 Coin**
- Oyun sonu → **Sandık Aç**
- Oyun sonu → **Coini 2 Katla** / **Canlan**

---

## ✅ Yayın Öncesi Kontrol Listesi

- [ ] `npm run build` çalışıyor
- [ ] Site HTTPS üzerinden yayında
- [ ] `manifest.webmanifest` erişilebilir
- [ ] `assetlinks.json` `.well-known/` altında
- [ ] `.aab` dosyası oluşturuldu
- [ ] Gizlilik politikası URL'si hazır
- [ ] 2+ ekran görüntüsü + 1024×500 öne çıkan grafik
- [ ] İçerik derecelendirme anketi dolduruldu
- [ ] Veri güvenliği formu dolduruldu

İyi şanslar! 🚀
