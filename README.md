# News API

- [Request protocol](#request-protocol)
	- [Tips for searching](#tips-for-searching)
	- [Request examples](#request-examples)
- [Response protocol](#response-protocol)


## Request protocol

The above JSON protocol shows the parameters available to search for news in knewin.io API.

```
{
	"key" : STRING ,
	"query" : STRING ,
	"offset" : INTEGER ,
  	"defaultOperator": STRING [OR, AND] ,
	"fields" : STRING [
			"url", "id", "title", "subtitle", "author", "content", 
			"source_id", "source", "crawled_date", "published_date", 
			"lang", "images", "hat", "category", "locality", "page"
		] ,
	"gmt" : STRING (ex.: "-3", "-0200", "+01:00", "0600") ,
	"filter" : {
		"sourceId" : INTEGER ,
		"language" : [
			"pt", "en", "es", "fr", "de", "bg", "cs", "el", "et", 
			"fi", "hu", "it", "ja", "ko", "lt", "nl", "pl", "ro", 
			"ru", "sk", "sl", "so", "sv", "uk"
		] ,
		"sincePublished" : DATE (yyyy-MM-ddTHH:mm:ss) ,
		"untilPublished" : DATE (yyyy-MM-ddTHH:mm:ss) ,
		"sinceCrawled" : DATE (yyyy-MM-ddTHH:mm:ss) ,
		"untilCrawled" : DATE (yyyy-MM-ddTHH:mm:ss)
	} ,
	"sort" : {
		"field": STRING ("published_date", "crawled_date", "frequency") ,
		"order": STRING ("asc", "desc")
	}
}
```

*where:*

Field | Required | Description
----- | -------- | -----------
`key` | yes | the key authorization
`query` | yes | a boolean query (max 1024 characters)
`defaultOperator` | no | the default boolean operator (OR or AND) - default OR
`offset` | no | a start position - default 0 (zero)
`gmt` | no |  the GMT to be used - default UTC
`fields` | no | a list of fields - default all fields
`filter` | no | to filter the result set (filter fields described in a table above)
`sort` | no | to sort the result set - default crawled date, descendent order - from newer to older (sort fields described in a table above)

Filter field | Required | Description
------------ | -------- | -----------
`sourceId` | no | a list of source identifications (max 100)
`language` | no | a list of languages
`sincePublished` | no | a since published date
`untilPublished` | no | a until published date
`sinceCrawled` | no | a since crawled date
`untilCrawled` | no | a until crawled date


Sort field | Required | Description
---------- | -------- | -----------
`field` | yes | the field to be used to sort the result set (possible values: `published_date`, `crawled_date`, `frequency`)
`order` | yes | the order to be sorted (possible values: `asc` or `desc`)


> *__IMPORTANT__: Date format is based on W3C pattern (eg., 1997-07-16T19:20:30)*

---

### Tips for searching

Query expressions/keywords are case insensitive, ie., searching for `Technology` or `technology` will bring the same amount of results.

All keywords are considered, even those that are called stopwords (i.e, `is`, `a`, `the`, etc). 

The boolean connectors should be written in upper case (i.e, AND, OR, NOT).

The default fields where searching is applied are: `title`, `subtitle`, `content`, `author`, `image_caption`, and `image_credit`.

Pagination should be done considering the `sinceCrawled` and `untilCrawled` fields available in the filter part of the query and `offset` field.

First request...
```
{
	"key" : YOUR-KEY ,
	"query" : "technology" ,
	"offset" : 0 , // start with 0 (zero)
	"filter" : {
		"sinceCrawled" : "2016-01-01T00:00:00" ,
		"untilCrawled" : "2016-01-01T23:59:59"
	}
}
```

Second request...
```
{
	"key" : YOUR-KEY ,
	"query" : "technology" ,
	"offset" : 10 , // change to multiple of 10 (each request brings 10 docs)
	"filter" : {
		"sinceCrawled" : "2016-01-01T00:00:00" ,
		"untilCrawled" : "2016-01-01T23:59:59"
	}
}
```
> *__IMPORTANT__: it's highly recommended to used `sinceCralwed` and `untilCrawled` filter fields, because new documents are added all the time so that it's possible to get the same set of documents in every new request.*


### Request examples

Search for news that contains the keyword `technology`:

```
{
	"key" : YOUR-KEY ,
	"query" : "technology" ,
	"offset" : 0
}
```

Search for news that contains the expression `"global warming"` sorted by the published date, from newer to older:

```
{
	"key" : YOUR-KEY ,
	"query" : "\"global warming\"" ,
	"offset" : 0 ,
	"sort" : {
		"field": "published_date" ,
		"order": "desc"
	}
}
```

Search for news that contains the expression `"global warming" AND "climate change"`, filtering for news only in English or Spanish, and sorted by the crawled date, from newer to older:

```
{
	"key" : YOUR-KEY ,
	"query" : "\"global warming\" AND \"climate change\"" ,
	"offset" : 0 ,
	"filter" : {
		["en" , "es"]
	}
	"sort" : {
		"field": "crawled_date" ,
		"order": "desc"
	}
}
```
---

## Response protocol

The response protocol, in JSON format, contains a list of news returned from a search in knewin.io API.

```
{
	"num_docs": INTEGER,
	"start":  INTEGER,
	"count": INTEGER,
	"hits": [	
		{
			"url": STRING ,
			"id": INTEGER ,
			"domain": STRING ,
			"title": STRING ,
			"subtitle": STRING ,
			"author": STRING ,
			"content": STRING ,
			"source_id": STRING ,
			"source": STRING ,
			"crawled_date": DATE (yyyy-MM-ddTHH:mm:ss) ,
			"published_date": DATE (yyyy-MM-ddTHH:mm:ss) ,
			"lang": STRING ,
			"category": STRING ,
			"hat": STRING ,
			"locality": STRING ,
			"page": STRING ,
			"source_locality": [
				{
					"country": STRING,
					"state": STRING, 
					"city": STRING
				}
			] ,
			"image_hits": [
				{
					"url": STRING,
					"caption": STRING,
					"credit": STRING
				}
			]
		}
	]
}
```

*where:*

Field | Description
----- | -----------
`num_docs` | the total amount of documents returned for a search
`start` | the start position of returned news in the result set
`count`| the amount of news returned in the response
`hits` | a list of news data (news fields described in a table above)


News field | Description
---------- | -----------
`url`| the news URL
`id` | the news identification
`domain` | the news URL's domain
`title` | the title
`subtitle` | the subtitle
`author` | the author
`content` | the content
`source_id` | the source identification
`source` | the source description
`crawled_date` | the crawled date
`published_date` | the published date
`lang` | the language
`category` | the news category
`hat` | the news hat
`locality` | the news locality
`page` | the page URL
`source_locality` | the source locality (source locality fields describe in a table above)
`image_hits` | the images (image fields described in a table above)


Source locality field | Description
--------------------- | -----------
`country` | the country name
`state` | the state name
`city` | the city name


Image field | Description
--------------------- | -----------
`url` | the image URL
`caption` | the image caption
`credit` | the image credit

