# Miscellenous One-Liners & Code Snippets

## Shell

### List Intentionally Installed Packages 
```shell
(zcat $(ls -tr /var/log/apt/history.log*.gz); cat /var/log/apt/history.log) 2>/dev/null |   egrep '^(Start-Date:|Commandline:)' |   grep -v aptdaemon |   egrep '^Commandline:' | awk '{print $NF}' | grep -E '^[^-].*' | sort | uniq
```

```shell
pip3 list --user
```

### Bash Unofficial Strict Mode
```shell
set -euo pipefail
IFS=$'\n\t'
```

## Python Snippets



### split list according to a predicate

```python
from itertools import groupby
def split_pred(a,f):
    return [list(g) for k,g in groupby(a, lambda x: f(x)) if k]

# str.split() equivalent
split = lambda a,s: split_pred(a, lambda x: x!=s)
```

### flatten nested list
```python
flatten = lambda n: [k for a in n for k in a]
#or
flatten = lambda v: itertools.chain.from_iterable(v)
```

### deep flatten

```python
def flatten(lst):
	return sum( ([x] if not isinstance(x, list) else flatten(x)
		     for x in lst), [] )
```

### iterate over batches of a list
```python
def batches(iterable, n=1):
    l = len(iterable)
    for ndx in range(0,l,n):
        yield iterable[ndx:min(ndx + n, l)]
```

### dict2obj
```python
from munch import Munch
obj = Munch(my_dict)
```
### class convertible to dict
```python
from typing import NamedTuple
class MyClass(NamedTuple):
    a: int
    b: str
```

### normalize accent characters
```python
import unicodedata
def asciify(s):
    return unicodedata.normalize('NFKD', s).encode('ascii', 'ignore').decode('utf-8')
```

### self-explenatory
```python
def underscore2camelcase(s):
    return s.replace('_',' ').title().replace(' ','')

def camelcase2underscore(s):
    return re.sub(r'[ \s]+','_', '_'.join(camelcase_split(s))).lower()

import re
def camelcase_split(identifier):
    matches = re.finditer('.+?(?:(?<=[a-z])(?=[A-Z])|(?<=[A-Z])(?=[A-Z][a-z])|$)', identifier)
    return [m.group(0) for m in matches]
```
### URL & Scraping
```python
import mimetypes, urllib2

def is_url_image(url): 
    mimetype,encoding = mimetypes.guess_type(url)
    return (mimetype and mimetype.startswith('image'))

def check_url(url):
    """Returns True if the url returns a response code between 200-300,
       otherwise return False.
    """
    try:
        headers = {
            "Range": "bytes=0-10",
            "User-Agent": "MyTestAgent",
            "Accept": "*/*"
        }

        req = urllib2.Request(url, headers=headers)
        response = urllib2.urlopen(req)
        return response.code in range(200, 209)
    except Exception:
        return False
   
mail_pattern = r"([a-zA-Z0-9_.+-]+(@|\[at\])[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+)"

import os, requests, lxml.html
def cached_page(url):
  if not url:
    return None
  try:
    if not os.path.isdir('cache'):
      os.mkdir('cache')
    cache = os.path.join("cache/", quote(url, safe=''))
  except Exception:# as ex:
    pass#print(ex)
  try:
    with open(cache,'r') as f:
      source = f.read()
      page   = lxml.html.fromstring(source)
  except Exception:
    with open(cache, 'w') as f:
      try:
        response = requests.get(url)
        #print("Request: "+url)
        source   = response.content
        if source:
          try:
            f.write(source.decode('utf-8'))
          except Exception:
            pass
        page = lxml.html.fromstring(source)
      except Exception:
        page = None
  return page
```

## C++

### String literal as template argument

```{c++}
template<size_t N>
struct string_literal {
  constexpr string_literal (const char (&str)[N]) {
    std::copy_n(str, N, value);
  }
  operator auto() const { return value; }
  char value[N];
};
```
