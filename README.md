# ECC-NIN-ChatAgent-Doni_Wahyudi-Batch_9
# 📰 Daily News Workflow (n8n)

Workflow ini dibuat untuk **mengambil berita harian**, **meringkasnya dengan AI**, dan **mengirimkan hasilnya ke Telegram** maupun melalui **Webhook**. Workflow ini berjalan otomatis setiap hari pukul **08:00 WIB**, serta bisa dipanggil secara manual melalui **Webhook**.

---
<img width="2299" height="1179" alt="image" src="https://github.com/user-attachments/assets/34e11e75-13fa-4f3d-9239-a023adc1efa1" />


## ⚙️ Cara Kerja Workflow

### 1. **Pemicu (Trigger)**
- **Schedule Trigger**  
  Workflow otomatis berjalan setiap hari jam 08:00 WIB.  
- **Webhook**  
  Workflow juga bisa dipanggil manual dengan melakukan **POST** ke endpoint `/news`.  
  - Body request harus berisi parameter `message` (misalnya: `"Give me news about Bitcoin"`).  
  - Informasi ini diekstrak oleh node **Information Extractor** menjadi `q` (search query).

---

### 2. **Pengambilan Data Berita**
Workflow menggunakan **dua sumber berita** untuk memperkaya hasil:

- **GNews API** (`https://gnews.io/api/v4/search`)  
  - Query: kata kunci `q` dari user.  
  - Bahasa: Inggris (`en`).  
  - API key disediakan di workflow.

- **NewsAPI.org** (`https://newsapi.org/v2/everything`)  
  - Query: kata kunci `q`.  
  - Bahasa: Inggris (`en`).  
  - Sortir: `publishedAt`.  
  - Maksimal 20 artikel.  
  - API key disediakan di workflow.  

Masing-masing hasil dipetakan ke field `articles` agar konsisten.

---

### 3. **Penggabungan & Agregasi**
- Node **Merge** menggabungkan hasil dari GNews dan NewsAPI.  
- Node **Aggregate** menyatukan artikel-artikel ke dalam satu array `articles`.

---

### 4. **Pemrosesan dengan AI (Google Gemini)**
- **Model Google Gemini** dipakai untuk menghasilkan ringkasan.  
- Ada **dua jalur ringkasan**:
  1. **Custom Query (via Webhook)**  
     - AI menyeleksi 7 artikel paling relevan sesuai query.  
     - Output diformat ke **Markdown**, termasuk URL artikel.  
     - Diawali dengan tanggal hari ini.
  2. **Daily Digest (via Schedule Trigger)**  
     - Query default = `"AI"`.  
     - AI menyeleksi 7 artikel tentang perkembangan AI.  
     - Output diformat ke **Markdown** dengan tanggal.  

---

### 5. **Output**
- **Respond to Webhook**: Jika workflow dipanggil manual, hasil ringkasan langsung dikembalikan ke client.  
- **Telegram Bot**: Jika workflow berjalan otomatis, hasil ringkasan dikirim ke akun Telegram tertentu menggunakan bot.

---

## 📌 Contoh Output
### From Webhook
<img width="1016" height="849" alt="image" src="https://github.com/user-attachments/assets/6886c7c0-548f-4b89-a4e1-3c518283a08f" />

### From Telegram
<img width="741" height="1069" alt="image" src="https://github.com/user-attachments/assets/ac4c7601-e63a-447a-80b1-0570e40c9509" />

### Full Output Example
Good Morning, Today is 2025/09/27, Here is the latest news about AI：

1.  Intel India Advocates for AI-Enabled Education and Policy Support
    Intel India's MD is pushing for policy interventions, including GST relief on computers for educational use, to bridge the digital divide. The company is collaborating with edtech firms to integrate AI-enabled learning tools and promote the adoption of AI PCs, emphasizing local AI processing for enhanced privacy and scalability.
    <br> URL: https://www.financialexpress.com/shorts/life/technology/gst-reliefoncomputersforchildren-using-aapar-id-validation-could-bridge-digital-divide-intel-india-md-3991162/

2.  AI in Healthcare: A Look at 'Dr. Bot' and Saving Lives
    A new book, "Dr. Bot," explores the evolving role of Artificial Intelligence in medicine and healthcare. While AI is already assisting with consultation notes and accelerating scan readings, the book delves into the challenging question of whether AI, specifically "chatdocs," can truly serve as reliable medical practitioners and save lives, amidst signs that chatbot progress may be slowing.
    <br> URL: https://www.irishtimes.com/culture/books/review/2025/09/27/dr-bot-why-doctors-can-fail-us-and-how-ai-could-save-lives-but-is-a-chatdoc-the-medic-for-you/

3.  Accenture Cuts Over 11,000 Jobs in AI-Driven Restructuring
    Global consulting giant Accenture has reduced its workforce by more than 11,000 employees over the last three months. This strategic move is part of the firm's broader restructuring efforts, driven by a pivot towards artificial intelligence and a focus on operational efficiencies.
    <br> URL: https://www.freepressjournal.in/tech/accenture-announces-over-11000-job-cuts-amid-ai-driven-restructuring

4.  AI Weaponizing Fake Narratives in Social Media Love Story Hoax
    A viral "real-life love story" that captivated thousands on social media in Kerala has been exposed as fake. The incident serves as a stark warning about how AI is being used to weaponize false narratives, exploiting human empathy to mislead users and turn emotional responses into a powerful tool for manipulation.
    <br> URL: https://www.newindianexpress.com/states/kerala/2025/Sep/27/nothing-real-in-ashwathi-rahuls-real-life-love-story

5.  OpenAI Data Suggests AI May Soon Take More Jobs, Despite Current Status
    OpenAI has launched GDPval, a new benchmark designed to qualitatively assess AI's capability to perform real-world jobs, including legal briefs, engineering blueprints, nursing care plans, and financial reports. The data suggests that while AI isn't widely replacing human jobs yet, this could change in the near future.
    <br> URL: https://biztoc.com/x/c73e4bb4e01cd8dd

6.  Anthropic Plans Significant Expansion Amidst Rapid Client Growth
    AI company Anthropic is set to triple its global workforce and expand its applied AI team by five times in 2025. This aggressive growth follows a remarkable increase in its business clients, from approximately 1,000 to over 300,000, in just two years, signaling robust demand for its AI solutions.
    <br> URL: https://biztoc.com/x/1a8c123d61dec6ad

7.  Meta AI App Introduces New "Vibes" Feed for AI-Generated Content
    The Meta AI app is rolling out a new "Vibes" feed, which will prioritize and showcase AI-generated videos. This update marks the next iteration of the Meta AI app, with a heightened focus on visual content created by artificial intelligence and enhanced community features.
    <br> URL: https://www.thurrott.com/a-i/327348/meta-ai-app-gets-new-vibes-feed-of-ai-generated-content


## 🚀 Cara Menggunakan

### 1. Import Workflow
- Buka aplikasi **n8n**.  
- Pilih menu **Import JSON**.  
- Upload file `Daily News.json`.

### 2. Set Credentials
Tambahkan kredensial berikut di n8n:
- **GNews API** → masukkan API key ke dalam Query Auth dengan name apikey dan value berupa API Key.  
- **NewsAPI** → masukkan API key ke dalam Header Auth dengan name X-Api-Key dan value berupa API Key.  
- **Telegram API** → masukkan bot token API Key ke dalam credentials telegram.  
- **Google Gemini API (Palm API)** → masukkan API key ke dalam credentials Google Gemini.

### 3. Aktifkan Workflow
- Pastikan workflow dalam keadaan **Active**.  
- Workflow akan otomatis berjalan setiap hari pukul **08:00 WIB**.

### 4. Gunakan Webhook (opsional, manual run)
- Edit html di notepad atau notepad++
- Cari variabel WEBHOOK_URL, kemudian replace dengan webhook url dari n8n
- Jangan lupa untuk menyimpan (Ctrl+S) html nya agar perubahannya terupdate

### 5. Cek Telegram
- Ringkasan harian otomatis dikirimkan ke akun Telegram yang sudah dikonfigurasi.

## 📖 Catatan

- Workflow ini berjalan di timezone Asia/Jakarta.
- Default query untuk Daily Digest adalah "AI".
- Jika dipanggil via Webhook, query bisa bebas (misalnya: "Bitcoin", "Climate Change", "SpaceX").

## 🔗 Heads-Up

Anda bisa mengembangkan workflow lebih lanjut dengan menambahkan:
- Integrasi email untuk mengirim newsletter otomatis.
- Kanal distribusi lain seperti Slack atau WhatsApp.
- Fitur penyaringan berita berdasarkan kategori (misalnya: politik, teknologi, olahraga).
Untuk pertanyaan atau kolaborasi, silakan buka issue atau pull request di repository ini.

## Terimakasih..!!
