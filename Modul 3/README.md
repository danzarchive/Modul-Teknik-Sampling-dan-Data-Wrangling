# Modul 4: Data Acquisition

### **Daftar Isi**
- [Modul 4: Data Acquisition](#modul-4-data-acquisition)
    - [**Daftar Isi**](#daftar-isi)
    - [**1. Data Types**](#1-data-types)
      - [**1.1 Structured Data**](#11-structured-data)
      - [**1.2 Semi-Structured Data**](#12-semi-structured-data)
      - [**1.3 Unstructured Data**](#13-unstructured-data)
    - [**2. Data Acquisition**](#2-data-acquisition)
      - [**2.1 Membaca File CSV**](#21-membaca-file-csv)
      - [**2.2 Membaca File Excel**](#22-membaca-file-excel)
      - [**2.3 Membaca File TXT**](#23-membaca-file-txt)
      - [**2.4 Membaca File JSON**](#24-membaca-file-json)
      - [**2.5 Membaca File XML**](#25-membaca-file-xml)
      - [**2.6 Membaca File YAML**](#26-membaca-file-yaml)
      - [**2.7 Membaca File PDF**](#27-membaca-file-pdf)
    - [**3. Pengantar HTML dan CSS**](#3-pengantar-html-dan-css)
      - [**3.1 Pengantar HTML**](#31-pengantar-html)
      - [**3.2 Elemen Dasar HTML**](#32-elemen-dasar-html)
      - [**3.3 Pengantar CSS**](#33-pengantar-css)
      - [**3.4 Analogi HTML dan CSS**](#34-analogi-html-dan-css)
      - [**3.5 Selektor CSS**](#35-selektor-css)
      - [**3.6 Contoh Kode**](#36-contoh-kode)
    - [**Referensi**](#referensi)

---

### **1. Data Types**
Data diklasifikasikan menjadi tiga tipe utama berdasarkan struktur dan pengolahan: structured, semi-structured, dan unstructured.

#### **1.1 Structured Data**
Data yang memiliki format tetap dan schema predefined. Data ini diorganisir dalam struktur tabular (baris dan kolom).

-   **Karakteristik:**
    -   Tersusun dalam baris (*records*) dan kolom (*attributes*).
    -   Mudah untuk di-query menggunakan bahasa seperti SQL.
    -   Langsung dapat digunakan oleh sebagian besar algoritma *machine learning*.
-   **Contoh:**
    -   **Tabel dalam database** relasional (MySQL, PostgreSQL).
    -   **Excel**.
    -   **CSV**.

#### **1.2 Semi-Structured Data**
Data yang tidak mengikuti struktur tabular yang ketat, tetapi memiliki *tag* atau penanda untuk memisahkan elemen-elemen semantik dan menciptakan hierarki.

-   **Karakteristik:**
    -   Tidak berbentuk baris dan kolom, tetapi memiliki struktur hierarkis (*nested*).
    -   Menggunakan *tag* atau pasangan *key-value*.
    -   Membutuhkan proses *parsing* sebelum dapat dianalisis.
-   **Contoh:**
    -   **JSON (JavaScript Object Notation):** digunakan dalam API.
    -   **XML (Extensible Markup Language):** digunakan dalam *web services* dan file konfigurasi.
    -   **Web server logs**.

#### **1.3 Unstructured Data**
Data yang tidak memiliki format yang telah ditentukan sebelumnya. Data yang paling banyak di dunia.

-   **Karakteristik:**
    -   Tidak memiliki skema yang jelas.
    -   Tidak dapat disimpan dalam format tabel tradisional tanpa pra-pemrosesan yang signifikan.
    -   Membutuhkan teknik lanjutan seperti NLP (*Natural Language Processing*) atau *Computer Vision* untuk mengekstrak fitur.
-   **Contoh:**
    -   **Teks:** Email, postingan media sosial (tweet), artikel berita, ulasan produk.
    -   **Gambar:** Foto di Instagram, citra medis (X-ray), gambar satelit.
    -   **Video:** Video YouTube, rekaman CCTV.
    -   **Audio:** Rekaman suara, podcast.
    -   **HTML**.

### **2. Data Acquisition**
**Data Acquisition** adalah fase awal dalam **data science**. Fase ini mengumpulkan raw data dari sumber, baik internal (database perusahaan, data warehouse) maupun eksternal (web, API, data publik) untuk analisis. Metode yang digunakan dalam data acquisition sangat bergantung pada tipe data yang diinginkan.

#### **2.1 Membaca File CSV**
CSV (*Comma-Separated Values*) adalah format teks untuk data tabular. Setiap baris adalah sebuah *record*, dan setiap kolom dipisahkan oleh koma.

```python
import pandas as pd

# Membaca file CSV dari direktori
df = pd.read_csv('dataset.csv')

print("--- Data dari CSV ---")
print(df)
print(f"\nDescribe Tabel: {df.describe()}")
```

- **Contoh Data:** Unduh sample CSV dari [Kaggle Iris Dataset](https://www.kaggle.com/datasets/uciml/iris/download?datasetVersionNumber=1) (file: iris.csv).

```python
import pandas as pd

# Membaca file CSV dari direktori (ganti path jika perlu)
df = pd.read_csv('iris.csv')

print("--- Data dari CSV ---")
print(df.head(5))  # Tampilkan 5 baris pertama
print(f"\nShape: {df.shape}")
print(f"\nDescribe Tabel: {df.describe()}")
print(f"\nInfo: {df.info()}")
```


#### **2.2 Membaca File Excel**
File Excel (`.xlsx`) adalah format biner yang lebih kompleks. Excel dapat berisi beberapa *sheets*, formula, dan pemformatan.

-   **Instalasi library:** `pip install openpyxl`
```python
import pandas as pd

# Membaca file Excel dari direktori
df_excel = pd.read_excel('dataset.xlsx', engine='openpyxl')

print("\n--- Data dari Excel ---")
print(df_excel)
```

- **Contoh Data:** Unduh sample Excel dari [Microsoft Sample Data](https://go.microsoft.com/fwlink/?LinkID=521962) (file: Financial Sample.xlsx).

```python
import pandas as pd

try:
    # Membaca file Excel dari direktori, spesifik sheet
    df_excel = pd.read_excel('Financial Sample.xlsx', engine='openpyxl', sheet_name='Sheet1')
    
    print("\n--- Data dari Excel ---")
    print(df_excel.head(5))  # Tampilkan 5 baris pertama
    print(f"\nShape: {df_excel.shape}")
    print(f"\nColumns: {df_excel.columns.tolist()}")
    print(f"\nDescribe: {df_excel.describe()}")
except FileNotFoundError:
    print("File Excel tidak ditemukan. Pastikan 'Financial Sample.xlsx' ada di direktori.")
```

#### **2.3 Membaca File TXT**
TXT adalah format teks sederhana untuk data unstructured, seperti log atau dokumen teks. Format TXT biasanya digunakan untuk menyimpan catatan sederhana, log aplikasi, atau data teks bebas yang tidak memerlukan formatting khusus, karena mudah dibaca oleh manusia dan mesin tanpa dependensi library tambahan.

-   **Karakteristik:**
    -   Tidak ada struktur bawaan, mudah dibaca tapi butuh processing manual.
-   **Praktik dengan Python:**
```python
# Membaca file TXT dari direktori
with open('data.txt', 'r', encoding='utf-8') as file:
    text = file.read()

print("\n--- Data dari TXT ---")
print(text[:200])  # Cetak 200 karakter pertama
```

- **Contoh Data:** Unduh sample TXT dari [Project Gutenberg - Alice's Adventures in Wonderland](https://www.gutenberg.org/files/11/11-0.txt) (file: 11-0.txt).

-   **Praktik dengan Python:**
```python
try:
    # Membaca file TXT dari direktori
    with open('11-0.txt', 'r', encoding='utf-8') as file:
        text = file.read()
    
    print("\n--- Data dari TXT ---")
    print(text[:200])  # Cetak 200 karakter pertama
    print(f"\nJumlah karakter: {len(text)}")
    print(f"\nJumlah baris: {len(text.splitlines())}")
    # Eksplorasi sederhana: Hitung kata unik
    words = text.lower().split()
    unique_words = len(set(words))
    print(f"\nJumlah kata unik: {unique_words}")
except FileNotFoundError:
    print("File TXT tidak ditemukan. Pastikan '11-0.txt' ada di direktori.")
```

#### **2.4 Membaca File JSON**
JSON (*JavaScript Object Notation*) menggunakan format *key-value*. Format data paling banyak untuk API. JSON biasanya digunakan untuk pertukaran data antar sistem, konfigurasi aplikasi, atau respons API, karena ringan, mudah dibaca, dan mendukung struktur nested.

*   Contoh JSON
```json
{
    "nama": "Ranggo",
    "usia": 20,
    "program_studi": "Sains Data",
    "mata_kuliah": ["Data Wrangling", "Pemodelan Statistika", "Data Warehouse"]
}
```

```python
import json

# Membuka dan membaca file JSON
with open('data.json', 'r') as file:
    data = json.load(file)

print("\n--- Data dari JSON ---")
print(f"Nama: {data['nama']}")
print(f"Mata Kuliah Pertama: {data['mata_kuliah'][0]}")
```

- **Contoh Data:** Unduh sample JSON dari [JSONPlaceholder - Sample Posts](https://jsonplaceholder.typicode.com/posts) (simpan sebagai posts.json).

```python
import json
import pandas as pd  # Untuk konversi ke DataFrame

try:
    # Membuka dan membaca file JSON
    with open('posts.json', 'r') as file:
        data = json.load(file)
    
    print("\n--- Data dari JSON ---")
    print(f"Jumlah item: {len(data)}")
    if isinstance(data, list):
        print(f"Item pertama: {data[0]}")
    
    # Konversi ke DataFrame untuk eksplorasi
    df_json = pd.DataFrame(data)
    print(f"\nHead DataFrame: {df_json.head(3)}")
    print(f"\nShape: {df_json.shape}")
except FileNotFoundError:
    print("File JSON tidak ditemukan. Pastikan 'posts.json' ada di direktori.")
```

#### **2.5 Membaca File XML**
XML (*Extensible Markup Language*) adalah format semi-structured dengan tag hierarkis, sering digunakan untuk data exchange. XML biasanya digunakan untuk dokumen yang memerlukan validasi skema ketat, seperti konfigurasi XML di aplikasi enterprise atau data RSS feed, karena mendukung namespace dan validasi DTD/XSD.

-   **Karakteristik:**
    -   Mirip JSON tapi lebih verbose.
    -   Membutuhkan parsing untuk ekstrak elemen.
-   **Instalasi library:** Built-in, atau gunakan `xml.etree.ElementTree`
```python
import xml.etree.ElementTree as ET

# Membaca file XML dari direktori
tree = ET.parse('data.xml')
root = tree.getroot()

print("\n--- Data dari XML ---")
for child in root:
    print(child.tag, child.attrib)
```

- **Contoh Data:** Unduh sample XML dari [W3Schools - Books XML](https://www.w3schools.com/xml/books.xml) (simpan sebagai books.xml).

```python
import xml.etree.ElementTree as ET
import pandas as pd  # Untuk konversi

try:
    # Membaca file XML dari direktori
    tree = ET.parse('books.xml')
    root = tree.getroot()
    
    print("\n--- Data dari XML ---")
    data = []
    for child in root:
        item = {subchild.tag: subchild.text for subchild in child}
        data.append(item)
        print(f"{child.tag}: {child.attrib}")
    
    # Konversi ke DataFrame
    df_xml = pd.DataFrame(data)
    print(f"\nDataFrame from XML: {df_xml.head()}")
except FileNotFoundError:
    print("File XML tidak ditemukan. Pastikan 'books.xml' ada di direktori.")
```

#### **2.6 Membaca File YAML**
YAML (*YAML Ain't Markup Language*) adalah format semi-structured seperti JSON, sering untuk konfigurasi dengan sintaks lebih mudah dibaca manusia. YAML biasanya digunakan untuk file konfigurasi (e.g., Docker Compose, Ansible), karena mendukung komentar, indentasi alami, dan struktur kompleks tanpa tanda kutip berlebih.

-   **Karakteristik:**
    -   Gunakan untuk data nested dengan indentasi.
-   **Instalasi library:** `pip install pyyaml`
```python
import yaml

# Membaca file YAML dari direktori
with open('config.yaml', 'r') as file:
    data = yaml.safe_load(file)

print("\n--- Data dari YAML ---")
print(data)
```

- **Contoh Data:** Unduh sample YAML dari [GitHub - Docker Compose Sample](https://raw.githubusercontent.com/docker/awesome-compose/master/nginx-go/docker-compose.yaml) (simpan sebagai docker-compose.yaml).

```python
import yaml
import pandas as pd  # Untuk konversi jika nested

try:
    # Membaca file YAML dari direktori
    with open('docker-compose.yaml', 'r') as file:
        data = yaml.safe_load(file)
    
    print("\n--- Data dari YAML ---")
    print(data)
    # Eksplorasi: Akses key spesifik
    if 'services' in data:
        print(f"\nServices: {list(data['services'].keys())}")
    
    # Konversi nested ke DataFrame (contoh jika data tabular)
    if isinstance(data, dict) and 'services' in data:
        df_yaml = pd.DataFrame.from_dict(data['services'], orient='index')
        print(f"\nDataFrame from YAML: {df_yaml.head()}")
except FileNotFoundError:
    print("File YAML tidak ditemukan. Pastikan 'docker-compose.yaml' ada di direktori.")
```

#### **2.7 Membaca File PDF**
PDF (*Portable Document Format*) adalah format untuk dokumen yang mempertahankan layout, sering berisi teks unstructured atau semi-structured yang perlu diekstrak. PDF biasanya digunakan untuk laporan, buku, atau dokumen resmi yang memerlukan tampilan konsisten antar perangkat, meskipun ekstraksi teksnya bisa rumit karena enkripsi atau gambar.

-   **Karakteristik:**
    -   Berguna untuk dokumen seperti laporan atau artikel.
    -   Membutuhkan library untuk ekstraksi teks.
-   **Instalasi library:** `pip install PyPDF2`
```python
import PyPDF2

# Membaca file PDF dari direktori
with open('document.pdf', 'rb') as file:
    reader = PyPDF2.PdfReader(file)
    text = ''
    for page in reader.pages:
        text += page.extract_text()

print("\n--- Data dari PDF ---")
print(text[:200])  # Cetak 200 karakter pertama
```

- **Contoh Data:** Unduh sample PDF dari [Sample PDF - IRS Form](https://www.irs.gov/pub/irs-pdf/fw9.pdf) (file: fw9.pdf).

```python
import PyPDF2

try:
    # Membaca file PDF dari direktori
    with open('fw9.pdf', 'rb') as file:
        reader = PyPDF2.PdfReader(file)
        text = ''
        for page in reader.pages:
            text += page.extract_text() + '\n'
    
    print("\n--- Data dari PDF ---")
    print(text[:200])  # Cetak 200 karakter pertama
    print(f"\nJumlah halaman: {len(reader.pages)}")
    print(f"\nMetadata: {reader.metadata}")
except FileNotFoundError:
    print("File PDF tidak ditemukan. Pastikan 'fw9.pdf' ada di direktori.")
```

-   **Teknik Ekstraksi PDF Lanjutan:**  
    Untuk kasus kompleks seperti PDF scanned (gambar teks) atau layout tabular, gunakan library lanjutan seperti PyMuPDF (fitz) untuk ekstraksi cepat dengan OCR fallback, atau pdfplumber untuk tabel dan layout-sensitive. PyMuPDF lebih cepat dan handle metadata/gambar; pdfplumber unggul untuk parsing tabel. Pada 2025, benchmark menunjukkan PyMuPDF sebagai pilihan utama untuk akurasi tinggi.

-   **Instalasi library lanjutan:** `pip install PyMuPDF pdfplumber pytesseract`
```python
# Contoh 1: PyMuPDF untuk teks dan metadata
import fitz  # PyMuPDF

try:
    doc = fitz.open('fw9.pdf')
    text = ''
    for page in doc:
        text += page.get_text() + '\n'
    
    print("\n--- Data dari PDF (PyMuPDF) ---")
    print(text[:200])
    print(f"\nJumlah halaman: {doc.page_count}")
    print(f"\nMetadata: {doc.metadata}")
    doc.close()
except FileNotFoundError:
    print("File PDF tidak ditemukan.")

# Contoh 2: pdfplumber untuk tabel
import pdfplumber

try:
    with pdfplumber.open('fw9.pdf') as pdf:
        page = pdf.pages[0]
        table = page.extract_table()
        print("\n--- Tabel dari PDF (pdfplumber) ---")
        print(table[:2] if table else "No table found")
except FileNotFoundError:
    print("File PDF tidak ditemukan.")

# Contoh 3: OCR untuk scanned PDF (dengan Tesseract)
import pytesseract
from pdf2image import convert_from_path  # pip install pdf2image

try:
    pages = convert_from_path('scanned.pdf', 300)  # DPI tinggi untuk akurasi
    ocr_text = ''
    for page in pages[:1]:  # Halaman pertama
        ocr_text += pytesseract.image_to_string(page) + '\n'
    
    print("\n--- OCR dari PDF Scanned ---")
    print(ocr_text[:200])
except FileNotFoundError:
    print("File PDF scanned tidak ditemukan.")
```

### **3. Pengantar HTML dan CSS**

#### **3.1 Pengantar HTML**
HTML (*HyperText Markup Language*) merupakan bahasa untuk website. HTML menggunakan tag untuk mendefinisikan elemen, memungkinkan browser menampilkan konten secara terstruktur.

#### **3.2 Elemen Dasar HTML**
- **Tag Dasar:** `<html>`, `<head>`, `<body>`.
- **Elemen Konten:** `<h1>` untuk heading, `<p>` untuk paragraf, `<a>` untuk link, `<img>` untuk gambar.
- **Atribut:** Menambahkan properti, seperti `src` pada `<img>` atau `href` pada `<a>`.

#### **3.3 Pengantar CSS**
CSS (*Cascading Style Sheets*) adalah bahasa untuk tampilan atau interface website. Memisahkan desain dari konten HTML yang dapat mengatur warna, font, tata letak, dan responsivitas.

#### **3.4 Analogi HTML dan CSS**
HTML dapat dianalogikan dengan kerangka mobil atau fondasi bangunan, yang mana sebagai kerangka utama seperti pondasi, kolom, dan atap (misalnya, tag `<body>`, `<header>`, `<section>`).  
CSS seperti dekorasi mobil atau rumah, yang menentukan estetika seperti cat, furnitur, dan pencahayaan (misalnya, `background-color: white; margin: 10px;`).  

![Analogi HTML dan CSS](https://v1.scrimba.com/articles/content/images/2022/11/image-1.png)  
*(Sumber: Scrimba).*

#### **3.5 Selektor CSS**
- **Selektor Elemen:** Menerapkan gaya ke tag spesifik, seperti `p { color: black; }`.
- **Selektor Class/ID:** `.kelas { font-size: 14px; }` atau `#id { border: 1px solid; }`.
- **Selektor Kompleks:** Kombinasi seperti `div p { margin: 5px; }` untuk elemen anak.

#### **3.6 Contoh Kode**
```html
<!DOCTYPE html>
<html lang="id">
<head>
    <title>Contoh Halaman Web</title>
    <style>
        body { font-family: Arial, sans-serif; background-color: #f4f4f4; }
        h1 { color: #333; text-align: center; }
        p { font-size: 16px; margin: 10px; }
    </style>
</head>
<body>
    <h1>Selamat Datang di Halaman Ini</h1>
    <p>Ini adalah paragraf contoh dengan gaya CSS sederhana.</p>
    <a href="https://example.com">Kunjungi Situs Ini</a>
</body>
</html>
```

### **Referensi**
- Broucke, S., & Baesens, B. (2018). *Practical Web Scraping for Data Science: Best Practices and Examples with Python*. Apress.
- Kazil, J., & Jarmul, K. (2016). *Data Wrangling with Python: Tips and Tools to Make Your Life Easier*. O'Reilly Media.
- Scrimba, https://v1.scrimba.com/articles/html-css-javascript/