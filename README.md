# HomeBot
A tool for obtaining and analyzing data from the Swedish housing market.

## Install
The following should get you started on most systems.
```
$ pip3 install -r requirements.txt
```
Optionally also install extra requirements.
```
$ pip3 install -r requirements-extra.txt
```

## Obtaining data
Data is obtained using *spiders* with [Scrapy]. Run
```
$ scrapy --help
```
for general help with Scrapy. To fetch data you need to get a spider *crawling*.
```
$ scrapy crawl [options] <spider>
```
You can pass `-a url=<url>` to change the starting URL for your crawls. See
```
$ scrapy list
```
for a list of available spiders.

Also see [settings.py] for some options that could be useful!

### Examples
Fetch all residences for sale from [Hemnet] that cost more than 20,000,000 SEK.
#### Villa
```
scrapy crawl \
         -a url="https://www.hemnet.se/bostader?location_ids%5B%5D=17793&location_ids%5B%5D=898740&location_ids%5B%5D=473325&item_types%5B%5D=villa" \
         --output-format=csv \
         --output=sthlm_villa.csv \
         hemnet
```
#### Flat

#### Villa-sold
```
scrapy crawl \
         -a url="https://www.hemnet.se/salda/bostader?item_types%5B%5D=villa&location_ids%5B%5D=17793" \
         --output-format=json \
         --output=sthlm_villa_sold.csv \
         hemnet-sold
```
#### Flat-sold
Fetch all sold residences from Hemnet with at least 10 rooms to a JSON file.
```
$ scrapy crawl \
         -a url="https://www.hemnet.se/salda/bostader?rooms_min=10.0" \
         --output-format=json \
         --output=hemnet-sold.json \
         hemnet-sold
```
##
Debug by add shell 
```python
from scrapy.shell import inspect_response
inspect_response(response, self)
```
Then in interactive shell do the work
```python
```python
import re, json, flatten_json
s = "//script"
for script in response.xpath(s):
    text = script.xpath("text()").extract_first()
    try:
        if text:
            pat = r""".*dataLayer = \[([.\W\w]{0,})\]\;"""
            a = re.search(pat, text).group(1)
            break
    except AttributeError:
        pass
x= "{" + a.split("},{")[1]
attribs = json.loads(x).get("property")
item=flatten(attribs)
```

## Analyzing data
When you got some data, it's time to analyze!

### SQL
SQL is a nice language for relational databases. Even though we don't really
have any relational data, it's still useful for doing aggregation and filtering.

You can use [sqlitebiter] to convert e.g. a JSON file to a [SQLite] format. 
```
$ sqlitebiter file hemnet-sold.json --output-path=hemnet-sold.sql
$ sqlite3 hemnet-sold.sql
sqlite> SELECT AVG(soldprice) FROM hemnet_sold_json1;
6047405.62248996
```

## Inspiration
This project was inspired by Lauri Vanhala's article
[Figuring out the best place to live in Helsinki].


[Hemnet]: https://www.hemnet.se/
[Scrapy]: https://scrapy.org/
[settings.py]: homebot/settings.py
[sqlitebiter]: https://sqlitebiter.readthedocs.io/
[SQLite]: https://www.sqlite.org/
[Figuring out the best place to live in Helsinki]: http://www.wanhala.net/post/156484186523/figuring-out-the-best-place-to-live-in-helsinki
