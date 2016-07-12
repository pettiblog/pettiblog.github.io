---
title: "Data Analysis with Python"
excerpt: "Analysing different sets of data and data mining with Python"
header:
  teaser: python.png
sidebar:
  - title: "Data Analysis with Python"
    image: python.png
    text: "Analysing different sets of data and data mining with Python"
comments: true
---

{% include toc title="Overview" icon="file-text" %}

I recently took a [nanodegree with Udacity](https://www.udacity.com/course/data-analyst-nanodegree--nd002) to "become a Data Analyst", as they say. It was great though, I really got to work on my Python programming skills and use it to analyse different types of data. Sadly most of the assignments had me write very specific scripts that cannot be used for anything else. So I thought I would write my own (more promiscuous) Python scripts and showcase them on this page. Feel free to use them, modify them or hang them in a picture frame on your wall. Jokes aside though, leave a comment or [email](mailto:nicolaspettican@gmail.com) me if you would like to collaborate on a little project I mention on this page or to modify any of the scripts, as they are still not on GitHub (but will be soon).

## CSV data

First off, of course, is the CSV format. The CSV format is the most common import and export format for datasets, spreadsheets and other databases. It basically contains rows, each with columns that are usually separated by commas. It's fairly straitforward to import and export these files in Python even without the csv module:

```python

import os

DATADIR = raw_input("\nCSV file directory: ")
DATAFILE = raw_input("\nCSV file name: ")

def import_file(datafile):
    data = []
    with open(datafile, "r") as f:
        header = f.readline().split(",")
        for line in f:
            fields = line.split(",")
            entry = {}
            for i, value in enumerate(fields):
                entry[header[i].strip()] = value.strip()

            data.append(entry)

        return data

def main():
    datafile = os.path.join(DATADIR, DATAFILE)
    data = import_file(datafile)

# "data" now contains the information from the csv

```

And things get even easier if you use the csv module!

```python

import csv
import os

DATADIR = raw_input("\nCSV file directory: ")
DATAFILE = raw_input("\nCSV file name: ")

def import_file(datafile):
    data = []
    with open(datafile, "rb") as f:
	reader = csv.reader(f, delimiter="\t")
        header = f.next().split(",")
        for row in f:
            line = row.split(",")
            data.append(entry)

        return data

def main():
    datafile = os.path.join(DATADIR, DATAFILE)
    data = import_file(datafile)

# "data" now contains the information from the csv

if __name__ == "__main__":
    main()

```

## Excel data

Oh Excel, trying to make our lives easier when it comes to analysing data... Well, it is quite a good program to use as a support for datasets, you can move data around easily and even do some simple statistics. However, it can easily mess up data fields and programming analysis can be complicated and ineffective compared to R, for example. Either way a lot of information is stored in these sheets, and if you want to take that data and analyse it as a CSV, what do you do? You use my Python script of course! For now it can convert your Excel sheet into a CSV. Soon enough however, it will be able to do much more, by using R from within. Interested in collaborating? Drop me an [email <i class="fa fa-send"></i>](mailto:nicolaspettican@gmail.com) !

```python

#!/usr/bin/env python

import xlrd
import os
from os.path import join, dirname, abspath, isfile
import csv
import sys

# Optional to include the directory
# DATADIR = raw_input("\nXLS file directory: ")
# DATAFILE = raw_input("\nXLS file name: ")

datafile = raw_input("\nImport XLS file: ")
outfile = raw_input("\nOutput CSV name: ")

# import the Excel file, of course
def import_file(datafile):
    if not isfile(datafile):
        print "\nGreat Scott! Where's the file?\n"
    workbook = xlrd.open_workbook(datafile)
    if len(workbook.sheet_names()) > 1:
        print "There are %s sheets: " % len(workbook.sheet_names())
        i = 1
        for n in workbook.sheet_names():
            print i,
            print n
            i += 1
        sh = which_sheet()
        sheet = workbook.sheet_by_index(sh)
    else:
        sheet = workbook.sheet_by_index(0)
    return sheet

# give the user the chance to choose the sheet 
def which_sheet():
    pynum = input("\nChoose sheet number: ")
    num = pynum - 1
    return num

# show the user the data to verify it worked
def show_data(data):
    print "\nThis is what the header looks like: \n"
    header = [data.cell(0, col_index).value for col_index in xrange(data.ncols)]
    print header
    return header

# save the file as CSV
def save_file(data, filename):
    with open(filename, "w") as f:
        w = csv.writer(f, quoting=csv.QUOTE_ALL)
        for rownum in xrange(data.nrows):
            w.writerow(data.row_values(rownum))
        
    print "\nSaved as %s\n" % filename

# yes/no question to save the file as CSV
# user might want to analyse the file more
def save(default="yes"):
    question = "\nSave as CSV now?\n"
    valid = {"yes": True, "y": True, "ye": True,
            "no": False, "n": False}
    if default is None:
        prompt = " [y/n] "
    elif default == "yes":
        prompt = " [Y/n] "
    elif default == "no":
        prompt = " [y/N] "
    else:
        raise ValueError("Invalid answer: '%s'" % default)
    
    while True:
        sys.stdout.write(question + prompt)
        choice = raw_input().lower()
        if default is not None and choice == '':
            return valid[default]
        elif choice in valid:
            return valid[choice]
        else:
            sys.stdout.write("Please respond with 'yes' or 'no' "
                             "(or 'y' or 'n').\n")

def main():
    # optional to include directory
    #datafile = os.path.join(DATADIR, DATAFILE)
    data = import_file(datafile)
    header = show_data(data)
    if save():
        save_file(data, outfile)
    
    # bellow this I am thinking of continuing it
    # to analyse the file even more
    # maybe include the R module for some stats
        
if __name__ == "__main__":
    main()

```

It's quite unfinished, but for now at least it can convert Excel sheets to CSV files. To be honest those functions are a small part of the bulk of the code. Specifically just the import_file() and save_file() functions would do the trick, the rest is just for some better user-interface. Perhaps I should invest some time into trying to compact the code. What do you think? Is it working for you? Let me know in the comments.

## JSON data

JSON, or JavaScript Object Notation, is great for modelling and transmitting data as it is language independent just like XML. The data is organised into fields and values and it allows for nested objects and nested arrays. Essentially the "objects" are kind of like dictionaries in Python. If you're curious to learn more, [here](http://www.w2schools.com/json/)'s a good free tutorial I followed.

When using Python, extracting JSON data is not too complicated, however you may need an API token in order to obtain the data you want. So just to keep things simple here is a script I wrote during my Udacity nanodegree. Be warned though, if you want to give it a go be aware that HTTP errors may happen, you may be lucky and have none, or be unlucky like me and have to run the script over 10 times to get it to pull all the data 😂.

```python

#!/usr/bin/env python

import json
import requests

BASE_URL = "http://musicbrainz.org/ws/2/"
ARTIST_URL = BASE_URL + "artist/"

query_type = {  "simple": {},
                "atr": {"inc": "aliases+tags+ratings"},
                "aliases": {"inc": "aliases"},
                "releases": {"inc": "releases"}}

def which_artist():
    # user can choose what artist to search
    id = raw_input('\nWhich artist are you interested in? ')
    return id

def choose():
    # if there are more artists with the query name
    elige = input('\nWhich one? Enter integer: ')
    return elige

def query_site(url, params, uid="", fmt="json"):
    # this is the main function for making queries to the musicbrainz API
    # the query returns a JSON document
    params["fmt"] = fmt
    r = requests.get(url + uid, params=params)
    print "requesting", r.url

    if r.status_code == requests.codes.ok:
        return r.json()
    else:
        r.raise_for_status()

def query_by_name(url, params, name):
    # this will add the artist name to the parameters
    params["query"] = "artist:" + name
    return query_site(url, params)


def pretty_print(data, indent=4):
    # this function will make the output more readable
    if type(data) == dict:
        print json.dumps(data, indent=indent, sort_keys=True)
    else:
        print data


def main():
    # the main function, this calls on all the previous ones
    try:
        id = which_artist()
        results = query_by_name(ARTIST_URL, query_type["simple"], id)
        if len(results["artists"]) > 1:
	# not the best code but it works!
            length = len(results["artists"])
            print '\nThere are %s with this name:\n' % length
            j = 0
            for i in results["artists"]:
                print j,
                try:
                    print '%s' % results["artists"][j]["name"],
                    print '%s' % results["artists"][j]["disambiguation"]
                except:
                    pretty_print(results["artists"][j]["name"]),
                j += 1
                if j > length:
                    break
            chosenone = choose()
	    # the Chosen One
        #pretty_print(results)
        
        artist_id = results["artists"][chosenone]["id"]
        print "\nARTIST: %s\n" % results["artists"][chosenone]["name"]
        pretty_print(results["artists"][chosenone])

        artist_data = query_site(ARTIST_URL, query_type["releases"], artist_id)
        releases = artist_data["releases"]
        release_titles = [r["title"] for r in releases]
        
        print "\nALL TITLES:\n"
        for t in release_titles:
            print t
    except ValueError:
        print "Decoding JSON has failed..."

if __name__ == '__main__':
    main()

```

## XML data

Comming soon <i class="fa fa-rocket"></i>

## HTML data

Comming soon <i class="fa fa-list-alt"></i>

## Cleaning up

Comming soon <i class="fa fa-hand-spock-o"></i>


