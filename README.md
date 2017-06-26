# A Jekyll plugin for sending & receiving webmentions via Webmention.io.

This Gem includes a suite of tools for managing webmentions in Jekyll:

* **[Tags](#tags)** - render webmention info into your templates:
  * [Count of webmentions](#webmention_count) (filterable)
  * [All webmentions](#webmentions) (filterable)
  * [Likes](#webmention_likes)
  * [Links](#webmention_links)
  * [Posts](#webmention_posts)
  * [Replies](#webmention_replies)
  * [Reposts](#webmention_reposts)
* **[Commands](#commands)** - Send webmentions you’ve made
* **[Generators](#generators)** - Collect webmentions from Webmention.io and gather sites you’ve mentioned

Currently supported webmention types (inbound):

* links
* likes
* posts
* replies
* reposts

## Configuration

This gem will work well out of the box, but is configurable in a number of ways. Note: all of these configuration options should nest under a `webmentions` key in your `_config.yml` file.

* `cache_folder` - by default, this gem will cache all files in the `.jekyll-cache`, but you can specify another location (like `_data`) if you like. In order to avoid collisions, all cache fles will be prefixed with "webmention_io_" unless your `cache_folder` value contains "webmention" (e.g. `.jekyll_cache/webmentions`)
* `cache_bad_uris_for` - In order to reduce unnecessary requests to servers that aren’t responding, this gem will keep track of them and avoid making new requests to them for 1 day. If you’d like to adjust this up or down, you can use this configuration value. It expects a number corresponding to the number of days you want to wait before trying the domain again.
* `legacy_domains` - If you’ve relocated your site from another URL or moved from to HTTPS from HTTP, you can use this configuration option to specify additional domains to append your `page.url` to. It expects an array.
* `templates` - If you would like to roll your own templates, you totally can. You will need to assign a hash of the template paths to use for loading each one.

### Simple Example:

```yml
webmentions:
  # Use my own cache folder
  cache_folder: .cache
  # skip bad URLs for 5 days
  cache_bad_uris_for: 5
  # I moved to www and then to https, so…
  legacy_domains:
    - http://aaron-gustafson.com
    - http://www.aaron-gustafson.com
```

### Exhaustive Example:

```yml
webmentions:
  cache_folder: .cache
  cache_bad_uris_for: 5
  legacy_domains:
    - http://aaron-gustafson.com
    - http://www.aaron-gustafson.com
  templates:
    count: _includes/webmentions/count.html
    likes: _includes/webmentions/likes.html
    links: _includes/webmentions/links.html
    posts: _includes/webmentions/posts.html
    replies: _includes/webmentions/replies.html
    reposts: _includes/webmentions/reposts.html
    webmentions: _includes/webmentions/webmentions.html
```

## Picking up Redirects

If you’ve ever changed the path to your posts, you may have used [the `jekyll-redirect-from` gem](https://github.com/jekyll/jekyll-redirect-from). `jekyll-webmention_io` will look for a `redirect_from` key in your YAML front matter and automatically include that original URL in any requests for webmentions so none get left behind.

## Tags

The various tag options provided by this gem are focused around display of information about incoming webmentions.

### `webmention_count`

Displays a count of webmentions for the current `post.url`:

	{% webmention_count post.url %}
	
The output will be a number.

You can optionally filter this number by one or more supported webmention types. For instance, if you only wanted posts and replies, you could use this format:

	{% webmention_count post.url posts replies %}

### `webmentions`

You can get a complete list of webmentions for a given `post.url` using the following liquid tag:

	{% webmentions post.url %}

The webmentions found, if any, will be piped into the webmentions template your specified in your configuration or the default one that ships with this gem.

You can optionally filter this number by one or more supported webmention types. For instance, if you only wanted posts and replies, you could use this format:

	{% webmentions post.url posts replies %}

#### Default template info

If you go with the default template, here’s a rundown of elements and class names in use in the template:

* `webmentions` - overall container (`div`)
* `webmentions__list` - the list of webmentions (`ol`)
* `webmentions__item` - the webmention container (`li`)
* `webmention` - the webmention itself (`article`)
	* `webmention--[type]` - modifier for the type
	* `webmention--no-author` - added if there’s no author info available
	* `webmention--no-photo` - added if there’s no photo of the author availble
	* `webmention--author-starts` - variant for when the author’s name starts the content (as in a tweet interaction such as a favorite or retweet)
	* `h-cite` - [Citation Microformat](http://microformats.org/wiki/h-cite)
* `webmention__author` - Author of the webmention (`div`)
  * `p-author` - [Citation Microformat](http://microformats.org/wiki/h-cite)
	* `h-card` - [Person Microformat](http://microformats.org/wiki/h-card)
* `u-url` - [Person Microformat](http://microformats.org/wiki/h-card) (`a`)
* `webmention__author__photo` - Author’s photo (`img`)
  * `u-photo` - [Person Microformat](http://microformats.org/wiki/h-card)
* `webmention__content` - The webmention’s content container (`div`)
  * `p-content` - [Citation Microformat](http://microformats.org/wiki/h-cite)
* `webmention__meta` - The webmention’s meta information container (`div`)
* `webmention__pubdate` - The publication date (`time`)
  * `dt-published` - [Citation Microformat](http://microformats.org/wiki/h-cite)
* `webmention__source` - The webmention permalink (`a`)
  * `u-url` - [Citation Microformat](http://microformats.org/wiki/h-cite)
* `webmentions__not-found` - The "no results" message shown if no mentions are found (`p`)

### `webmention_likes`

You can get a complete list of "like" webmentions for a given `post.url` using the following liquid tag:

	{% webmention_likes post.url %}

The webmentions found, if any, will be piped into the webmentions template your specified in your configuration or the default one that ships with this gem.

#### Default template info

If you go with the default template, here’s a rundown of elements and class names in use in the template:

* `webmentions` - overall container (`div`)
  * `webmentions--likes` - Identifies this as only pertaining to "likes"
* `webmentions__list` - the list of webmentions (`ol`)
* `webmentions__item` - the webmention container (`li`)
  * `webmention`
	* `webmention--like`
* `webmention__author` - Author of the webmention (`div`)
  * `p-author` - [Citation Microformat](http://microformats.org/wiki/h-cite)
	* `h-card` - [Person Microformat](http://microformats.org/wiki/h-card)
* `u-url` - [Person Microformat](http://microformats.org/wiki/h-card) (`a`)
* `webmention__author__photo` - Author’s photo (`img`)
  * `u-photo` - [Person Microformat](http://microformats.org/wiki/h-card)
* `webmentions__not-found` - The "no results" message shown if no mentions are found (`p`)

### `webmention_links`

You can get a complete list of "link" webmentions for a given `post.url` using the following liquid tag:

	{% webmention_links post.url %}

The webmentions found, if any, will be piped into the webmentions template your specified in your configuration or the default one that ships with this gem.

#### Default template info

If you go with the default template, here’s a rundown of elements and class names in use in the template:

* `webmentions` - overall container (`div`)
  * `webmentions--links` - Identifies this as only pertaining to "links"
* `webmentions__list` - the list of webmentions (`ol`)
* `webmentions__item` - the webmention container (`li`)
  * `webmention`
	* `webmention--like`
* `webmention__meta` - The webmention’s meta information container (`div`)
* `webmention__author` - Author of the webmention (`a`)
  * `h-card` - [Person Microformat](http://microformats.org/wiki/h-card)
  * `u-url` - [Person Microformat](http://microformats.org/wiki/h-card) (`a`)
* `webmention__source` - The webmention permalink (`a`)
  * `u-url` - [Citation Microformat](http://microformats.org/wiki/h-cite)
* `webmention__pubdate` - The publication date (`time`)
  * `dt-published` - [Citation Microformat](http://microformats.org/wiki/h-cite)
* `webmentions__not-found` - The "no results" message shown if no mentions are found (`p`)

### `webmention_posts`

You can get a complete list of "posts" webmentions for a given `post.url` using the following liquid tag:

	{% webmention_posts post.url %}

The webmentions found, if any, will be piped into the webmentions template your specified in your configuration or the default one that ships with this gem.

#### Default template info

If you go with the default template, here’s a rundown of elements and class names in use in the template:

* `webmentions` - overall container (`div`)
  * `webmentions--posts` - Identifies this as only pertaining to "posts"
* `webmentions__list` - the list of webmentions (`ol`)
* `webmentions__item` - the webmention container (`li`)
  * `webmention`
	* `webmention--post`
* `webmention__title` - The title of the post (`a`)
	* `webmention__source`
  * `u-url` - [Citation Microformat](http://microformats.org/wiki/h-cite)
* `webmention__meta` - The webmention’s meta information container (`div`)
* `webmention__pubdate` - The publication date (`time`)
  * `dt-published` - [Citation Microformat](http://microformats.org/wiki/h-cite)
* `webmentions__not-found` - The "no results" message shown if no mentions are found (`p`)

### `webmention_replies`

You can get a complete list of "reply" webmentions for a given `post.url` using the following liquid tag:

	{% webmention_replies post.url %}

The webmentions found, if any, will be piped into the webmentions template your specified in your configuration or the default one that ships with this gem.

#### Default template info

If you go with the default template, here’s a rundown of elements and class names in use in the template:

* `webmentions` - overall container (`div`)
  * `webmentions--replies` - Identifies this as only pertaining to "replies"
* `webmentions__list` - the list of webmentions (`ol`)
* `webmentions__item` - the webmention container (`li`)
  * `webmention`
	* `webmention--reply`
* `webmention__meta` - The webmention’s meta information container (`div`)
* `webmention__author` - Author of the webmention (`a`)
  * `h-card` - [Person Microformat](http://microformats.org/wiki/h-card)
  * `u-url` - [Person Microformat](http://microformats.org/wiki/h-card) (`a`)
* `webmention__author__photo` - Author’s photo (`img`)
  * `u-photo` - [Person Microformat](http://microformats.org/wiki/h-card)
* `p-name` - Author’s name (`b`)
* `webmention__source` - The webmention permalink (`a`)
  * `u-url` - [Citation Microformat](http://microformats.org/wiki/h-cite)
* `webmention__pubdate` - The publication date (`time`)
  * `dt-published` - [Citation Microformat](http://microformats.org/wiki/h-cite)
* `webmentions__not-found` - The "no results" message shown if no mentions are found (`p`)
	
### `webmention_reposts`

You can get a complete list of "repost" webmentions for a given `post.url` using the following liquid tag:

	{% webmention_reposts post.url %}

The webmentions found, if any, will be piped into the webmentions template your specified in your configuration or the default one that ships with this gem.

#### Default template info

If you go with the default template, here’s a rundown of elements and class names in use in the template:

* `webmentions` - overall container (`div`)
  * `webmentions--reposts` - Identifies this as only pertaining to "likes"
* `webmentions__list` - the list of webmentions (`ol`)
* `webmentions__item` - the webmention container (`li`)
  * `webmention`
	* `webmention--repost`
* `webmention__author` - Author of the webmention (`div`)
  * `p-author` - [Citation Microformat](http://microformats.org/wiki/h-cite)
	* `h-card` - [Person Microformat](http://microformats.org/wiki/h-card)
* `u-url` - [Person Microformat](http://microformats.org/wiki/h-card) (`a`)
* `webmention__author__photo` - Author’s photo (`img`)
  * `u-photo` - [Person Microformat](http://microformats.org/wiki/h-card)
* `webmentions__not-found` - The "no results" message shown if no mentions are found (`p`)

## Commands

Webmentions are not automatically sent when building your Jekyll project as that may not always be desirable. That said, this gem does automatically collect mentions made in your posts. It caches them and makes them available to you to send using the following command:

```
jekyll webmention
```

## Generators

This gem includes two generators. One collects any webmentions referencing your posts. The other collects any webmentions you may have made in order to queue them up for sending using [the `jekyll webmention` command](#commands).