# General

searx is a [metasearch-engine](https://en.wikipedia.org/wiki/Metasearch_engine), so it is using different search engines to provide better results. 

Because there is no general search-api which can be used for every search-engine, there must be build an adapter between searx and the external search-engine. This adapters are stored in the folder [_searx/engines_](https://github.com/asciimoo/searx/tree/master/searx/engines), and this site is build to make an general documentation about this engines

# returning results

Searx has the possiblity to return results in different media-types. Currently the following media-types are supported:

* default
* images
* videos
* torrent

to set another media-type as default, you must set the parameter ```template``` to the required type.

### default

| result-parameter | information  |
| ---------------- | ------------ |
| url              | string, which is representing the url of the result |
| title            | string, which is representing the title of the result |
| content          | string, which is giving a general result-text |
| publishedDate    | [datetime.datetime](https://docs.python.org/2/library/datetime.html#datetime-objects), represent when the result is published  |

### images

to use this template, the parameter 

| result-parameter | information  |
| ---------------- | ------------ |
| template         | is set to ```images.html``` |
| url              | string, which is representing the url to the result site |
| title            | string, which is representing the title of the result _(not implemented yet)_ |
| content          | _(not implemented yet)_ |
| publishedDate    | [datetime.datetime](https://docs.python.org/2/library/datetime.html#datetime-objects), represent when the result is published _(not implemented yet)_ |
| img_src          | string, which is representing the url to the result image |
| thumbnail        | string, which is representing the url to a small-preview image _(not implemented yet)_ |

### videos

| result-parameter | information  |
| ---------------- | ------------ |
| template         | is set to ```videos.html``` |
| url              | string, which is representing the url of the result |
| title            | string, which is representing the title of the result |
| content          | _(not implemented yet)_ |
| publishedDate    | [datetime.datetime](https://docs.python.org/2/library/datetime.html#datetime-objects), represent when the result is published |
| thumbnail        |  string, which is representing the url to a small-preview image |

### torrent

| result-parameter | information  |
| ---------------- | ------------ |
| template         | is set to ```torrent.html``` |
| url              | string, which is representing the url of the result |
| title            | string, which is representing the title of the result |
| content          | string, which is giving a general result-text |
| publishedDate    | [datetime.datetime](https://docs.python.org/2/library/datetime.html#datetime-objects), represent when the result is published _(not implemented yet)_ |
| seed             | int, number of seeder |
| leech            | int, number of leecher |
| magnetlink       | string, which is the [magnetlink](https://de.wikipedia.org/wiki/Magnet-Link) of the result | 