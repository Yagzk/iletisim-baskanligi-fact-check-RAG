# 🔍 İletişim Başkanlığı Fact-Check RAG

>  **Bu proje hâlâ test aşamasındadır. Eksiklikler ve hatalar olabilir.**

T.C. Cumhurbaşkanlığı İletişim Başkanlığı'nın dezenformasyon bültenlerini kullanarak Türkçe iddiaları sorgulayan bir RAG (Retrieval-Augmented Generation) sistemi.

## 🧠 Nasıl Çalışır?

```
Kullanıcı sorusu → Embedding → ChromaDB'den benzer vakalar → Llama 3.1 analiz → Cevap
```

1. T.C. İletişim Başkanlığı'nın ~1.300 dezenformasyon vakası vektör veritabanına gömülür
2. Kullanıcı bir iddia girer
3. Sistem en benzer geçmiş vakaları cosine similarity ile bulur
4. Llama 3.1 bu vakalara dayanarak iddianın dezenformasyon olup olmadığını analiz eder

## 📦 Teknoloji Stack

| Bileşen | Teknoloji |
|---|---|
| Veri | [iletisim/dezenformasyon-bultenleri](https://huggingface.co/datasets/iletisim/dezenformasyon-bultenleri) |
| Embedding | `paraphrase-multilingual-mpnet-base-v2` |
| Vektör DB | ChromaDB (cosine similarity) |
| LLM | Llama 3.1 8B (Ollama) |
| Notebook | Jupyter / Google Colab |

## 🚀 Kurulum

### 1. Gereksinimler

```bash
pip install chromadb sentence-transformers pyarrow pandas ollama
```

### 2. Ollama & Llama 3.1

[Ollama](https://ollama.com/download)'yı indirip kur, ardından:

```bash
ollama pull llama3.1
```

### 3. Veriyi İndir

[Hugging Face](https://huggingface.co/datasets/iletisim/dezenformasyon-bultenleri)'den `train-00000-of-00001.parquet` dosyasını indirip proje klasörüne koy.

### 4. Notebook'u Çalıştır

`app.ipynb` dosyasını açıp hücreleri sırayla çalıştır.

## 📁 Dosya Yapısı

```
├── app.ipynb                        # Ana notebook
├── train-00000-of-00001.parquet     # Ham veri (Hugging Face'den indir)
├── dezenformasyon_clean.parquet     # Temizlenmiş veri (notebook üretir)
└── chroma_db/                       # Vektör veritabanı (notebook üretir)
```

## 💬 Kullanım

```python
rag_fact_check("Depremde baraj patladı")
```

```
🔍 Sorgu: Depremde baraj patladı
============================================================
📌 Bulunan Benzer Vakalar (3):
  1. Deprem Sonrasında Atatürk Barajı'nda Çatlaklar Oluştu (skor: 0.68)
  2. Osmaniye'deki Karaçay Barajı Patladı (skor: 0.59)
  3. Erzincan İliç'teki Altın Madeninde Baraj Patladı (skor: 0.59)

🤖 Analiz:
Verilen geçmiş vakalar incelendiğinde bu iddia daha önce
çürütülmüş benzer vakalarla örtüşmektedir...
```

## ⚙️ Parametreler

```python
rag_fact_check(
    sorgu="iddia metni",
    k=3,       # Kaç benzer vaka getirilsin
    esik=0.5   # Minimum benzerlik skoru (0-1)
)
```

Eşik değeri düşürülürse daha fazla vaka gelir ama alakasız sonuçlar artabilir.

## 📊 Veri Hakkında

- **Kaynak:** T.C. Cumhurbaşkanlığı İletişim Başkanlığı Dezenformasyon Mücadele Merkezi
- **Ham kayıt:** 2.810
- **Temizleme sonrası:** ~1.300 benzersiz vaka
- **Lisans:** CC BY 4.0
- **Dil:** Türkçe

## 🔧 Veri Temizleme

Ham veride tespit edilen sorunlar ve çözümleri:

- **Curly quote karakterleri** (`\u201c`, `\u201d`) → regex ile iddia başlığı çıkarıldı
- **PDF parse kaynaklı satır kırıkları** → `\n` birleştirildi
- **Bozuk `˿` karakterleri** → temizlendi
- **Fact-check içine karışmış bülten metinleri** → iddia başlığında kesildi
- **Duplicate vakalar** → en uzun fact-check tutulan versiyon korundu

## 🗺️ Yol Haritası

- [ ] Skor eşiği ayarlanabilir slider
- [ ] Sorgu geçmişi
- [ ] Türkçe embedding modeline geçiş

## 📄 Lisans

MIT
