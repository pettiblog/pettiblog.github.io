---
title: "Data Analysis with Python"
excerpt: "Learn the basics of Data Analysis with Python"
modified: 2016-05-31
category: tech
header:
  overlay_color: "#000"
  overlay_filter: "0.5"
  overlay_image: programming_croped.jpg
  teaser: python.png
comments: true
---

{% include toc title="Overview" icon="file-text" %}

On March 2016 I took a [Udacity Nanodegree](https://www.udacity.com/course/data-analyst-nanodegree--nd002) to become a **Python Data Analyst**. I really enjoyed it, but sadly most of the assignments were about writing very specific programs that I never used. However, it did open the door to writing cooler programs that I use almost daily (like my [smart alarm clock](https://github.com/nickpettican/Alarm-Pi)!). So in this page I will show you how to analyse the most common types of data.

Just a **word of caution**, I am assuming you have a basic understanding of Python data types, functions and formatting. If not be sure to check out [Derek Banas's video](https://www.youtube.com/watch?v=N4mEzFDjqtA) on Python programming to learn some of the basics.

## CSV data

First off, of course, is the CSV format. The CSV format is the most common import and export format for datasets, spreadsheets and other databases. It basically contains rows, each with columns that are usually separated by commas. It's fairly straightforward to import and export these files in Python:

```python
# import csv library
import csv

# import data
data = [line.strip().split(',') for line in open('folder/infile.csv', 'r')]

# export data
with open('folder/outfile.csv', 'wb') as out:
    writer = csv.writer(out)
    writer.writerows(data)
```

Short and simple. Now lets look at a more in-depth breakdown of a simple program and how this is implemented:

```python
# This is a guide for CSV file import and export with some bonus data manipulation ;)
# if you're new to Python, note that the main function calls all the others
# so go down to def main() to see what's happening

# you can import libraries in one line by separating with commas
import os, csv

# you could just use FILE = 'folder/subfolder/file.csv'
# by being in caps these variables are global
FILE_IS_HERE = 'folder/subfolder/'
FILE_NAME = 'file.csv'
OUTPUT_NAME = 'outfile.csv'

# this might be easier for beginners, but it's much longer
# see the function bellow this one for the equivalent as a list comprehension
def import_file_nested(datafile):
    data = []
    for line in open(datafile, 'r'):
        cleaned = line.strip().split(',')
        data.append(cleaned)
    return data

# this does the same as the above function but much shorter and 'pythonic'
# confusing? this is a list comprehension!
def import_file_list_comprehension(datafile):
    return [line.strip().split(',') for line in open(datafile, 'r')]
    # one line, that's all you need ladies and gentlemen!

# just in case you want to import the data into a dictionary
# you can use dictionary comprehension!
def import_file_dictionary(datafile):
    # first you have to import as list of lists
    data = [line.strip().split(',') for line in open(datafile, 'r')]
    # and now we can assign the first element of each line as the key
    # and the rest as the values for those keys
    return {line[0]: line[1:] for line in data}

# just having some fun here
def scrambled_eggs(data):
    # this finds the length of the longest list in data 
    max_len = max(len(line) for line in data)
    # this will find all the lines that are shorter than the longest
    # and add 'egg' until they reach the length
    for line in data:
        if len(line) < max_len:
            for i in range(len(line), max_len):
                line.append('egg')
    # this will add 'egg' to every line and switch rows to columns
    return zip(*[line.append('egg') for line in data])

# this will output the data to a new CSV file
def output_data(new_data, outfile):
    with open(outfile, 'wb') as outcsv:
        # we are using the csv module here
        writer = csv.writer(outcsv)
        # warning! each line in new_data must contain a list
        writer.writerows(new_data)

# the main function will call the import and export functions
def main():
    # tell it where the file is with:
    datafile = os.path.join(FILE_IS_HERE, FILE_NAME)
    # or you could just use datafile = 'folder/subfolder/file.csv'

    # then you can import the file into a list of lists with this function
    data = import_file_nested(datafile)
    # or use the shorter funtion:
    data = import_file_list_comprehension(datafile)

    # lists not doing it for you? how about a dictionary?
    data_dict = import_file_dictionary(datafile)

    # 'data' now contains all the information from the CSV file as a list of lists
    # now lets do something fun with the data:
    new_data = scrambled_eggs(data)

    # now we can output the data to a new file:
    outfile = os.path.join(FILE_IS_HERE, OUTFILE_NAME)
    # make sure you tell the function where the outfile file should go
    output_data(new_data, outfile)
    # all done!

# and of course, call the main function like so:
main()
# or, if you feel daring, you can also use this:
if __name__ == "__main__":
    main()
```

And that's all there is to it for CSV file import and export as far as basic needs go.

## Excel data

Oh Excel, trying to make our lives easier when it comes to analysing data... It's a great program to use as a support for datasets; you can move data around easily and even do some simple statistics. However, it can easily mess up data fields and programming analysis can be complicated and ineffective compared to R and Python. Yet, a lot of information is stored in Excel workbooks, so in order to import and export this in and out of Python for analysis I will guide you through the basics:

```python
import xlrd
from xlwt import Workbook

# import Excel data
data = {sheet: zip(*[[sheet.cell(row, col).value.encode('utf-8') for col in range(0, sheet.ncols)] for row in range(0, sheet.nrows)]) for sheet in xlrd.open_workbook('folder/infile.xlsx').sheet_names()}

# export Excel data
wb = Workbook(encoding='utf-8')
for key, value in data.items():
    sheet = wb.add_sheet(key)
    for i, line in enumerate(value):
        for x, col in enumerate(line):
            sheet.write(i, x, col)
wb.save('folder/outfile.xls')
```

Short and ~~simple~~ complicated! Now lets look at a more in-depth breakdown of what's going on using a simple program:

```python
# This is a guide for Excel data import and export with some bonus manipulation ;)
# we will be mainly using the xlrd (Excel read) and xlwt (Excel write) libraries
# what we will do here is import all the information from an Excel workbook into a dictionary

import xlrd, os
from xlwt import Workbook

# where is the import file and where to output it
INPUT = 'folder/infile.xlsx'
OUTPUT = 'folder/outfile.xls'

# import the Excel file
def import_file(datafile):
    # this checks if the file exists
    if not os.path.isfile(datafile):
        exit("\nGreat Scott! Where's the file?\n")
        # fan of Back to the Future?
    # here we open the Excel file
    workbook = xlrd.open_workbook(datafile)
    # now lets see how many sheets this file has
    print len(workbook.sheet_names())
    return workbook

def save_as_csv(data, filename):
    with open(filename, "w") as f:
        w = csv.writer(f, quoting=csv.QUOTE_ALL)
        for rownum in xrange(data.nrows):
            w.writerow(data.row_values(rownum))

# this will pull the data from the sheet and switch rows to columns in order to
# have the head of the column as the first element of the list
def pull_from_sheet(sheet):
    return = zip(*[[sheet.cell(row, col).value.encode('utf-8') for col in range(0, sheet.ncols)] for row in range(0, sheet.nrows)])

# this will all the data from the Excel workbook into a dictionary
def pull_all_data(excel):
    data = {}
    for sheet_name in excel.sheet_names():
        # store the data from the sheet in 'sheet'
        sheet = excel.sheet_by_name(sheet_name)
        # we already have a function that pulls the data (just above)
        data[sheet] = pull_from_sheet(sheet)
    return data

# just having some fun here
def drink_beer(data):
    for sheet in data:
        fun = [line.append('gulp!') for line in data[sheet]]
    return data

# and now to output back to Excel
def output_as_xls(final, outfile):
    # we open the output workbook
    wb = Workbook(encoding='utf-8')
    # we iterate through the dictionary
    for key, value in final.items():
        sheet = wb.add_sheet(key)
        # and add the data to the rows and columns
        for i, line in enumerate(value):
            for x, col in enumerate(line):
                sheet.write(i, x, col)
    # finally we save the excel workbook and we're done! 
    wb.save(outfile)

def main():
    # this will tell python where the file is
    infile = INFILE
    outfile = OUTFILE

    # we first open the workbook as the variable 'excel'
    excel = import_file(infile)

    # you can then either take the data you want from a particular sheet
    sheet_name = 'sheet1'
    data = pull_from_sheet(excel.sheet_by_name('sheet1'))
    # or pull all the data into a dictionary with the sheet name as the key
    # and all the data in the sheet as a list in the value for that key
    all_data = pull_all_data(excel)

    # now we can have some fun with the data
    final = drink_beer(all_data)
    
    # and now that we're done we can either output a sheet as CSV:
    save_as_csv(data, 'folder/test.csv')
    # or save as xls (an old excel format)
    output_as_xls(final, outfile)
    # finished!

    # unfortunately if you want to save it as the newest Excel format you 
    # would need to use the openpyxl library
        
if __name__ == "__main__":
    main()

```

And now you know the basics of Excel document import and output using python! 

## JSON data

JSON, or JavaScript Object Notation, is great for modelling and transmitting data as it is language independent (just like XML). The data is organised into fields and values and it allows for nested objects and nested arrays. Essentially the "objects" are like dictionaries in Python. If you're curious to learn more, [here](http://www.w2schools.com/json/)'s a good free tutorial I followed.

This data is usually obtained from a website or API (Application Programming Interface), so this is where things start to get pretty cool! I will show you the basics of the requests library before we get further in-depth later on. In this walkthrough we will be obtaining the weather information from Yahoo! Weather. Before we start, know that in order to use this script for your country's weather you need to visit [this site](http://woeid.rosselliot.co.nz/) and obtain the WOEID code. Now, let's get down to it:

```python
# In this walkthrough we will be obtaining weather information from Yahoo! Weather
# make sure you have your WOEID code at the ready

import json, requests

# insert your WOEID here
WOEID = ''

# here we will store the url segments which I broke down for better understanding
def weather_url():
    yahoo_weather = 'https://query.yahooapis.com/v1/public/yql?q=select%20*%20from%20weather.forecast'
    woeid = '%20where%20woeid%3D' + WOEID
    celcius = '%20and%20u%3D%27C%27'
    format_json = '&format=json'
    return yahoo_weather + woeid + celcius + format_json

# you can print the dictionary with this function
def pretty_print(data, indent=4):
    # this function will make the printed output more readable
    if type(data) == dict:
        print json.dumps(data, indent=indent, sort_keys=True)
    else:
        print data

# this will store some of the information from the json object into a dictionary
def organise_info(response):
    today_info = {}
    # now we can store the data in today_info
    today_info['current_temp'] = response['query']['results']['channel']['item']['condition']['temp']
    today_info['current_low'] = response['query']['results']['channel']['item']['forecast'][0]['low']
    today_info['current_high'] = response['query']['results']['channel']['item']['forecast'][0]['high']
    today_info['conditions'] = response['query']['results']['channel']['item']['condition']['text']
    today_info['forecast_conditions'] = response['query']['results']['channel']['item']['forecast'][0]['text']
    return today_info

# again, the main function will call all the other functions
def main():
    try:
        # here we will use requests to pull the JSON data from Y! Weather
        weather_info = requests.get(weather_url())
        # .ok will check if the data was downloaded 
        if weather_info.ok:
            response = weather.info.json()
            # now we have all of the information as a python dictionary!
            # if we want we could print it using:
            pretty_print(response)
            # but lets organise it neatly and extract what we want
            info = organise_info(response)
            # now we have the temperature and weather condition information inside a smaller dictionary
    except:
        exit('Error while pulling weather info!\n')

if __name__ == '__main__':
    main()

```

## HTML data

Here comes the fun part. Now, before we dive into some serious **web-scraping**, you really need to have some basic knowledge of HTML. Python has many different libraries you can use to scrape the web, but my favourite by far are **requests**, **lxml** and **BeautifulSoup**. The latter I don't use as much as I used to since I require a much faster HTML parser for my web-scrapers, but I shall cover it anyway because it has really helped me in the past. 

In this walkthrough I will show you how to scrape Google News and pull the top ten World News titles and how to extract all the outward links from a website. In this instance I will be using OOP (Object Oriented Programming), which means I will be using Python classes. So without further ado let's get down to it:

```python
# In this section we will create the Scraper class with
# a method (a class function) that scrapes Google News for the top news
# and a method to scrape all the external links in a website

import requests
from bs4 import BeautifulSoup
from lxml import etree

# create the Scraper class
class Scraper:
    # initialise the class
    def __init__(self):
        # in this case we are using a requests Session so that we can update the headers
        self.web = requests.Session()
        # the user agent will be used to tell the website that the scraper is a browser
        # otherwise Google will not let you scrape HTML information from their sites
        self.user_agent = ("Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 "
                           "(KHTML, like Gecko) Chrome/48.0.2564.103 Safari/537.36")
        self.update_header()
        self.world_url = 'http://news.google.com/news?output=rss'

    # this will update the headers to tell the website the scraper is a browser and not a bot
    def update_header(self):
        if self.web.headers['User-Agent']:
            self.web.headers.update({'User-Agent': self.user_agent})

    # this method will return the top ten news titles
    def get_world_news(self):
        # response variable is now a 'Response' object
        response = self.web.get(self.world_url)
        if response.ok:
            return self.news_titles(response)
        else:
            print '%s Error' %(response.status_code)

    # in this method we will use BeautifulSoup
    # bear in mind it is slower, nearly 10 times slower than lxml
    def news_titles(self, response):
        # reponse.content contains all the HTML code from the website
        news = BeautifulSoup(response.content, 'lxml')
        # BeautifulSoup has really ease to use '.find()'/'.find_all()' functions
        news_titles_raw = news.find_all('title')
        news_titles = [news.text.encode('utf-8') for news in news_titles_raw if 'Google' not in news.text]
        return self.remove_tail(news_titles)

    # the news titles come with a 'tail' of sorts, let's remove it
    def remove_tail(self, news_titles):
        news_titles_done = []
        for news in news_titles:
            head, sep, tail = news.partition(' - ')
            news_titles_done.append(head)
        return news_titles_done

    # now let's make the scraper extract all the external links from a given website
    # in this method lxml will be used just to make it that much faster
    def get_external_links(self, url):
        # a few things can go wrong so we will be trying to catch the exceptinos
        try:
            # again we use requests to get the website response
            website = self.web.get(url)
            if website.ok:
                # and now we use lxml to parse the information
                # this library parses the HTML into an element tree
                tree = etree.HTML(website.content)
                # all you need to do is get the hang of the find and findall methods
                all_a = tree.findall('.//a')
                if all_a:
                    hrefs = []
                    for a in all_a:
                        # this is a prime example of catching the exception
                        try:
                            if '://' in a.attrib['href'] and 'mailto:' not in a.attrib['href']:
                                hrefs.append(a.attrib['href'])
                        except:
                            continue
                    # in order to remove duplicates we will turn the list into a set()
                    print 'Found %s links' %(len(set(hrefs)))
                    return set(hrefs)
                else:
                    return ['NA']
            else:
                print '%s Error while scraping!' %(website.status_code)
        except:
            exit('Error while scraping for external links!')

```

Now that we have our Scraper class set up we can create a new script to call it. In this case I'm assuming you saved the code under scraper.py, and created a new file (e.g. run.py).

```python
# First we import the Scraper class
from scraper import Scraper

# initialise it
scraper = Scraper()

# now we can call the top news method
news = scraper.get_world_news()
# now we can print the news
for n in news:
    print n

# or scrape the external links for any website we want
links = scraper.get_external_links('https://en.wikipedia.org/wiki/Web_scraping')
# and to view them
for link in links:
    print link
# all done!
```

Now that you know some of the basics you can create your own super duper web scraper!

But before you go on to create your own Google alternative (yes, Google is a web scraper) be warned! Many websites don't like scrapers crawling through them and [set traps](https://www.quora.com/What-are-some-Web-crawler-tips-to-avoid-crawler-traps) that could block your IP address. Website may include a [robots.txt](http://www.robotstxt.org/robotstxt.html) file that let you know what they permit you to scrape.

And there you have it, the basics of Data Analysis with Python. Granted I didn't actually cover Data Cleaning, which will be included in my next article! 

Gracias <i class="fa fa-hand-peace-o"></i>!
