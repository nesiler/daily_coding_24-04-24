## Problem - Web Scraping with Go

In this scenario, we will perform web scraping using **Go** and the widely used scraping library in Go, **"gocolly/colly"**, from the website **"[books.toscrape](https://books.toscrape.com/)"**.

>This scenario is for educational purposes only. Web scraping without the permission of the website owner may be illegal. Before scraping a website, it is important to check the robots.txt file and adhere to ethical rules. Obtaining permission from the website owner may also be necessary to avoid overloading the website and comply with laws. In our example, we are using the [books.toscrape](https://books.toscrape.com/) website, which is created purely for testing purposes.

### 🛠️ Setting Up the Environment 🛠️

Firstly, let's create a folder and a file named `main.go` inside it.

```bash
mkdir book-scraper && cd book-scraper
touch main.go
```

If Go is installed on the system, we can run the following command in the terminal to install the required package:
```bash
go get -u github.com/gocolly/colly
```

If Go is not installed, after installing Go for Alpine Linux, we can run the above command.

### 🚀 Application Steps 🚀

We can complete the first stage by pasting the following code into the `main.go` file.

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
        fmt.Println("Book Title:", bookTitle)
    })

    // Start scraping
    collector.Visit("https://books.toscrape.com/")
}
```

**Explanation:**
  Inside the `main` function:
    * We create a `colly` object with the `colly.NewCollector` function.
    * We specify the domain to be scraped with the `colly.AllowedDomains` function as "books.toscrape.com".
    * We define a callback function to be triggered when HTML elements (h3 tags) are encountered using the `OnHTML` function.
        * Inside this callback function, we retrieve the content of the h3 tag (book title) with `element.Text` and print it to the screen.
    * We initiate the scraping process with the `Visit` function.


### ▶️ Running the Code ▶️

We can start web scraping with Go by running the following command:
```bash
go run main.go
```

**Output:**

```
Book Title: A Light in the ...
Book Title: Tipping the Velvet
Book Title: Soumission
Book Title: Sharp Objects
...
```

Now let's do a slightly more complex example. This time, let's extract book titles, prices, and image URLs and write them to a file.
We can proceed to the next step. ⏩