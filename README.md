# llm-metin-analizi-ve-structured-output
Google Gemini API ile Serbest Metin Analitiği, Duygu/Aciliyet Sınıflandırması ve Yapılandırılmış Çıktı (JSON/DataFrame) Modellemesi
# Büyük Dil Modelleri (LLM) ile Yapılandırılmamış Metin Analitiği ve Operasyonel Karar Destek Sistemi

Bu proje; kurumsal geri bildirim havuzlarındaki serbest metinlerin (müşteri şikayetleri, teknik arıza bildirimleri vb.) **Google Gemini API** kullanılarak yapılandırılmış veri şemalarına (Structured Output / JSON) dönüştürülmesini, duygu analizi ve aciliyet derecelendirmesi yapılmasını ve operasyonel ekipler için SLA (Hizmet Seviyesi Anlaşması) öncelik matrisi çıkarılmasını kapsayan uçtan uca bir yapay zekâ mühendisliği ve veri analitiği çalışmasıdır.

---

## Projenin Amacı ve İş Problemi
Geleneksel çağrı merkezi ve destek operasyonlarında serbest metin halindeki binlerce geri bildirim manuel olarak incelenmekte, bu durum acil müdahale gerektiren teknik ve operasyonel krizlerin geç fark edilmesine yol açmaktadır. Bu çalışmada amaç; prompt mühendisliği ve katı JSON çıktı şemaları ile geri bildirimleri saniyeler içinde departman, duygu ve aciliyet boyutlarında normalize edip aksiyon odaklı bir karar destek paneline dönüştürmektir.

---

## Kullanılan Teknolojiler ve Kütüphaneler
* **Programlama Dili:** Python
* **Yapay Zekâ & LLM Altyapısı:** Google Gemini API (`google-genai`), Prompt Engineering, Structured Output (`application/json`)
* **Veri Manipülasyonu ve İşleme:** `pandas`, `json`, `time`
* **Veri Görselleştirme:** `matplotlib`, `seaborn`

---

## Metodoloji ve Analitik Adımlar

### 1. Sistem Promptu ve Katı Şema Tasarımı
* LLM'in serbest sohbet üretmesi engellenmiş; `response_mime_type="application/json"` parametresi ve sistem talimatı (System Instruction) ile modele aşağıdaki katı şema dayatılmıştır:
  * `kategori`: Hizmet, Teknik, Fatura/Ödeme, Personel
  * `duygu`: Pozitif, Nötr, Negatif
  * `aciliyet_derecesi`: 1 - 5 arası tam sayı
  * `yonetici_ozeti`: Maksimum 10 kelimelik kök neden özeti

### 2. Toplu Metin İşleme (Batch Processing) ve DataFrame Dönüşümü
* Geri bildirim havuzu iteratif olarak işlenmiş, hata yakalama (`try-except`) ve bekleme (`time.sleep`) mekanizmalarıyla gelen yanıtlar analize hazır bir Pandas DataFrame'ine dönüştürülmüştür.

### 3. İş Zekası Görselleştirmesi ve Kriz Filtreleme
* Kategori bazlı ortalama aciliyet seviyeleri ve müşteri duygu oranları görselleştirilmiştir.
* Aciliyet puanı $\ge 4$ olan kritik vakalar anlık alarm tablosu olarak izole edilmiştir.

### 4. Operasyonel Aksiyon Matrisi ve SLA Modellemesi
* Departman bazında toplam bildirim sayısı, ortalama aciliyet ve negatiflik oranı hesaplanarak otomatik öncelik kuralları uygulanmıştır:
  * **🔴 KRİTİK:** Aciliyet $\ge 4$ veya Negatiflik $\ge \%80$ $\rightarrow$ *SLA < 2 Saat*
  * **🟡 ORTA:** Aciliyet $\ge 2.5$ $\rightarrow$ *SLA < 24 Saat*
  * **🟢 STANDART:** Rutin Takip

---

## Örnek Operasyonel Aksiyon Matrisi Çıktısı

| Kategori | Toplam Bildirim | Ortalama Aciliyet | Negatif Oranı | Operasyonel Öncelik (SLA) |
| :--- | :---: | :---: | :---: | :--- |
| **Personel** | 1 | 3.0 | %100 | 🔴 KRİTİK - Anlık Müdahale (SLA < 2 Saat) |
| **Teknik** | 3 | 3.67 | %66.7 | 🟡 ORTA - Gün İçi İnceleme (SLA < 24 Saat) |

---

## Kurulum ve Çalıştırma

```bash
# Gerekli kütüphaneleri yükleyin
pip install -q -U google-genai pandas matplotlib seaborn
