# langet-translations

[Langet](https://github.com/doukankurt/langet) uygulamasının oyun çeviri paketleri deposu. Bu repo **private**'tır; Langet uygulaması buradan GitHub API üzerinden (kişisel erişim token'ı ile) `catalog.json` dosyasını ve oyun paketlerini indirir.

## Yapı

```
catalog.json                       -> Tüm oyunların ve dillerin indeksi
games/
  <gameSlug>/
    <langCode>/
      manifest.json                -> Paket meta verisi + değiştirilecek dosya listesi
      package.zip                  -> Oyun dizinine kopyalanacak dosyalar (aynı klasör yapısıyla)
```

## Yeni bir oyun çevirisi eklemek

1. `games/_template` klasörünü kopyala: `games/<gameSlug>/<langCode>/`
   - `gameSlug`: oyunun kısa kimliği (örn. `watch_dogs`). Steam oyunları için Steam AppID de eşleştirme için kullanılabilir.
   - `langCode`: ISO dil kodu (`tr`, `en`, ...)
2. `manifest.json` dosyasını doldur:
   - `files`: `package.zip` içindeki, oyunun kurulum dizinine göre **göreli** dosya yollarının listesi (örn. `Data/Localization/tr.dat`).
3. Değiştirilecek dosyaları, oyunun kurulum dizinindeki gerçek klasör yapısını birebir koruyarak `package.zip` içine koy.
4. `catalog.json` içindeki `games` dizisine oyunu/dili ekle (yoksa oyunu da ekle):

```json
{
  "steamAppId": 447040,
  "slug": "watch_dogs",
  "name": "Watch Dogs",
  "languages": [
    {
      "code": "tr",
      "label": "Türkçe",
      "version": "1.0.0",
      "manifest": "games/watch_dogs/tr/manifest.json",
      "package": "games/watch_dogs/tr/package.zip"
    }
  ]
}
```

## Uygulama tarafında nasıl çalışır

- **Kurma**: Uygulama `package.zip`'i indirir, `manifest.json`'daki her dosya için oyunun kurulum dizininde önce orijinali `.langet_backup/<langCode>/` altına yedekler, sonra zip'teki dosyayla değiştirir.
- **Kaldırma**: `.langet_backup/<langCode>/` altındaki orijinal dosyalar geri yüklenir ve yedek klasörü silinir.
- Bir oyunda aynı anda yalnızca bir dil paketi aktif olacak şekilde tasarlanmıştır (yeni dil kurulmadan önce mevcut dil otomatik kaldırılır), çünkü aynı dosyaların üstüne art arda yama yapmak bozulmaya yol açabilir.
