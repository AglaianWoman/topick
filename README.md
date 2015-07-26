# Topick

One trick pony NLP library for extracting keywords from HTML documents. It uses `htmlparser2` for HTML parsing, `nlp_compromise` for NLP and `text-miner` for text cleaning and removing stop words.

## Installation

`npm install topick`

Topick is intended primarily for server-side use because of cross-domain issues, although I'm working on making the codebase isomorphic so that browser use is possible as well (with an appropriate module loader such as webpack).

## Usage

### `getKeywords(uri[, opts])`

Example:

`getKeywords` takes either a valid `HTTP` URI, or a HTML string, and returns a promise that can be resolved appropriately:

```js
import Topick from 'topick'

Topick.getKeywords('http://example.com/', {
  htmlTags: ['p'],
  ngram: {
    min_count: 4,
    max_size: 2
  }
}).then((keywords) => {
  console.log(keywords); // ['most relevant keyword', 'very relevant keyword', 'somewhat relevant keyword']
  // do something with your keywords
})
```

The keywords are arranged in order of descending relevance.

#### Options

`getKeywords` accepts a second optional options object.

Currently available options are:

##### `htmlTags`

Default: `['p', 'b', 'em', 'title']`

An array of HTML tags that should be parsed.

#### `method`

Default: `combined`

Topick includes three methods for generating keywords. 

`ngram`

Generates n-grams from the content string and ranks them in terms of frequency.

`namedentities`

Uses `nlp_compromise`'s `spot` method to identify [named entities](https://en.wikipedia.org/wiki/Named-entity_recognition) before generating n-grams based on these named entities.

`combined`

Runs both `ngram` and `namedentities` methods, then combines their ranking.

##### `useDefaultStopWords`

Default: `true`

If true, uses Topick's internal stop words dictionary to remove stop words. If false, no stop word removal will be performed unless you supply your own stop word array (see `customStopWords`).

Topick's dictionary is a set union of all six English collections found [here](https://code.google.com/p/stop-words/).

##### `customStopWords`

Default: `[]`

An array of strings that should be used as stop words. This has no bearing on `useDefaultStopWords`, although it should be populated with your own stop word array if `useDefaultStopWords` is set to `false`, else Topick will generate a lot of irrelevant keywords.

##### `maxNumberOfKeywords`

Default: 10

Maximum number of keywords to generate.

##### `ngram`

Default:

```
{ min_count: 3, max_size: 1 }
```

Defines options for n-gram generation. 

`min_count` is the minimum number of times a particular n-gram should appear in the document before being considered. There should be no need to change this number.

`max_size` is the maximum size of n-grams that should be generated (defaults to generating unigrams).

### `getKeywordsSync(uri[, opts])`

Works similarly to `getKeywords(uri[, opts])`, except it is synchronous and returns an array directly:

```js
Topick.getKeywordsSync('http://example.com').forEach((keyword) => {
  console.log(`This is a keyword: ${keyword}`);  
})
```

### `getDomain(uri)`

Example:

```js
Topick.getDomain('http://example.com')
```

Given `http://example.com`, returns `example`. Removes URI scheme, port number, and TLD.

## Contributing

Contributions are welcome!

Topick is written in ES6 wherever possible. The development workflow is centered primarily around webpack, so be sure to check out `webpack.config.js`.

## TODO

- Progressive min-count fallback