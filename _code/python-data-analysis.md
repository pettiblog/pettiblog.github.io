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

## CSV data

## Excel data

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

## HTML data

## Cleaning up
