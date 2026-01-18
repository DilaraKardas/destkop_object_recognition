🇬🇧 [English](README.md) | 🇹🇷 Türkçe

# YOLOv8 Object Detection Project  
# YOLOv8 Nesne Tespiti Projesi

---

## 🇹🇷 Türkçe

### 📌 Proje Tanımı

Bu proje, **Görsel Dil Modelleri (Vision-Language Models)** dersi kapsamında  
**1 haftadan kısa sürede** geliştirilmiş bir **nesne tespiti (object detection)** çalışmasıdır.

Projenin temel amacı, **oluşturulan veri setinin model performansı üzerindeki etkisini gözlemlemek**  
ve veri kalitesinin nesne tespit başarımına nasıl yansıdığını incelemektir.

---

### 🗂️ Veri Seti Hakkında

Bu projede kullanılan veri seti:

- **Bursa Teknik Üniversitesi Bilgisayar Mühendisliği laboratuvarlarında**  
  bulunan gerçek nesnelerden elde edilmiştir.
- Veri seti şu nesneleri içermektedir:
  - Monitör
  - Klavye
  - Sandalye
  - Bilgisayar faresi
- Tüm görseller **el ile çekilmiştir** (hazır veri seti kullanılmamıştır).
- **2000'den fazla görsel**, **Roboflow** platformu üzerinden **manuel olarak etiketlenmiştir**.
- Veri arttırma yöntemleri uygulanmıştır.
- Veri seti, Roboflow üzerinden:
  - `%70 Train`
  - `%20 Validation`
  - `%10 Test`  
  oranlarıyla ayrılmıştır.

---

### 🎯 Projenin Amacı

Bu çalışmada temel hedef:

- Model mimarisinden ziyade,
- **veri setinin kalitesinin ve çeşitliliğinin**
- nesne tespit modeli üzerindeki etkisini anlamaktır.

Bu nedenle model:
- Hazır mimari (YOLOv8) kullanılarak,
- **özel ve tamamen kullanıcı tarafından oluşturulmuş bir veri setiyle**  
  eğitilmiştir.

---

### 🧠 Kullanılan Teknolojiler

- Python 3.x
- Ultralytics YOLOv8
- PyTorch
- OpenCV
- Roboflow

---

### 📂 Proje Klasör Yapısı
```text
yolo_webcam_test/
├── best.pt            # Eğitilmiş YOLOv8 model ağırlıkları
├── webcam_test.py     # Webcam üzerinden gerçek zamanlı test kodu
└── README.md          # Proje açıklama dosyası
```

---

### ⚙️ Kurulum

Gerekli kütüphaneleri yükleyin:
```bash
pip install ultralytics opencv-python
```

Projeyi çalıştırın:
```bash
cd yolo_webcam_test
python webcam_test.py  # webcam ile modeli test edin
```
