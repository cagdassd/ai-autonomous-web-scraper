# Case Study: Otonom Yapay Zeka ile Akıllı Web Otomasyonu (n8n + Browser Use API)

## 📌 Proje Özeti
Bu projede, geleneksel kurallı botların (RPA/Selenium) aksine, kararlar alabilen ve engelleri kendi başına aşabilen otonom bir yapay zeka ajanı kullanılarak uçtan uca bir veri çekme (web scraping) otomasyonu geliştirilmiştir.

Sistem, bir e-ticaret platformunda (Trendyol) arama yapma, verileri analiz etme ve istenen formata getirip Google Sheets'e kaydetme işlemlerini insan müdahalesi olmadan gerçekleştirir.

## 🛠️ Kullanılan Teknolojiler
* **n8n:** İş akışı (workflow) yönetimi ve API entegrasyonları için ana orkestrasyon aracı.
* **Browser Use API:** Karar alabilen, tarayıcıyı bir insan gibi kontrol eden otonom AI Agent.
* **Google Sheets API:** Çekilen verilerin depolandığı veritabanı.
* **LLM (Büyük Dil Modeli):** Tarayıcı ajanının arkasındaki akıl.

## 🏗️ Sistem Mimarisi ve İş Akışı (Workflow)
Sistem n8n üzerinde çalışan asenkron bir polling (döngü) mimarisi üzerine kurulmuştur:
1. **Trigger:** Süreç manuel veya zamanlanmış olarak tetiklenir.
2. **Task Dispatch (POST):** Yapay zeka ajanına doğal dilde (prompt) görev verilir: *"Trendyol'a gir, login ol, 'laptop' araması yap ve ilk 5 ürünün adını, fiyatını, puanını çek."*
3. **Status Polling (GET & IF):** API asenkron çalıştığı için n8n, işlemin bitip bitmediğini periyodik (5 saniyede bir) olarak kontrol eder.
4. **Data Parsing (Code):** Yapay zekanın döndürdüğü ham metin veya JSON verisi, JavaScript ile işlenip temizlenir.
5. **Data Export:** Temizlenen veri Google Sheets tablolarına satır satır yazılır.

## 🧠 Karşılaşılan Zorluklar ve AI Tarafından Üretilen Otonom Çözüm
Geleneksel otomasyon sistemlerinde en büyük sorun site güncellemeleri ve anti-bot/CAPTCHA korumalarıdır. Bu test sırasında çok çarpıcı bir olay yaşanmıştır:

* **Sorun:** Yapay zeka ajanı, Trendyol login ekranında anti-bot (CAPTCHA) kısıtlamasıyla karşılaşmıştır. (Klasik bir RPA botu burada hata verip dururdu).
* **AI Çözümü:** Ajan, görevin asıl amacının login olmak değil, **"laptop verilerini çekmek"** olduğunu idrak etmiş, login adımını atlayarak doğrudan arama sonuç sayfasına (URL) yönlenmiş ve verileri başarıyla çekmiştir.

**Browser Use Log Kaydı:**
> *"Successfully extracted the first 5 laptop products... Note: The login attempt was bypassed due to anti bot/CAPTCHA restrictions encountered... but the search and extraction were completed successfully by navigating directly to the search results."*

## 🎯 Sonuç ve Kurumsal Kazanımlar
1. **Esneklik:** CSS selector veya XPath kodlamaya gerek kalmadan, sadece doğal dille komut verilerek web otomasyonu yapılabilmektedir.
2. **Dayanıklılık (Resilience):** Site tasarımı değişse bile AI görsel olarak sayfayı anladığı için otomasyon kırılmaz. Hatalara karşı kendi kendine alternatif yollar (bypass) bulabilir.
3. **Hız:** Saatler sürecek script yazım süreçleri, n8n ve AI entegrasyonu ile dakikalar içinde deploy edilebilir hale gelmiştir.
