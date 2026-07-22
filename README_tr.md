🇹🇷 Türkçe | 🇬🇧 [English](README.md)

# Furniture AR App

Fotogrametri (COLMAP) ve derin öğrenme (SegFormer) kullanarak, önceden taranmış bir mekana ait fotoğrafta gerçek ölçekte ve doğru zemin hizasında 3B mobilya yerleştirmeyi sağlayan web tabanlı bir artırılmış gerçeklik (AR) sistemi.

Bursa Teknik Üniversitesi Bilgisayar Mühendisliği Bölümü bitirme projesi olarak geliştirilmiştir — Bitirme Projesi Sergisi'nde **En İyi 2. Proje** seçilmiştir (Temmuz 2026).

## Problem

Mobilya e-ticaretinde en kritik sorunlardan biri, müşterinin ürünü satın almadan önce kendi mekanında nasıl duracağını göremeden karar vermek zorunda kalmasıdır; bu da yüksek iade oranlarına yol açar. Mevcut AR çözümlerinin çoğu özel bir uygulama kurulumu veya derinlik sensörü gibi donanımlar gerektirdiğinden erişimi sınırlar.

## Yaklaşım

Oda geometrisini her istekte tek bir fotoğraftan tahmin etmek yerine (bu yöntem güvenilmez çıktı, bkz. [Neden monoküler derinlik tahmini değil?](#neden-monoküler-derinlik-tahmini-değil)), bu sistem bir mekanı (örneğin bir showroom veya model daire) **bir kereye mahsus fotogrametrik olarak tarar**, ardından o tarama sırasında çekilen fotoğrafların herhangi birinde doğru ölçekte mobilya yerleştirimi sunar — hepsi sadece bir web tarayıcısı üzerinden, uygulama kurulumu gerektirmeden.

## Nasıl çalışır

1. **Mekan taraması** — Odanın 60-85 fotoğrafı COLMAP (Structure-from-Motion + Multi-View Stereo) ile işlenerek yoğun bir nokta bulutu ve fotoğraf başına kamera pozu/intrinsics elde edilir.
2. **Ölçek kalibrasyonu** — Mekanda bilinen gerçek bir referans mesafe (duvar köşesi, çerçeve köşegeni gibi) bir kez ölçülüp COLMAP'in kendi biriminin metreye çevrilmesinde kullanılır.
3. **Zemin düzlemi tespiti** — Bir referans fotoğraf SegFormer (ADE20K) ile segmente edilir, zemin etiketli pikseller dense nokta bulutuna geri projekte edilir, RANSAC zemin düzlemi denklemini bulur.
4. **Canlı zemin sınırı** — Kullanıcı taramadan herhangi bir fotoğraf seçtiğinde, SegFormer *sadece o fotoğraf için* yeniden çalıştırılır ve zeminin gerçekte nerede bittiği (duvarlar, mobilyalar) ışın-düzlem kesişimiyle hesaplanır — nokta bulutu yoğunluğuna bağımlı değildir.
5. **Yerleştirme** — Kullanıcı bir GLB mobilya modeli yükler, fotoğrafta bir noktaya tıklar; sistem bu pikseli zemin düzlemi üzerinde gerçek bir 3D konuma çevirir, modeli gömülü metadata (`real_height_m`) veya elle girilen yükseklikle ölçeklendirir.
6. **Etkileşim** — Sürükleyerek taşıma, fare tekerleğiyle ölçekleme, sağ-tık sürükleyerek döndürme — hepsi ilk zemin/kamera verisi alındıktan sonra tarayıcıda (Three.js) canlı olarak hesaplanır.

## Kullanılan teknolojiler

| Katman | Teknoloji |
|---|---|
| 3B rekonstrüksiyon | COLMAP (SfM + MVS) |
| Semantik segmentasyon | SegFormer (ADE20K, `nvidia/segformer-b4-finetuned-ade-512-512`) |
| Düzlem uydurma | RANSAC |
| Backend | Flask (Python) |
| Frontend / 3B render | Three.js (WebGL) |
| Sayısal hesaplama | NumPy, SciPy |

## Neden monoküler derinlik tahmini değil?

Projenin önceki bir versiyonunda, oda ölçeğini tek bir fotoğraftan tahmin etmek için Apple'ın Depth Pro modeli denenmiştir. Depth Pro bir saniyenin altında tam çözünürlüklü bir derinlik haritası üretebilse ve kameranın odak uzaklığını tahmin ederek metrik ölçek elde etmeye çalışsa da, bu projenin kullanım senaryosunda doğruluğu ölçülen oda boyutlarında hiçbir zaman %10 hatanın altına inmemiştir — çünkü tahmin edilen odak uzaklığındaki küçük bir hata, sahne derinliği arttıkça çok daha büyük bir mesafe hatasına dönüşmektedir. Nihai sistemde kullanılan COLMAP + bilinen referans mesafe yaklaşımı ise, oda başına bir kereye mahsus çoklu-fotoğraf taraması gerektirmesi karşılığında %2-8 hata payı sağlamıştır.

## Proje yapısı

```text
furniture-ar-app/
├── app.py                       # Flask sunucusu ve API route'ları
├── scene_utils.py                # Kamera geometrisi, zemin düzlemi, canlı sınır tespiti
├── requirements.txt
├── templates/
│   ├── home.html                 # Ürün listeleme / "mağaza" sayfası
│   └── index.html                # AR yerleştirme sayfası
├── static/
│   ├── css/
│   ├── js/main.js                 # Three.js sahnesi, etkileşim mantığı
│   ├── glb/                       # Mobilya modelleri (gitignore'da — kullanıcı sağlar)
│   ├── images/                    # Oda tarama fotoğrafları (gitignore'da — kullanıcı sağlar)
│   └── thumbnails/                # Ürün küçük resimleri (gitignore'da — kullanıcı sağlar)
└── scripts/
    ├── detect_floor_plane.py      # Bir kereye mahsus offline zemin düzlemi tespiti (RANSAC)
    └── embed_furniture_height.py  # Bir GLB dosyasına real_height_m metadata'sı gömer
```

## Kurulum

1. Repoyu klonla ve bağımlılıkları kur:
```bash
   pip install -r requirements.txt --break-system-packages
```
2. Bir odanın kendi COLMAP taramanı yap (sparse + dense rekonstrüksiyon), ardından kamera verisini `full_scene_data.json` (kamera pozları, intrinsics, ölçek faktörü) olarak dışa aktaran bir script çalıştır (bkz. `scripts/`).
3. `scripts/detect_floor_plane.py`'yi bir kez çalıştırarak zemin düzlemi denklemini sahne JSON'una ekle.
4. Ortaya çıkan `full_scene_data_with_floor.json`'ı proje köküne koy.
5. Oda fotoğraflarını `static/images/`'a, mobilya `.glb` dosyalarını `static/glb/`'ye ekle.
6. (İsteğe bağlı) GLB dosyalarına gerçek yükseklik göm:
```bash
   python scripts/embed_furniture_height.py sandalye.glb 0.85
```
7. Sunucuyu başlat:
```bash
   python app.py
```
8. `http://localhost:5000`'i aç.

## Bilinen kısıtlar

- Sadece önceden taranmış odanın fotoğraflarıyla çalışır (henüz rastgele kullanıcı fotoğrafı desteği yok — aşağıya bakınız).
- Lens bozulması yaklaşık bir düzeltmeyle ele alınmıştır, tam bir undistortion modeliyle değil.
- Zemin sınırının doğruluğu, seçilen fotoğraf için SegFormer'ın segmentasyon kalitesine bağlıdır.

## Gelecek çalışmalar

- Rastgele kullanıcı fotoğraflarını, COLMAP'i yeniden çalıştırmadan taranmış mekana kaydetmek için görsel yeniden konumlandırma (visual re-localization / PnP).
- Manuel döndürme yerine otomatik mobilya yönü belirleme.
- Görsel gerçekçiliği artırmak için gölge/ışık tutarlı bindirme (compositing).

## Danışman

Bu proje, Bursa Teknik Üniversitesi'nde Dr. Öğr. Üyesi Kubilay Demir danışmanlığında geliştirilmiştir.
