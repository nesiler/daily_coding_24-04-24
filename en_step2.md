## Problem - Web Scraping with Go

### What Data Will We Scrape?
When performing data scraping, it is necessary to visit the relevant website and decide which data to scrape by examining the `html` structure.

For example, on our [website](https://books.toscrape.com/), there is a page listing books. On this page, for each book, there is a book title, price, and image URL. We want to scrape this data and print it to the screen.

![](scraper_ss.png)

As seen in the image above, when we right-click on the page and select "Inspect," we can examine the `html` structure of the page.
The tool indicated by the arrow allows us to click on the desired data to access detailed information.
In the area highlighted in green, we can see which element is under which tag.

The structure of the data we will use is as follows:
* Title: h3
* Price: price_color
* Image URL: img->src (child attribute)

### 👩🏻‍💻 Continuing with Coding 👨🏻‍💻

This time, we will also perform a file writing operation. Therefore, we first import the "os" package.
```go
import (
	"fmt"
	"github.com/gocolly/colly"
	"os"
)
```

We create the `Book` struct to hold the book data.
```go
type Book struct {
    Title string
    Price string
    ImageURL string
}
```

We add the following code snippet to the beginning of the `main` function. This code will create a file named `kitap_verileri.txt`.
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

After creating the Colly instance, we update the `OnHTML` function as follows.
```go
	// On HTML element callback
	collector.OnHTML(".product_pod", func(element *colly.HTMLElement) {
		book := Book{}

		book.Title = element.ChildText("h3 a")
		book.Price = element.ChildText(".price_color")
		book.ImageURL = element.ChildAttr("img", "src")

		line := fmt.Sprintf("Book Title: %s\nPrice: %s\nImage URL: %s\n\n", book.Title, book.Price, book.ImageURL)
		fmt.Println(line)

		// File writing operation
		_, err := file.WriteString(line)
		if err != nil {
			fmt.Println("Dosyaya yazma hatası:", err)
		}
	})
```

**Explanation:**
Inside the `main` function:
    * We create a struct named `Book` to store the data to be scraped (book title, price, image URL).
    * We define a callback function to be triggered when HTML elements (`.product_pod` class) are encountered using the `OnHTML` function.
        * Inside this callback function:
            * Using the `element.ChildText` and `element.ChildAttr` functions, we retrieve the data associated with the h3 tag, `.price_color` class, and img tag under the `.product_pod` element (`book.Title`, `book.Price`, `book.ImageURL`).
            * We print the obtained data to the screen in a formatted way.

### ▶️ Running the Code ▶️

```bash
go run main.go
```

**Output:**

```
Book Title: A Light in the ...
Price: £51.77
Image URL: media/cache/2c/da/2cdad67c44b002e7ead0cc35693c0e8b.jpg
...
```

