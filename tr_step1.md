## Problem - Go ile Web Scraping

Bu senaryoda, **Go** ve Go dilinde çok tercih edilen scraping kütüphanesi **"gocolly/colly"** kullanarak **"[books.toscrape](https://books.toscrape.com/)"** web sitesinden veri kazıma (web scraping) yapacağız.

>Bu senaryo sadece eğitim amaçlıdır. Web sitesi sahibinin izni olmadan web sitesinden veri kazımak yasa dışı olabilir. Web sitesini taramadan önce robots.txt dosyasını kontrol etmeniz ve etik kurallara uymanız önemlidir. Web sitesine aşırı yük bindirmemek ve yasalara uymak için web sitesi sahibinin izin almanız da gerekebilir. Bizim örneğimizde, [books.toscrape](https://books.toscrape.com/) sitesi tamamen test amaçlı oluşturulmuş bir site olduğu için bu siteyi kullanıyoruz.

### 🛠️ Gerekli Ortamların Ayarlanması 🛠️

Öncelikle, bir klasör ve içerisine `main.go` adında bir dosya oluşturalım.

```bash
mkdir book-scraper && cd book-scraper
touch main.go
```

Sistemde Go yüklüyse, gerekli paketi yüklemek için terminalde aşağıdaki komutu çalıştırabiliriz:
```bash
go get -u github.com/gocolly/colly
```

Go yüklü değilse, Alpine Linux için aşağıdaki komutu çalıştırarak Go'yu yükledikten sonra yukarıdaki komutu çalıştırabiliriz:
```bash
apk add go
```

### 🚀 Uygulama Adımları 🚀
Aşağıdaki kodu `main.go` dosyasına yapıştırarak ilk aşamayı tamamlayabiliriz.
```go
package main

import (
    "fmt"
    "github.com/gocolly/colly"
)

func main() {
    // Colly instance
    collector := colly.NewCollector(
        colly.AllowedDomains("books.toscrape.com"),
    )

    // On HTML element callback
    collector.OnHTML("h3", func(element *colly.HTMLElement) {
        bookTitle := element.Text
        fmt.Println("Kitap Adı:", bookTitle)
    })

    // Start scraping
    collector.Visit("https://books.toscrape.com/")
}
```

**Açıklama:**
  `main` fonksiyonu içerisinde:
    * `colly.NewCollector` fonksiyonu ile bir `colly` nesnesi oluşturuyoruz.
    * `colly.AllowedDomains` fonksiyonu ile scraping yapılacak alan adını ("books.toscrape.com") belirliyoruz.
    * `OnHTML` fonksiyonu ile HTML öğelerine (h3 etiketleri) rastlandığında tetiklenecek bir callback fonksiyonu tanımlıyoruz.
        * Bu callback fonksiyonu içerisinde, `element.Text` ile h3 etiketinin içeriğini (kitap adını) alıyoruz ve ekrana yazdırıyoruz.
    * `Visit` fonksiyonu ile scraping işlemini başlatıyoruz.


### ▶️ Kodu Çalıştırma ▶️
Aşağıdaki komutu çalıştırarak Go ile web scraping işlemini başlatabiliriz:
```bash
go run main.go
```

**Çıktı:**

```
Kitap Adı: A Light in the ...
Kitap Adı: Tipping the Velvet
Kitap Adı: Soumission
Kitap Adı: Sharp Objects
...
```

Şimdi biraz daha karmaşık bir örnek yapalım. Bu sefer kitap adı, fiyat ve resim bilgilerini alıp bir dosyaya yazdıralım.
Bir sonraki adıma ilerleyebiliriz. ⏩