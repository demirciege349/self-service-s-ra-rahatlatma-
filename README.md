# Sırala — QR ile sipariş ve canlı sıra takibi

Sırala, garsonsuz çalışan self-servis kafeler için bir sipariş ve sıra sistemidir. Müşteri masadaki QR kodu okutur, menüden siparişini seçer, sıra numarasını alır ve masasına oturur. Sırası telefonunda canlı ilerler; sırası gelince kasaya gider, sadece ödemesini yapar.

Müşteri uygulama indirmez, hesap açmaz. Sistem her telefonun tarayıcısında çalışır.

> **Kısaca:** Ayakta kuyruk biter, kasa hızlanır, müşteri oturarak bekler.

---

## İçindekiler

1. [Çözdüğü sorun](#çözdüğü-sorun)
2. [Nasıl çalışır](#nasıl-çalışır)
3. [Müşteri ekranı](#müşteri-ekranı)
4. [Kasa ekranı](#kasa-ekranı)
5. [Sıra mantığı](#sıra-mantığı)
6. [Siparişe giriş yolları](#siparişe-giriş-yolları)
7. [İşletme ayarları](#işletme-ayarları)
8. [İnternet kesilirse](#internet-kesilirse)
9. [Neler değişir, neler aynı kalır](#neler-değişir-neler-aynı-kalır)
10. [Görsel dil](#görsel-dil)
11. [Dosya yapısı](#dosya-yapısı)
12. [Demoyu çalıştırma](#demoyu-çalıştırma)
13. [Kodda değişiklik yapma](#kodda-değişiklik-yapma)
14. [Teknik mimari (gerçek sürüm)](#teknik-mimari-gerçek-sürüm)
15. [Bilinen sınırlar](#bilinen-sınırlar)
16. [Yol haritası](#yol-haritası)

---

## Çözdüğü sorun

Yoğun saatlerde kasanın önünde uzun ayakta kuyruk oluşur. Kasiyer her müşterinin siparişini tek tek dinleyip girerken sıra durur. Müşteri kuyrukta beklerken masa bulamaz, kafe kalabalık ve dağınık görünür.

Sırala siparişi kasadan önce, masada aldırır. Kasiyer siparişi dinlemez; ekranında hazır görür ve sadece ödemeyi alır. Böylece kasa daha hızlı akar ve bekleme ayakta değil, masada geçer.

Sırala baristanın hızını değiştirmez. İçecekler aynı hızda hazırlanır; değişen, bekleme süresinin nerede ve nasıl geçtiğidir.

---

## Nasıl çalışır

1. **Okut.** Müşteri masadaki QR kodu telefonunun kamerasıyla okutur. Sayfa açılır, masa numarası otomatik gelir.
2. **Seç.** Menüden ürünlerini seçer, ödeme şeklini (Kart / Nakit) belirler.
3. **Numara al.** Sıra numarasını alır (ör. 160) ve masasına oturur.
4. **Takip et.** Telefonunda canlı görür: *Senin numaran 160 · Şu an 145 · Önünde 15.*
5. **Uyarı.** 3 sıra kala telefon ses ve titreşim verir.
6. **Öde.** Sırası gelince kasaya gider. Sipariş kasiyerin ekranında hazırdır; kasiyer sadece ödemeyi alır.
7. **Teslim al.** Ödeme alınınca hazırlık başlar. İçecek tezgâha gelince kasiyer "Hazır"a basar, müşteri telefonunda görür ve tezgâhtan alır.

---

## Müşteri ekranı

**Menü**
- Ürünler kategorilere ayrılmıştır: Kahveler, Soğuk kahveler, Çay ve sıcaklar, Soğuk içecekler, Atıştırmalıklar, Tatlılar.
- Üstteki sekmelerden bir kategoriye dokununca sadece o kategorinin ürünleri listelenir.
- Bir kategoriden ürün seçildiğinde o sekmenin yanında seçilen ürün sayısı görünür.
- Tükenen ürünler üstü çizili ve "Tükendi" yazısıyla görünür, sepete eklenemez.
- Alt çubukta ödeme şekli (Kartla öde / Nakit öde) ve toplam tutarla birlikte "Sıra numarası al" düğmesi bulunur.

**Sıra numarası**
- Büyük sıra numarası, "Şu an" (kasadaki numara) ve "Önünde" (kaç kişi var) bilgisi.
- Durum etiketi: *Sırada → Ödendi · Hazırlanıyor → Hazır.*
- Sipariş özeti: masa, ödeme şekli, ürünler ve toplam.
- **Siparişi değiştir:** müşteri numara aldıktan sonra siparişini değiştirebilir. Numarası aynı kalır, sona atılmaz.
- **İptal et:** onay sorusundan sonra sipariş iptal edilir.

**Uyarılar**
- Önünde 3 ya da daha az kişi kalınca: *"Önünde 2 kişi kaldı. Kasaya doğru gelebilirsin."* + ses + titreşim.
- Sırası gelince: *"Sıran geldi! Kasaya gelebilirsin."*
- Sipariş hazır olunca: *"Siparişin hazır! Tezgâhtan alabilirsin."* + ses.
- Sayfa açıkken ekran kilitlenmez (Wake Lock).

**Kurallar**
- Bir telefonda aynı anda tek aktif numara olabilir. Bu, şaka siparişlerin önüne geçer.
- Sayfa kapatılıp QR tekrar okutulursa müşteri kendi numarasını yine görür.

---

## Kasa ekranı

Kasa ekranı kasadaki tablet ya da laptopta açılır ve işletme hesabıyla giriş ister.

**Sıradaki müşteri kartı**
- Büyük sıra numarası, ödeme şekli (KART / NAKİT), masa bilgisi.
- Ek notlar: *seslenerek çağır* (elden sipariş), *değiştirildi*, *geri atıldı*.
- Ürün listesi ve toplam tutar.

**Tuşlar**

| Tuş | Ne yapar |
|---|---|
| **Ödendi** | Sırayı ilerletir, müşteride durumu "Hazırlanıyor" yapar, siparişi "Hazırlananlar" listesine taşır. |
| **3 sıra geri at** | Gelmeyen müşteriyi 3 sıra arkaya alır. Sıra ilerler. |
| **İptal** | Yine gelmeyen müşterinin siparişini iptal eder. Sıra ilerler. |
| **Hazır** | Hazırlananlar listesindeki siparişi hazır yapar. Müşteri telefonunda görür. |
| **Tükendi** | Ürünü müşteri menüsünden anında kaldırır. Şifre gerekmez. |

**Listeler**
- **Bekleyenler:** sıradaki müşteriden sonra gelenler.
- **Hazırlananlar:** ödemesi alınmış, tezgâha gelmeyi bekleyen siparişler.
- **Son hazırlar:** müşteriye bildirilmiş son siparişler.

Yeni sipariş geldiğinde kasa ekranı kısa bir ses çalar.

---

## Sıra mantığı

- Sıra, **kasaya gelme sırasıdır.** Hazırlık sırası değildir.
- Hazırlık **ödemeden sonra** başlar.
- Numaralar her gün 101'den başlar.
- **Gelmeyen müşteri:** kasiyer önce seslenir → gelmezse **3 sıra geri at** → yine gelmezse **İptal**.
- "3 sıra geri at" sıradaki müşteriyi 3. ve 4. arkadaki müşterilerin arasına yerleştirir. Arkada 3 kişiden az varsa en sona alır.
- Barista iş akışı sistemin dışındadır. Barista ekranı yoktur; "Hazır" tuşu kasiyerdedir.

---

## Siparişe giriş yolları

| Yol | Nerede | Sipariş nasıl görünür |
|---|---|---|
| **Masa QR'ı** | Her masada, masa numarasıyla | "Masa 4" |
| **Masasız QR** | Kapıda ya da kasa önünde | "Masasız" |
| **Elden sipariş** | Kasiyer girer | "Elden · seslenerek çağır" |

**Elden sipariş** QR okutamayan ya da telefonu olmayan müşteri içindir ve son çare olarak kullanılır. Müşteri sıranın sonundan numara alır, sırayı atlamaz. Sırası gelince kasiyer seslenerek çağırır.

---

## İşletme ayarları

- **Menü:** kurulum sırasında kafeyle birlikte girilir. Kategori, ürün adı ve fiyattan oluşur.
- **Tükendi:** kasiyer tek dokunuşla, şifresiz işaretler.
- **Fiyat değiştirme:** kasa ekranında, **patron PIN'i** (4-6 hane) ister. Her değişiklik kayda geçer: tarih-saat, ürün, eski fiyat → yeni fiyat, değiştiren hesap.
- **Kasa hesapları:** kasa ve kurulum ekranına girebilecek e-postalar kurulum ekranından yönetilir.
- **Masa QR'ları:** masa sayısı girilir, QR kartları yazdırılır ya da PNG olarak indirilir.
- **Gün sonu:** "Siparişleri sıfırla" tüm siparişleri siler ve numaraları 101'den başlatır. Menü ve ayarlar kalır.

### Ödeme
- Sistem kasaya sadece ödeme **şeklini** (Kart / Nakit) iletir.
- POS cihazı sisteme bağlı değildir. Kasiyer POS'u her zamanki gibi elle kullanır.
- Online ödeme ve otomatik çekim yoktur.

---

## İnternet kesilirse

- Mevcut siparişler iptal edilmez, oldukları yerde donar.
- Kasa ekranı son listeyi göstermeye devam eder.
- Kasa eski usule döner: kasiyer siparişleri sözlü alır.
- Yedek çözüm: kasiyer telefonundan internet paylaşımı (hotspot) açar, tablet geri bağlanır.
- Bağlantı gelince sistem kaldığı yerden devam eder.

---

## Neler değişir, neler aynı kalır

**Değişir**
- Kasa önünde ayakta kuyruk oluşmaz.
- Kasiyer sipariş dinlemez, sadece ödeme alır.
- Müşteri ne kadar bekleyeceğini görür, masasında oturur.
- Siparişler yazılı geldiği için yanlış anlama azalır.

**Aynı kalır**
- Barista aynı hızda çalışır.
- Ödeme kasada, aynı POS cihazıyla alınır.
- Menü ve fiyatlar kafenin kendi menüsüdür.
- İsteyen müşteri hâlâ kasada sözlü sipariş verebilir (elden sipariş).

---

## Görsel dil

Lüks ve sakin bir görünüm hedeflenir: koyu bir zemin, az renk, ince çizgiler.

| Rol | Renk | Kullanım |
|---|---|---|
| Zemin | `#161826` koyu lacivert | Sayfa arka planı, üstte hafif çivit ışıltı |
| Yüzey | `#232532` | Kartlar, alt çubuk |
| Ana renk | `#d4b37a` şampanya altını | Ana düğmeler, fiyatlar, seçili kategori, masa etiketi |
| Altın açık | `#ecdcb8` | Sıra numarası, altın düğme yazısı |
| Altın koyu | `#2c2619` | Altın düğme ve seçili sekme dolgusu |
| İkinci renk | `#9184d9` mor | Uyarı şeridi, "Önünde" sayısı, ödeme seçimi |
| Metin | `#e9e9ed` | Ana metin |

**Yazı tipleri**
- **Instrument Serif:** kafe adı, kategori başlıkları, büyük sıra numaraları.
- **Inter:** tüm metinler ve düğmeler.
- **Sora:** fiyatlar ve küçük sayılar.

**Kurallar**
- Renk büyük alanlara dökülmez; çizgi, kenar ve hafif ışıltı olarak kullanılır.
- Ana düğmeler altın kenarlı ve hafif dolguludur.
- Dokunma alanları en az 40-44 px'tir.
- Büyük harfli küçük başlıklar Türkçe kurallarla yazılır (YİYECEK, NAKİT).

---

## Dosya yapısı

```
sirala-kod/
  index.html      Sayfa iskeleti; fontlar ve dosyalar buradan yüklenir
  css/style.css   Tüm stil. En üstte renk ve yazı değişkenleri (:root)
  js/app.js       Menü, sepet, sıra, uyarılar ve kasa mantığı
  README.md       Bu dosya
```

`js/app.js` içindeki bölümler:

| Bölüm | İçerik |
|---|---|
| `MENU0` | Menü listesi: kategori, ürün adı, fiyat |
| `CAFE`, `TABLE`, `TICK_MS` | Kafe adı, masa, otomatik sıra hızı |
| `seed()` | Demoda sırada bekleyen sahte siparişler (140-159) |
| `beep()`, `keepAwake()` | Ses, titreşim, ekranı açık tutma |
| `setInterval(...)` | Demo için kasiyer taklidi: sırayı otomatik ilerletir |
| `submit()`, `back3()` | Numara alma / sipariş güncelleme, 3 sıra geri atma |
| `menuView()`, `ticketView()`, `kasaView()` | Ekranların HTML'i |
| `render()` | Ekranı baştan çizer |
| `click` olayı | Tüm düğmeler `data-act` ile burada yakalanır |

---

## Demoyu çalıştırma

**Bilgisayarda:** `index.html` dosyasına çift tıkla.

**Telefonda (GitHub Pages):**
1. Dosyaları GitHub'da bir depoya yükle.
2. Depoda **Settings → Pages** bölümüne gir.
3. **Branch:** `main`, klasör: `/ (root)` seç, **Save**'e bas.
4. Birkaç dakika sonra site `https://KULLANICI_ADI.github.io/DEPO_ADI/` adresinde açılır.
5. Bu adresten bir QR kod üretip telefonla okutabilirsin.

**Demoyu kullanma:**
- Üstteki **Müşteri / Kasa** geçişiyle iki ekran arasında gidip gelirsin.
- **Otomatik: açık** iken sıra her 3 saniyede bir kendiliğinden ilerler. Kapatırsan kasa ekranından "Ödendi"ye basarak elle ilerletirsin.
- Ses için ekrana en az bir kez dokunmak gerekir (tarayıcı kuralı).
- Demo verileri sahtedir, tek telefonda çalışır ve sayfa yenilenince sıfırlanır.

---

## Kodda değişiklik yapma

| Ne değiştirmek istiyorsun | Nereden |
|---|---|
| Menü ürünleri ve fiyatlar | `js/app.js` → `MENU0` |
| Yeni kategori | `MENU0`'a yeni kategori adıyla satır ekle; sekme kendiliğinden oluşur |
| Başlangıçta tükenen ürünler | `js/app.js` → `menu` tanımındaki `out:` koşulu |
| Kafe adı, masa | `js/app.js` → `CAFE`, `TABLE` |
| Otomatik sıra hızı | `js/app.js` → `TICK_MS` (milisaniye) |
| Uyarının kaç sıra kala çalacağı | `js/app.js` → `checkAlerts()` içindeki `p <= 3` |
| Renkler | `css/style.css` → `:root` içindeki `--gold`, `--acc`, `--bg` … |
| Yazı tipleri | `index.html` font bağlantısı + `css/style.css` → `--f-serif`, `--f-body`, `--f-num` |
| Metinler | `js/app.js` içindeki görünüm fonksiyonları |

---

## Teknik mimari (gerçek sürüm)

Bu klasör tasarım demosudur. Gerçek sürüm ayrı bir klasörde (`sirala/`) Firebase ile çalışır.

- **Ön yüz:** düz HTML + CSS + JavaScript (ES modules). Framework ve build adımı yok.
- **Arka uç:** Firebase — Firestore (veritabanı), Authentication (giriş), Hosting (yayın).
- **Müşteri girişi:** anonim oturum, otomatik. Müşteri hiçbir şey doldurmaz.
- **Kasa ve kurulum girişi:** e-posta ve şifre.
- **Sıra numarası:** sayaç ve sipariş tek bir işlemde (transaction) yazılır; iki telefon aynı numarayı alamaz. Güvenlik kuralları sayacın tam 1 arttığını doğrular.
- **Canlı güncelleme:** Firestore `onSnapshot` dinleyicileri; kasada yapılan her işlem müşteri telefonuna anında yansır.
- **Çevrimdışı:** Firestore yerel önbelleği; bağlantı kopunca kasa son listeyi gösterir.

**Sayfalar**

| Dosya | Kim kullanır | Adres |
|---|---|---|
| `index.html` | Müşteri | `/?k=KAFE_KODU&m=MASA_NO` (masasız: `&ayakta=1`) |
| `kasa.html` | Kasiyer | `/kasa.html?k=KAFE_KODU` |
| `kurulum.html` | Kurulumu yapan / kafe sahibi | `/kurulum.html` |

**Veri modeli (özet)**

```
cafes/{kafe}                 ad, masa sayısı, menü, kasa hesapları
cafes/{kafe}/meta/counter    sıradaki numara (101'den başlar)
cafes/{kafe}/private/settings  PIN özeti, fiyat değişiklik kaydı
cafes/{kafe}/orders/{no}     numara, masa, ürünler, toplam, ödeme şekli,
                             durum (waiting / preparing / ready / cancelled)
```

---

## Bilinen sınırlar

- **Bildirim:** Android'de gerçek push bildirimi henüz yok. Müşteri sayfayı açık tutmalıdır; açıkken ekran kilitlenmez.
- **iPhone:** titreşim desteklenmez; ses çalışır.
- **Tek aktif numara:** telefon tarafında kontrol edilir; gizli sekmeyle atlatılabilir.
- **PIN:** kasiyerin izinsiz fiyat değiştirmesini zorlaştırır ama tam bir güvenlik önlemi değildir; bu yüzden her değişiklik kaydedilir.
- **Sipariş görünürlüğü:** sıra hesaplanabilsin diye siparişler (ürün, masa, ödeme şekli) okunabilir. İsim ya da telefon numarası tutulmaz.
- **Eski siparişler:** gün sonunda "Siparişleri sıfırla" elle yapılır.
- **Gerçek Firebase testi:** gerçek sürüm henüz canlı ortamda denenmedi.

---

## Yol haritası

1. Yeni görsel dili gerçek sürüme (`sirala/public/css/style.css`) taşımak
2. Gerçek Firebase'de canlı test, güvenlik kurallarının doğrulanması
3. Android push bildirimi (Firebase Cloud Messaging)
4. Gün sonu otomatik temizlik
5. Kasa için tablet yerleşimi ve "Elden sipariş" sekmesinin yeni tasarımı
6. İleride: salondaki TV ekranı, tablet kiosk, SMS bildirimi (ücretli ek), garsonlu restoran sürümü

---

*"Sırala" geçici çalışma adıdır.*
