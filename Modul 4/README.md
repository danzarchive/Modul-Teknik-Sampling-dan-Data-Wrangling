# Materi Data Scraping untuk Mata Kuliah Data Wrangling

## 1. Pengertian dan Teori Data Scraping

Data scraping atau web scraping adalah proses otomatis untuk mengekstrak data dari website atau sumber data online lainnya. Proses ini melibatkan pengambilan data yang ditampilkan di halaman web dan mengkonversinya menjadi format yang dapat digunakan untuk analisis, seperti CSV, JSON, atau database.

### Konsep Dasar:
- **HTML Parsing**: Membaca dan menganalisis struktur HTML untuk menemukan elemen yang berisi data yang diinginkan
- **HTTP Requests**: Mengirim permintaan ke server web untuk mendapatkan konten halaman
- **DOM Navigation**: Navigasi melalui Document Object Model untuk menemukan elemen spesifik
- **Data Extraction**: Mengekstrak teks, angka, atau informasi lain dari elemen HTML

### Aspek Legal dan Etika:
- Selalu periksa file `robots.txt` website
- Hormati rate limiting dan jangan overload server
- Patuhi Terms of Service website
- Gunakan untuk keperluan penelitian dan pembelajaran yang sah

## 2. Fundamental Teknologi Web untuk Scraping

### Struktur HTML
HTML (HyperText Markup Language) adalah backbone dari web scraping. Pemahaman tentang struktur HTML sangat penting:

```html
<html>
  <head>
    <title>Judul Halaman</title>
  </head>
  <body>
    <div class="container">
      <h1 id="main-title">Judul Utama</h1>
      <p class="content">Konten paragraf</p>
      <ul>
        <li>Item 1</li>
        <li>Item 2</li>
      </ul>
    </div>
  </body>
</html>
```

### CSS Selectors
CSS selectors digunakan untuk menargetkan elemen HTML spesifik:
- `.class-name` untuk class
- `#id-name` untuk ID
- `tag-name` untuk tag HTML
- `parent > child` untuk relasi parent-child

### XPath
XPath adalah bahasa untuk navigasi XML/HTML documents:
- `//tag` untuk semua tag
- `//tag[@attribute='value']` untuk tag dengan atribut tertentu
- `//tag[text()='content']` untuk tag dengan teks tertentu

## 3. Library Python untuk Data Scraping

### 3.1 Requests
Library untuk mengirim HTTP requests:

```python
import requests

# GET request
response = requests.get('https://example.com')
print(response.status_code)
print(response.text)

# POST request dengan data
data = {'key': 'value'}
response = requests.post('https://example.com/api', data=data)

# Headers dan session
headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'}
session = requests.Session()
response = session.get('https://example.com', headers=headers)
```

### 3.2 BeautifulSoup4
Library untuk parsing HTML dan XML:

```python
from bs4 import BeautifulSoup
import requests

# Parsing HTML
html_content = requests.get('https://example.com').text
soup = BeautifulSoup(html_content, 'html.parser')

# Mencari elemen
title = soup.find('title').text
all_links = soup.find_all('a')
specific_div = soup.find('div', class_='content')
by_id = soup.find('p', id='description')
```

#### Perbedaan HTTP Requests dengan Beautiful Soup

| HTTP Requests | Beautiful Soup |
|---------------|----------------|
| Mengambil konten halaman web dari server (HTML content). | Memproses dan mengekstrak data dari konten HTML yang diambil. |
| Menggunakan pustaka requests untuk permintaan GET atau POST. | Menggunakan pustaka Beautiful Soup untuk memparsing HTML. |
| Langkah pertama dalam proses scraping. | Langkah kedua, yaitu untuk menavigasi dan mengekstrak data spesifik. |
| Bisa digunakan untuk mengambil data JSON dari API. | Terutama digunakan untuk memproses data HTML dan XML.


### 3.3 Selenium
Untuk website yang menggunakan JavaScript heavily:

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

# Setup driver
driver = webdriver.Chrome()
driver.get('https://example.com')

# Tunggu elemen load
wait = WebDriverWait(driver, 10)
element = wait.until(EC.presence_of_element_located((By.CLASS_NAME, "content")))

# Interaksi dengan halaman
driver.find_element(By.ID, "search-box").send_keys("search term")
driver.find_element(By.XPATH, "//button[@type='submit']").click()

driver.quit()
```

### 3.4 Scrapy
Framework untuk large-scale scraping:

```python
import scrapy

class QuotesSpider(scrapy.Spider):
    name = 'quotes'
    start_urls = ['https://quotes.toscrape.com']

    def parse(self, response):
        for quote in response.css('div.quote'):
            yield {
                'text': quote.css('span.text::text').get(),
                'author': quote.css('small.author::text').get(),
                'tags': quote.css('div.tags a.tag::text').getall(),
            }
        
        next_page = response.css('li.next a::attr(href)').get()
        if next_page:
            yield response.follow(next_page, self.parse)
```

## 4. Metode dan Teknik Data Scraping

### 4.1 Static Scraping
Untuk website dengan konten statis yang tidak bergantung pada JavaScript:

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

def scrape_static_content(url):
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')
    
    # Extract data berdasarkan struktur HTML
    data = []
    for item in soup.find_all('div', class_='item'):
        title = item.find('h2').text.strip()
        price = item.find('span', class_='price').text.strip()
        data.append({'title': title, 'price': price})
    
    return data
```

### 4.2 Dynamic Scraping
Untuk website dengan konten yang di-generate oleh JavaScript:

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
import time

def scrape_dynamic_content(url):
    driver = webdriver.Chrome()
    driver.get(url)
    
    # Tunggu konten load
    time.sleep(3)
    
    # Scroll untuk trigger infinite scroll
    driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
    time.sleep(2)
    
    # Extract data
    elements = driver.find_elements(By.CLASS_NAME, "dynamic-content")
    data = [element.text for element in elements]
    
    driver.quit()
    return data
```

### 4.3 API Scraping
Menggunakan API endpoints untuk mendapatkan data:

```python
import requests
import json

def scrape_api_data(api_url, params=None):
    headers = {
        'User-Agent': 'Mozilla/5.0',
        'Accept': 'application/json'
    }
    
    response = requests.get(api_url, params=params, headers=headers)
    
    if response.status_code == 200:
        return response.json()
    else:
        print(f"Error: {response.status_code}")
        return None
```

## 5. Praktik Data Scraping dengan Real Case

### Case 1: Scraping Berita dari Detik.com

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
from datetime import datetime
import time

def scrape_detik_news():
    base_url = "https://news.detik.com"
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36'
    }
    
    try:
        response = requests.get(base_url, headers=headers)
        soup = BeautifulSoup(response.text, 'html.parser')
        
        # Cari artikel berita
        articles = soup.find_all('article', class_='list-content__item')
        
        news_data = []
        for article in articles[:10]:  # Ambil 10 berita pertama
            try:
                title_elem = article.find('h3', class_='media__title')
                title = title_elem.find('a').text.strip() if title_elem else 'N/A'
                
                link_elem = article.find('h3', class_='media__title').find('a')
                link = link_elem['href'] if link_elem else 'N/A'
                
                time_elem = article.find('div', class_='media__date')
                publish_time = time_elem.text.strip() if time_elem else 'N/A'
                
                summary_elem = article.find('div', class_='media__desc')
                summary = summary_elem.text.strip() if summary_elem else 'N/A'
                
                news_data.append({
                    'title': title,
                    'link': link,
                    'publish_time': publish_time,
                    'summary': summary,
                    'scraped_at': datetime.now().isoformat()
                })
                
            except Exception as e:
                print(f"Error parsing article: {e}")
                continue
        
        # Convert to DataFrame
        df = pd.DataFrame(news_data)
        return df
        
    except Exception as e:
        print(f"Error scraping Detik: {e}")
        return None

# Jalankan scraping
news_df = scrape_detik_news()
if news_df is not None:
    print(f"Berhasil scraping {len(news_df)} berita")
    print(news_df.head())
    
    # Simpan ke CSV
    news_df.to_csv('detik_news.csv', index=False, encoding='utf-8')
```

### Case 2: Scraping Data Cuaca dari BMKG

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import re

def scrape_bmkg_weather():
    url = "https://www.bmkg.go.id/cuaca/prakiraan-cuaca-indonesia.bmkg"
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
    }
    
    try:
        response = requests.get(url, headers=headers)
        soup = BeautifulSoup(response.text, 'html.parser')
        
        # Cari tabel cuaca
        weather_table = soup.find('table', class_='table-weather')
        
        if not weather_table:
            print("Tabel cuaca tidak ditemukan")
            return None
            
        weather_data = []
        rows = weather_table.find_all('tr')[1:]  # Skip header
        
        for row in rows:
            cols = row.find_all('td')
            if len(cols) >= 4:
                city = cols[0].text.strip()
                weather = cols[1].text.strip()
                temp = cols[2].text.strip()
                humidity = cols[3].text.strip()
                
                weather_data.append({
                    'city': city,
                    'weather_condition': weather,
                    'temperature': temp,
                    'humidity': humidity,
                    'scraped_at': datetime.now().isoformat()
                })
        
        df = pd.DataFrame(weather_data)
        return df
        
    except Exception as e:
        print(f"Error scraping BMKG: {e}")
        return None

# Jalankan scraping cuaca
weather_df = scrape_bmkg_weather()
if weather_df is not None:
    print(f"Data cuaca untuk {len(weather_df)} kota")
    print(weather_df.head())
```

### Case 3: Scraping Data Saham dari Yahoo Finance

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import json

def scrape_yahoo_finance_stock(symbol):
    url = f"https://finance.yahoo.com/quote/{symbol}"
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
    }
    
    try:
        response = requests.get(url, headers=headers)
        soup = BeautifulSoup(response.text, 'html.parser')
        
        # Ekstrak data saham
        price_elem = soup.find('fin-streamer', {'data-field': 'regularMarketPrice'})
        current_price = price_elem.text if price_elem else 'N/A'
        
        change_elem = soup.find('fin-streamer', {'data-field': 'regularMarketChange'})
        price_change = change_elem.text if change_elem else 'N/A'
        
        change_percent_elem = soup.find('fin-streamer', {'data-field': 'regularMarketChangePercent'})
        change_percent = change_percent_elem.text if change_percent_elem else 'N/A'
        
        # Informasi tambahan
        summary_table = soup.find('table', {'data-test': 'summary-table'})
        additional_data = {}
        
        if summary_table:
            rows = summary_table.find_all('tr')
            for row in rows:
                cells = row.find_all('td')
                if len(cells) == 2:
                    key = cells[0].text.strip()
                    value = cells[1].text.strip()
                    additional_data[key] = value
        
        stock_data = {
            'symbol': symbol,
            'current_price': current_price,
            'price_change': price_change,
            'change_percent': change_percent,
            **additional_data,
            'scraped_at': datetime.now().isoformat()
        }
        
        return stock_data
        
    except Exception as e:
        print(f"Error scraping Yahoo Finance: {e}")
        return None

# Scraping multiple stocks
stocks = ['AAPL', 'GOOGL', 'MSFT', 'TSLA']
stock_data_list = []

for stock in stocks:
    print(f"Scraping data untuk {stock}...")
    data = scrape_yahoo_finance_stock(stock)
    if data:
        stock_data_list.append(data)
    time.sleep(1)  # Rate limiting

# Convert to DataFrame
stocks_df = pd.DataFrame(stock_data_list)
print(stocks_df)
```

### Case 4: Scraping E-commerce dengan Selenium

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import TimeoutException
import pandas as pd
import time

def scrape_tokopedia_products(search_term):
    # Setup Chrome driver
    options = webdriver.ChromeOptions()
    options.add_argument('--headless')  # Run in background
    options.add_argument('--no-sandbox')
    options.add_argument('--disable-dev-shm-usage')
    
    driver = webdriver.Chrome(options=options)
    
    try:
        # Navigasi ke Tokopedia
        search_url = f"https://www.tokopedia.com/search?st=product&q={search_term.replace(' ', '%20')}"
        driver.get(search_url)
        
        # Tunggu produk load
        wait = WebDriverWait(driver, 10)
        wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, '[data-testid="master-product-card"]')))
        
        # Scroll untuk load more products
        driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
        time.sleep(2)
        
        # Ekstrak data produk
        products = driver.find_elements(By.CSS_SELECTOR, '[data-testid="master-product-card"]')
        
        product_data = []
        for product in products[:20]:  # Ambil 20 produk pertama
            try:
                name_elem = product.find_element(By.CSS_SELECTOR, '[data-testid="spnSRPProdName"]')
                name = name_elem.text if name_elem else 'N/A'
                
                price_elem = product.find_element(By.CSS_SELECTOR, '[data-testid="spnSRPProdPrice"]')
                price = price_elem.text if price_elem else 'N/A'
                
                rating_elem = product.find_element(By.CSS_SELECTOR, '[data-testid="spnSRPProdRating"]')
                rating = rating_elem.text if rating_elem else 'N/A'
                
                seller_elem = product.find_element(By.CSS_SELECTOR, '[data-testid="spnSRPProdTabShop"]')
                seller = seller_elem.text if seller_elem else 'N/A'
                
                product_data.append({
                    'name': name,
                    'price': price,
                    'rating': rating,
                    'seller': seller,
                    'search_term': search_term,
                    'scraped_at': datetime.now().isoformat()
                })
                
            except Exception as e:
                print(f"Error extracting product data: {e}")
                continue
        
        return product_data
        
    except TimeoutException:
        print("Timeout waiting for products to load")
        return []
    except Exception as e:
        print(f"Error scraping Tokopedia: {e}")
        return []
    finally:
        driver.quit()

# Jalankan scraping
search_terms = ['laptop gaming', 'smartphone 5G']
all_products = []

for term in search_terms:
    print(f"Scraping produk untuk: {term}")
    products = scrape_tokopedia_products(term)
    all_products.extend(products)
    time.sleep(2)

# Convert to DataFrame
products_df = pd.DataFrame(all_products)
print(f"Total produk yang di-scrape: {len(products_df)}")
print(products_df.head())
```

## 6. Best Practices dan Troubleshooting

### 6.1 Error Handling

```python
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry
import time

def create_session_with_retries():
    session = requests.Session()
    
    # Retry strategy
    retry_strategy = Retry(
        total=3,
        backoff_factor=1,
        status_forcelist=[429, 500, 502, 503, 504],
    )
    
    adapter = HTTPAdapter(max_retries=retry_strategy)
    session.mount("http://", adapter)
    session.mount("https://", adapter)
    
    return session

def safe_scrape(url, headers=None):
    session = create_session_with_retries()
    
    try:
        response = session.get(url, headers=headers, timeout=10)
        response.raise_for_status()
        return response
    except requests.exceptions.RequestException as e:
        print(f"Error scraping {url}: {e}")
        return None
```

### 6.2 Rate Limiting

```python
import time
from functools import wraps

def rate_limit(calls_per_second=1):
    min_interval = 1.0 / calls_per_second
    last_called = [0.0]
    
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            elapsed = time.time() - last_called[0]
            left_to_wait = min_interval - elapsed
            if left_to_wait > 0:
                time.sleep(left_to_wait)
            ret = func(*args, **kwargs)
            last_called[0] = time.time()
            return ret
        return wrapper
    return decorator

@rate_limit(calls_per_second=0.5)  # Max 1 call per 2 seconds
def scrape_with_rate_limit(url):
    response = requests.get(url)
    return response.text
```

### 6.3 Data Cleaning dan Validasi

```python
import re
import pandas as pd

def clean_scraped_data(df):
    # Remove HTML tags
    df = df.applymap(lambda x: re.sub('<.*?>', '', str(x)) if isinstance(x, str) else x)
    
    # Strip whitespace
    df = df.applymap(lambda x: x.strip() if isinstance(x, str) else x)
    
    # Replace empty strings with NaN
    df = df.replace('', pd.NA)
    
    # Remove duplicate rows
    df = df.drop_duplicates()
    
    return df

def validate_data(df, required_columns):
    # Check required columns exist
    missing_cols = set(required_columns) - set(df.columns)
    if missing_cols:
        print(f"Missing columns: {missing_cols}")
        return False
    
    # Check for empty dataframe
    if df.empty:
        print("DataFrame is empty")
        return False
    
    # Check data completeness
    completeness = (df.count() / len(df)) * 100
    print("Data completeness:")
    print(completeness)
    
    return True
```

## 7. Penyimpanan dan Export Data

```python
import pandas as pd
import json
import sqlite3
from datetime import datetime

def save_scraped_data(data, format='csv', filename=None):
    if filename is None:
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        filename = f"scraped_data_{timestamp}"
    
    if isinstance(data, list):
        df = pd.DataFrame(data)
    elif isinstance(data, pd.DataFrame):
        df = data
    else:
        print("Data format not supported")
        return
    
    if format == 'csv':
        df.to_csv(f"{filename}.csv", index=False, encoding='utf-8')
    elif format == 'json':
        df.to_json(f"{filename}.json", orient='records', indent=2)
    elif format == 'excel':
        df.to_excel(f"{filename}.xlsx", index=False)
    elif format == 'sqlite':
        conn = sqlite3.connect(f"{filename}.db")
        df.to_sql('scraped_data', conn, if_exists='replace', index=False)
        conn.close()
    
    print(f"Data saved as {filename}.{format}")

# Contoh penggunaan
# save_scraped_data(news_df, format='csv', filename='detik_news_latest')
```

## 8. Monitoring dan Logging

```python
import logging
from datetime import datetime

def setup_scraping_logger():
    logger = logging.getLogger('scraper')
    logger.setLevel(logging.INFO)
    
    # File handler
    file_handler = logging.FileHandler(f'scraping_{datetime.now().strftime("%Y%m%d")}.log')
    file_handler.setLevel(logging.INFO)
    
    # Console handler
    console_handler = logging.StreamHandler()
    console_handler.setLevel(logging.INFO)
    
    # Formatter
    formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
    file_handler.setFormatter(formatter)
    console_handler.setFormatter(formatter)
    
    logger.addHandler(file_handler)
    logger.addHandler(console_handler)
    
    return logger

def log_scraping_session(func):
    logger = setup_scraping_logger()
    
    def wrapper(*args, **kwargs):
        logger.info(f"Starting scraping session: {func.__name__}")
        start_time = datetime.now()
        
        try:
            result = func(*args, **kwargs)
            end_time = datetime.now()
            duration = end_time - start_time
            
            logger.info(f"Scraping completed successfully in {duration}")
            logger.info(f"Records scraped: {len(result) if result else 0}")
            
            return result
            
        except Exception as e:
            logger.error(f"Scraping failed: {str(e)}")
            raise
    
    return wrapper
```

## Kesimpulan

Data scraping adalah skill penting dalam data wrangling yang memungkinkan kita mengakses data dari berbagai sumber web. Dengan memahami fundamentalnya, kita dapat mengotomatisasi proses pengumpulan data secara efisien.

Poin-poin penting:
1. Selalu hormati robots.txt dan Terms of Service
2. Implementasikan rate limiting untuk menghindari overload server
3. Gunakan error handling yang robust
4. Validasi dan clean data hasil scraping
5. Simpan data dalam format yang sesuai dengan kebutuhan analisis

## Tugas dan Latihan

### Tugas 1: Basic Web Scraping
Lakukan scraping pada website berita lokal (misal, [kompas](https://indeks.kompas.com/), [cnnindonesia](https://www.cnnindonesia.com/indeks), [tempo](https://www.tempo.co/indeks)) dan ekstrak:
- Judul artikel
- Tanggal publikasi
- Ringkasan artikel
- Link artikel

### Tugas 2: Dynamic Content Scraping
Gunakan Selenium untuk scraping website e-commerce dan ambil:
- Nama produk
- Harga
- Rating
- Jumlah review

### Tugas 3: Scrapping dengan API Integration
Integrasikan hasil scraping dengan penyimpanan database dan buat sistem monitoring untuk tracking perubahan data.

<!-- ### Tugas 4: Error Handling dan Optimization
Implementasikan robust error handling dan optimization untuk scraping large-scale data dengan rate limiting yang appropriate. -->