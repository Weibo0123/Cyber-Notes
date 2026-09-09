# Web Scraping with Python

## What Is Web Scraping?

Retrieving HTML content from websites and extracting information from it via automated scripts, used for data science/ML datasets, market research, content aggregation, and combining data across sites.

⚠️ Web scraping may be restricted by a site's terms of use or by data-protection/privacy law — check before scraping a target or reusing collected data.

## Three Core Libraries

| Library | Role |
|---|---|
| **Requests** | Sends HTTP requests, retrieves raw content — no parsing built in |
| **Beautiful Soup** | Parses HTML/XML — not a full scraper by itself, usually paired with `requests` |
| **Scrapy** | A complete framework: requests, parsing, data processing/storage, request optimization, user-agent handling, middleware |

Beautiful Soup is simpler and good for smaller jobs; Scrapy is heavier but built for large/complex scraping projects.

## `requests` Alone

For simple cases, the raw response may be enough without any HTML parser at all:
```python
import requests

URL = "https://letsdefend.io"
response = requests.get(URL)
print(response.text)     # raw response body, as text
```

## Beautiful Soup Workflow

```
1. Send an HTTP request (requests)
2. Parse the response (BeautifulSoup)
3. Search the parsed HTML for the needed elements
```
```python
import requests
from bs4 import BeautifulSoup

URL = "https://letsdefend.io"

page = requests.get(URL)
soup = BeautifulSoup(page.content, "html.parser")

results = soup.find("meta", {"name": "description"})
print(results)
```
| Line | Purpose |
|---|---|
| `requests.get(URL)` | Sends the GET request, stores the response |
| `BeautifulSoup(page.content, "html.parser")` | Parses the response body as HTML |
| `soup.find("meta", {"name": "description"})` | Finds the **first** `<meta>` tag with `name="description"` |

```bash
pip3 install bs4
# if pip3 itself is missing on Ubuntu/Debian:
sudo apt install python3-pip
```

## Selecting Elements with CSS Selectors

For more targeted extraction than `.find()`, Beautiful Soup also supports CSS selectors via `.select()`/`.select_one()` — useful for pulling structured data out of something like an HTML table:

```python
import requests
from bs4 import BeautifulSoup

URL = "https://www.projecthoneypot.org/list_of_ips.php"

html_content = requests.get(URL)
soup = BeautifulSoup(html_content.text, "html.parser")

ip_addresses = []

for row in soup.select("table.manmx tr")[1:]:
    ip_cell = row.select_one("td a.bnone")
    if ip_cell:
        ip_addresses.append(ip_cell.text.strip())

for ip in ip_addresses:
    print(ip)
```
⚠️ *Correction from the original notes:* the source passed `html_content` (the whole `requests` **Response object**) directly into `BeautifulSoup()`. That's wrong — Beautiful Soup needs the actual markup, a string or bytes, not the Response wrapper. It should be `html_content.text` (as above) or `html_content.content`.

| Piece | Purpose |
|---|---|
| `soup.select("table.manmx tr")` | CSS selector: all `<tr>` inside a `<table class="manmx">` |
| `[1:]` | Skips index 0 — here, the header row |
| `row.select_one("td a.bnone")` | First `<a class="bnone">` inside a `<td>` in this row |
| `.text.strip()` | Extracts the visible text, trims whitespace |
| `ip_addresses.append(...)` | Collects results into a list |

## Saving Scraped Data

```python
with open("iplist.txt", "w") as file:
    for ip in ip_addresses:
        print(ip)
        file.write(ip + "\n")
```
`"w"` overwrites the file; each entry is written on its own line via the trailing `\n`. This exact file (`iplist.txt`) is the input the IDS application in file 04 reads its monitored-IP list from — this scraper is one realistic way that list gets populated in the first place.

## Scrapy — the Framework Approach

Spiders live in a Scrapy project's `spiders` directory:
```python
import scrapy

class DescriptionSpider(scrapy.Spider):
    name = "description"
    start_urls = ["https://letsdefend.io"]

    def start_requests(self):
        for url in self.start_urls:
            yield scrapy.Request(url, callback=self.parse_description)

    def parse_description(self, response):
        description = response.xpath('//meta[@name="description"]/@content').get()
        print("Description:", description)
```
```bash
scrapy crawl description
scrapy crawl description --nolog     # suppress Scrapy's normal verbose logging
```

### XPath
Scrapy commonly locates elements via **XPath** expressions instead of (or alongside) CSS selectors:
```python
response.xpath('//meta[@name="description"]/@content').get()
```
This selects the `content` attribute of a `<meta>` tag whose `name` attribute is `description` — same target as the Beautiful Soup `.find()` example above, expressed in XPath instead of BS4's own query syntax.

## Cheat Sheet

```
requests.get(url).text          raw HTML, no parsing
BeautifulSoup(html, "html.parser")     parse HTML/XML
soup.find(tag, {"attr": "val"})           first matching element
soup.select("css selector")                 all matching elements (CSS syntax)
soup.select_one("css selector")               first matching element (CSS syntax)
element.text.strip()                           extract & clean text

Scrapy: class Spider(scrapy.Spider): start_requests() + parse()
response.xpath('//tag[@attr="val"]/@target').get()    XPath extraction

open(file, "w") + file.write(x + "\n")     save results, one per line

⚠️ Pass response.text / response.content into BeautifulSoup — NOT the raw Response object
```
