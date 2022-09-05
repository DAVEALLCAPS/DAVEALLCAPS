## Scraping the Web with BeautifulSoup4 and Requests

Python is used extract massive amounts of data from the web, today we will learn how to use requests and BeautifulSoup4 to scrape a basic HTML web page. Our test site is going to come from [scrapethissite.com](https://www.scrapethissite.com/), this site is made for the purpose of teaching people the art of web scraping and the page we will be working with is [Countries of the World: A Simple Example](https://www.scrapethissite.com/pages/simple/). 

### Test Page Structure
![Screenshot 2022-09-05 090557.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662390368730/60RguOLGR.png align="left")

The page's description reads: 
>A single page that lists information about all the countries in the world. Good for those just get started with web scraping. Practice looking for patterns in the HTML that will allow you to extract information about each country. Then, build a simple web scraper that makes a request to this page, parses the HTML and prints out each country's name.

So we are going to do just that, first things first we need to take a look at the structure of the page. So I am going to **highlight** one of the country names, **right click**, and **click inspect**.

![Screenshot 2022-09-05 091121.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662390743330/6fqAQEuFD.png align="left")

This should bring up the browser's developer tools and actually highlight where in the HTML this element is located.

![Screenshot 2022-09-05 091438.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662390886090/hfJjxW1wg.png align="left")
Our developer tools shows us that the country name is located in an **h3 tag** and has a **class="country-name"**. This information will allow us to easily locate all of the country names in the document using BeautifulSoup so let's jump into some code.

### Modules Used
The two modules we need to install for this project are:
- requests
- beautifulsoup4

We can install them using pip with the respective commands:
```
pip install requests
```
```
pip install beautifulsoup4
```
And now we can begin writing our script.

### Coding the Scraper
Let's go ahead and import our newly installed modules:

```py
import requests
from bs4 import BeautifulSoup
``` 
Set our test page URL:

```py
testURL = 'https://www.scrapethissite.com/pages/simple/'
``` 
Make a request to that page:

```py
page = requests.get(testURL)
```  
Turn the page into soup. Running the document through Beautiful Soup gives us a BeautifulSoup object [(more from the official documentation)](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#beautifulsoup), which represents the document as a nested data structure:

```py
soup = BeautifulSoup(page.text, "html.parser")
``` 
Declare an empty list to append the country names to:
```py
country_names = []
```
Loop through all names in our soup using the [find_all method](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#find-all) for any h3 tag with the class 'country-name' and add it to the list with all whitespace stripped:
```py
for names in soup.find_all('h3', class_='country-name'):
    country_names.append(names.text.strip())
```
Now we have a list full of country names, let's print to make sure, and the len() is the ensure we have captured all 250 items from the page.
```py
print(country_names)

print('There is', len(country_names), 'country names in our list.')
```
### Successful Output
We have completed the scraper, let's save and run it in all of it's glory.

![Screenshot 2022-09-05 093815.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662392300088/sJB7kwoSK.png align="left")
