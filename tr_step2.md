## Problem - Go ile Web Scraping

### Hangi Verileri Kazıyacağız?
Veri kazıma işlemi yaparken, ilgili web sitesini ziyaret edip hangi verileri kazıyacağımıza karar vermemiz ve verilerin `html` yapısını incelememiz gerekmektedir.

Örnek olarak bizim [sitemizde](https://books.toscrape.com/) kitapların listelendiği bir sayfa var. Bu sayfada her bir kitap için kitap adı, fiyatı ve resim URL'si bulunmaktadır. Biz bu verileri kazıyarak ekrana yazdırmak istiyoruz.

![](scraper_ss.png)

Yukarıdaki resimde de görüldüğü gibi, sayfaya sağ tıklayıp "Inspect" seçeneğine tıkladığımızda, sayfanın `html` yapısını inceleyebiliriz. 
Ok ile belirtilen araç, istediğimiz veriye tıklayarak detaylı bilgiye ulaşmamızı sağlar. 
Yine yeşil ile belirtilen alanda hangi elementin hangi tag altında olduğunu görebiliriz.

Bizim kullanacağımız verilerin yapısı şu şekilde:
* Başlık: h3
* Fiyat: price_color
* Resim URL: img->src (child attribute)

### 👩🏻‍💻 Kodlamaya Devam 👨🏻‍💻

Bu sefer dosyaya yazma işlemi de yapacağız. Bu yüzden öncelikle "os" paketini import ediyoruz.
```go
import (
	"fmt"
	"github.com/gocolly/colly"
	"os"
)
```

Kitap verilerini tutacak olan `Book` struct'ını oluşturuyoruz.
```go
type Book struct {
    Title string
    Price string
    ImageURL string
}
```

`main` fonksiyonunun en başına aşağıdaki kodu ekliyoruz. Bu kod, `kitap_verileri.txt` adında bir dosya oluşturacak.
```go
	// Create a file
    file, err := os.Create("kitap_verileri.txt")
    if err != nil {
        fmt.Println("Dosya oluşturulamadı:", err)
        return
    }
    defer file.Close()

	// Colly instance...
```

Colly instance oluşturduktan hemen sonra `OnHTML` fonksiyonunu aşağıdaki gibi güncelliyoruz.
```go
	// On HTML element callback
	collector.OnHTML(".product_pod", func(element *colly.HTMLElement) {
		book := Book{}

		book.Title = element.ChildText("h3 a")
		book.Price = element.ChildText(".price_color")
		book.ImageURL = element.ChildAttr("img", "src")

		line := fmt.Sprintf("Kitap Adı: %s\nFiyat: %s\nResim URL: %s\n\n", book.Title, book.Price, book.ImageURL)
		fmt.Println(line)

		// Dosyaya yazma işlemi
		_, err := file.WriteString(line)
		if err != nil {
			fmt.Println("Dosyaya yazma hatası:", err)
		}
	})
```

**Açıklama:**
`main` fonksiyonu içerisinde:
    * `Book` adında bir struct oluşturuyoruz. Bu struct, kazınacak verileri (kitap adı, fiyat, resim URL'si) saklayacak.
    * `OnHTML` fonksiyonu ile HTML öğelerine (`.product_pod` sınıfı) rastlandığında tetiklenecek bir callback fonksiyonu tanımlıyoruz.
        * Bu callback fonksiyonu içerisinde:
            * `element.ChildText` ve `element.ChildAttr` fonksiyonlarını kullanarak `.product_pod` öğesinin altındaki h3 etiketi, `.price_color` sınıfı ve `img` etiketi ile ilişkili verileri (`book.Title`, `book.Price`, `book.ImageURL`) alıyoruz.
            * Alınan verileri formatlı bir şekilde ekrana yazdırıyoruz.

### ▶️ Kodu Çalıştırma ▶️

```bash
go run main.go
```

**Çıktı:**

```
Kitap Adı: A Light in the ...
Fiyat: £51.77
Resim URL: media/cache/2c/da/2cdad67c44b002e7ead0cc35693c0e8b.jpg
...
```

