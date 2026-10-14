# Adanalım — QR ile masadan sipariş

Adanalım, restoran ve kafeler için masadan sipariş sistemidir. Misafir masadaki QR kodu okutur, fotoğraflı menüden seçer, siparişini gönderir ve "yaklaşık 20 dk" yazısını görür. Sipariş mutfak/kasa ekranına yazılı düşer. Hesap sonunda garsona ya da kasaya ödenir.

Misafir uygulama indirmez, hesap açmaz; her telefonun tarayıcısında çalışır.

**Canlı demo:** https://demirciege349.github.io/self-service-s-ra-rahatlatma-/
Telefonla açın, sipariş verin, üstteki **Mutfak / kasa** düğmesiyle siparişin düştüğünü görün. (Demo tek cihazda çalışır; veriler o telefonda kalır.)

---

## Misafir ne görür?

- **Fotoğraflı menü**, kategori şeridi
- Her yemekte **ortalama hazırlanma süresi**, **porsiyon miktarı** (ör. 180 gr et, 300 ml), **yaklaşık kalori** ve **alerjenler**
- **Alerjen filtresi:** "Süt içermesin" seçilince ilgili yemekler işaretlenir
- **1 / 1,5 porsiyon** seçimi; gram ve kalori kendiliğinden 1,5 katına çıkar
- **Porsiyonu masada gör (3D):** yemek, telefon kamerasıyla masanın üstünde gerçek boyutunda görünür
- Sipariş notu ("acısız"), gönderince **tahmini süre**; menüye dönünce üstte **kalan dakika** şeridi
- Her ekranda **Garson çağır**, hesap ekranında **Hesap iste**

## İşletme ne görür?

**Kasa / mutfak ekranı** (tablet ya da bilgisayar, şifreli):
- Gelen siparişler: masa, saat, not, kalan süre (geciken kırmızı), **Hazırlandı**
- **Masalar:** açık hesaplar, hesap isteyen masa kırmızı, **Ödeme alındı · masayı kapat**, **Masayı taşı**
- **Garson siparişi:** QR kullanmayan misafir için
- **Menü:** **Tükendi** (tek dokunuş), fiyat ve süre değişikliği (patron PIN'i, kayıtlı), **Basılı fiyat listesini yazdır**
- **Rapor:** sipariş sayısı, QR/garson oranı, ciro, masa başı hesap, hazırlanma süresi, en çok satanlar, yoğun saatler
- Yeni sipariş, garson çağrısı ve hesap isteğinde ses

**Kurulum ekranı:** menü editörü (fotoğraf, fiyat, porsiyon, kalori, süre, alerjenler, 3D model, sıralama), masa QR kartları (PNG / yazdır), PIN, kasa hesapları.

## Teknik yapı

- Saf HTML, CSS ve JavaScript; derleme adımı yok
- Gerçek sürüm: **Firebase** (Firestore + Authentication + Hosting). Misafir anonim oturumla, işletme e-posta/şifreyle girer; yetkiler sunucu tarafındaki güvenlik kurallarıyla korunur
- 3D görüntüleme: Google **model-viewer** (Android'de Scene Viewer, iPhone'da AR Quick Look)
- Bu repodaki `yayin` dalı yalnızca tek cihazlık demoyu içerir; çok cihazlı gerçek sürüm ayrı paket olarak kurulur

## Dosyalar (`yayin` dalı)

| Dosya | Ne |
|---|---|
| `index.html` | Telefon demosu (misafir + mutfak/kasa) |
| `tanitim.html` | İşletme sahiplerine tanıtım sitesi |
| `site.html` | Örnek restoran web sitesi şablonu |
| `models/adana.glb` | Gerçek boyutlu örnek 3D tabak (30 cm) |
| `manifest.webmanifest`, `icon.svg` | "Ana ekrana ekle" için uygulama bilgileri |

## Bilinen sınırlar

- Masadan online ödeme ve adisyon programı bağlantısı henüz yok
- Kalori, gramaj ve alerjen bilgilerini işletme girer; menüde yaklaşık olduğu belirtilir
- Basılı fiyat listesi yönetmelik gereği masada ve girişte bulunmalıdır; sistem bu listeyi tek tuşla üretir

---

Eskişehir · 2026
