# Metal-Archives Query (maq)

A command line tool to query [The Metal-Archives](http://www.metal-archives.com) and dump reviews and band information (including their logos) in CSV format for offline use.

[The Metal-Archives](http://www.metal-archives.com) does not have an official developer API to access its database directly. However, there is an internal JSON API that the site's Javascript code uses to display results which can be leveraged to achieve essentially the samething.

## Getting Started

These instructions will get maq up and running on your local machine for development and testing purposes. 

### Prerequisites

```
pip3 install -r requirements.txt
```

### Installing

```
sudo cp maq /usr/local/bin
```

## Usage

```
# maq --help
Usage: maq [OPTIONS] COMMAND [ARGS]...

  Metal-Archives Query

Options:
  -c, --csvfile FILENAME          CSV format
  -d, --delimiter TEXT            CSV delimeter
  --header / --noheader           Write header
  -q, --quoting [all|minimal|nonnumeric|none]
                                  CSV quoting behavior
  -r, --retries INTEGER           Number of request retries
  -t, --throttle FLOAT            Throttle requests (seconds)
  --quiet                         Quite mode
  --help                          Show this message and exit.

Commands:
  bands    Query bands
  reviews  Query reviews
```

To query for band information:

```
# maq bands --help
Usage: maq bands [OPTIONS]

  Query bands

Options:
  -b, --by [alpha|country|genre]  Query by
  --batch INTEGER                 Batch number to start from
  -c, --country [AF|AX|AL|DZ|AD|AO|AR|AM|AW|AU|AT|AZ|BR|BD|BB|BE|BZ|BO|BA|BW|BN|BG|KH|CA|CL|CN|CO|CR|HR|CU|CW|CY|CZ|DK|DO|EC|EG|SV|EE|ET|FO|FI|FR|PF|GE|DE|GI|GR|GL|GU|GT|GG|GY|HN|HK|HU|IS|IN|ID|XX|IR|IQ|IE|IM|IL|IT|JM|JP|JE|JO|KZ|KE|KR|KW|KG|LA|LV|LB|LY|LI|LT|LU|MK|MG|MY|MV|MT|MU|MX|MD|MC|MN|ME|MA|MZ|MM|NA|NP|NL|NC|NZ|NI|NO|OM|PK|PA|PY|PE|PH|PL|PT|PR|QA|RE|RO|RU|SM|SA|RS|SG|SK|SI|ZA|ES|LK|SR|SJ|SE|CH|SY|TW|TJ|TH|TT|TN|TR|TM|UG|UA|AE|GB|US|ZZ|UY|UZ|VE|VN]
                                  Band country code
  -g, --genre [black|death|doom|electronic|avantgarde|folk|gothic|grind|groove|heavy|metalcore|orchestral|power|prog|speed|thrash]
                                  Band genre
  -l, --letter TEXT               First letter of band name
  --logos                         Fetch band logos
  --logosdir PATH                 Path to save logos
  --listcountries                 List country code -> country
  -n, --number INTEGER            Number of records to return
  --help                          Show this message and exit.
```

To query for review information:

```
# maq reviews --help
Usage: maq reviews [OPTIONS]

  Query reviews

Options:
  -b, --by [alpha|date|rating]    Query by
  --batch INTEGER                 Batch number to start from
  -d, --date TEXT                 Year-Date [YYYY-MM]
  -l, --letter TEXT               First letter of band name
  -n, --number INTEGER            Number of records to return
  -r, --rating [0|5|10|15|20|25|30|35|40|45|50|55|60|65|70|75|80|85|90|95|100]
                                  Review rating
  --help                          Show this message and exit.
```

## Usage Examples

To query bands starting with the letter 'M' and return the first 10 results:

```
maq bands --by alpha --letter M -n10
```

To query bands originating from the United States and return the first 10 results:

```
maq bands --by country --country US -n10
```

To list all country codes:

```
maq bands --listcountries
```

To query reviews of artists starting with the letter 'M' and return the first 10 results:

```
maq reviews --by alpha --letter M -n10
```

To query reviews in the range of 45-50% and return the first 10 results of batch 5:

```
maq reviews --by rating --rating 50 -n10 --batch 5
```

To dump all reviews into a CSV file:

```
maq --csvfile reviews.csv reviews
```

To dump all bands into a CSV file and their corresponding logos (one image per band id):

```
maq --csvfile bands.csv bands --logos
```

A progress bar is displayed as maq fetches query results from the website. Use the **--quiet** option to enable silent operation.

Please note that records are written out in batches. Currently the batchsize is static since MA does not allow you to change it programmatically.

## CSV Format

```
Bands:
  "bid"       - Band ID
  "name"      - Band name
  "origin"    - Country of origin
  "location"  - Location
  "status"    - Status (active, split-up, etc.)
  "formedin"  - Formed in year
  "genre"     - Genre list separated by '|' delimiter
  "themes"    - Lyrical themes list separated by '|' delimiter
  "label"     - Current label
  
Reviews:
All review entries start with a band entry for that review (all the columns outlined above) in addition to:
  "uid"       - User ID
  "aid"       - Album ID
  "album"     - Album name
  "author"    - Review author
  "review"    - Review content (UTF-8)
  "rating"    - Rating (0-100%)
  "date"      - Review date
```

## Logos

If you specify '--logos' for a band query that band's logo image file will be saved as 'bid.\[jpg|gif|png\]' (e.g. 1.jpg) so you can correlate the image file with the band using their band ID.

## Troubleshooting

maq makes heavy use of the [click](http://click.palletsprojects.com/en/7.x/) which requires that your shell's locale be set to something sensible. If you execute maq and get the following error:

```
RuntimeError: Click will abort further execution because Python 3 was configured to use ASCII as encoding for the environment.
```

Please set your shell's locale as follows and then re-run maq:

```
export LC_ALL=C.UTF-8
export LANG=C.UTF-8
```

maq writes entries in batches (batch size is fixed at 200 for reviews and 500 for bands). If for whatever reason a batch fails to process due to network timeouts or other unexpected interruptions, you can always start over at the batch number using the '--batch' option above.

## Authors

* **Alexander Sack** - *Initial work*

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* HellBlazer (MA Webmaster) for giving me permission to scrape the archives.
* [Peter Steele (RIP)](https://en.wikipedia.org/wiki/Peter_Steele) and [his merry band of green men](https://www.metal-archives.com/bands/Type_O_Negative/802) for making it enjoyable.
