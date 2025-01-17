# \Suin\RSSWriter

`\Suin\RSSWriter` is yet another simple RSS writer library for PHP 5.4 or later. This component is Licensed under MIT license.

This library can also be used to publish Podcasts.

## Quick demo

Blog

```php
$feed = new Feed();

$channel = new Channel();
$channel
    ->title('Channel Title')
    ->description('Channel Description')
    ->url('http://blog.example.com')
    ->feedUrl('http://blog.example.com/rss')
    ->language('en-US')
    ->copyright('Copyright 2012, Foo Bar')
    ->pubDate(strtotime('Tue, 21 Aug 2012 19:50:37 +0900'))
    ->lastBuildDate(strtotime('Tue, 21 Aug 2012 19:50:37 +0900'))
    ->ttl(60)
    ->pubsubhubbub('http://example.com/feed.xml', 'http://pubsubhubbub.appspot.com') // This is optional. Specify PubSubHubbub discovery if you want.
    ->appendTo($feed);

// Blog item
$item = new Item();
$item
    ->title('Blog Entry Title')
    ->description('<div>Blog body</div>')
    ->contentEncoded('<div>Blog body</div>')
    ->url('http://blog.example.com/2012/08/21/blog-entry/')
    ->author('John Smith')
    ->pubDate(strtotime('Tue, 21 Aug 2012 19:50:37 +0900'))
    ->guid('http://blog.example.com/2012/08/21/blog-entry/', true)
    ->preferCdata(true) // By this, title and description become CDATA wrapped HTML.
    ->appendTo($channel);

echo $feed; // or echo $feed->render();
```

Output:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<rss xmlns:content="http://purl.org/rss/1.0/modules/content/" xmlns:atom="http://www.w3.org/2005/Atom" version="2.0">
  <channel>
    <title>Channel Title</title>
    <link>http://blog.example.com</link>
    <description>Channel Description</description>
    <language>en-US</language>
    <copyright>Copyright 2012, Foo Bar</copyright>
    <pubDate>Tue, 21 Aug 2012 10:50:37 +0000</pubDate>
    <lastBuildDate>Tue, 21 Aug 2012 10:50:37 +0000</lastBuildDate>
    <ttl>60</ttl>
    <atom:link rel="self" href="http://example.com/feed.xml" type="application/rss+xml"/>
    <atom:link rel="hub" href="http://pubsubhubbub.appspot.com"/>
    <item>
      <title><![CDATA[Blog Entry Title]]></title>
      <link>http://blog.example.com/2012/08/21/blog-entry/</link>
      <description><![CDATA[<div>Blog body</div>]]></description>
      <content:encoded><![CDATA[<div>Blog body</div>]]></content:encoded>
      <guid>http://blog.example.com/2012/08/21/blog-entry/</guid>
      <pubDate>Tue, 21 Aug 2012 10:50:37 +0000</pubDate>
      <author>John Smith</author>
    </item>
    <item>
      <title>Some Podcast Entry</title>
      <link>http://podcast.example.com/2012/08/21/podcast-entry/</link>
      <description>&lt;div&gt;Podcast body&lt;/div&gt;</description>
      <enclosure url="http://podcast.example.com/2012/08/21/podcast.mp3" type="audio/mpeg" length="4889"/>
    </item>
  </channel>
</rss>
```

Podcast

```php
$feed = new Feed();

$itunesCategories = [
    'Sports' => null,
    'Technology' => [
        'Gadgets' => null,
    ],
    'Arts' => null,
];

$channelPodcast = new ChannelPodcast();

$channelPodcast
    ->author('Author')
    ->subtitle('Subtitle')
    ->summary('Summary')
    ->owner(['name'=> 'John', 'email' => 'john@example.com'])
    ->image('https://www.example.com/picture.jpg')
    ->category($itunesCategories);

$channel = new Channel();

$channel
    ->title('Channel Title')
    ->description('Channel Description')
    ->url('http://blog.example.com')
    ->language('en-US')
    ->copyright('Copyright 2012, Foo Bar')
    ->pubDate(strtotime('Tue, 21 Aug 2012 19:50:37 +0900'))
    ->lastBuildDate(strtotime('Tue, 21 Aug 2012 19:50:37 +0900'))
    ->ttl(60)
    ->podcast($channelPodcast)
    ->appendTo($feed);

$itemPodcast = new ItemPodcast();
$itemPodcast
    ->author('nono')
    ->subtitle('I am nono subtitle')
    ->image('https://www.example.com/picture.jpg');

$item = new Item();
$item
    ->title('Some Podcast Entry')
    ->description('<div>Podcast body</div>')
    ->url('http://podcast.example.com/2012/08/21/podcast-entry/')
    ->enclosure('http://podcast.example.com/2012/08/21/podcast.mp3', 4889, 'audio/mpeg')
    ->podcast($itemPodcast)
    ->appendTo($channel);

print $feed->asPodcast()->render();
```

Output:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<rss version="2.0" xmlns:itunes="http://www.itunes.com/dtds/podcast-1.0.dtd" xmlns:googleplay="http://www.google.com/schemas/play-podcasts/1.0">
  <channel>
    <title>Channel Title</title>
    <link>http://blog.example.com</link>
    <description>Channel Description</description>
    <language>en-US</language>
    <copyright>Copyright 2012, Foo Bar</copyright>
    <pubDate>Tue, 21 Aug 2012 18:50:37 +0800</pubDate>
    <lastBuildDate>Tue, 21 Aug 2012 18:50:37 +0800</lastBuildDate>
    <ttl>60</ttl>
    <itunes:author>Author</itunes:author>
    <itunes:subtitle>Subtitle</itunes:subtitle>
    <itunes:summary>Summary</itunes:summary>
    <itunes:owner>
      <itunes:name>John</itunes:name>
      <itunes:email>john@example.com</itunes:email>
    </itunes:owner>
    <itunes:image href="https://www.example.com/picture.jpg"/>
    <itunes:category text="Sports"/>
    <itunes:category text="Technology">
      <itunes:category text="Gadgets"/>
    </itunes:category>
    <itunes:category text="Gadgets"/>
    <itunes:category text="Arts"/>
    <item>
      <title>Some Podcast Entry</title>
      <link>http://podcast.example.com/2012/08/21/podcast-entry/</link>
      <description>&lt;div&gt;Podcast body&lt;/div&gt;</description>
      <enclosure url="http://podcast.example.com/2012/08/21/podcast.mp3" type="audio/mpeg" length="4889"/>
      <itunes:author>nono</itunes:author>
      <itunes:subtitle>I am nono subtitle</itunes:subtitle>
      <itunes:image href="https://www.example.com/picture.jpg"/>
    </item>
  </channel>
</rss>
```

## Installation

### Easy installation

You can install directly via [Composer](https://getcomposer.org/):

```bash
$ composer require tronice/php-rss-writer
```

### Manual installation

Add the following code to your `composer.json` file:

```json
{
    "require": {
        "tronice/php-rss-writer": "dev-master@dev"
    }
}
```

...and run composer to install it:

```bash
$ composer install
```

Finally, include `vendor/autoload.php` in your product:

```php
require_once 'vendor/autoload.php';
```

## How to use

The [`examples`](examples) directory contains usage examples for RSSWriter.

If you want to know APIs, please see [`FeedInterface`](src/Suin/RSSWriter/FeedInterface.php), [`ChannelInterface`](src/Suin/RSSWriter/ChannelInterface.php) and [`ItemInterface`](src/Suin/RSSWriter/ItemInterface.php).

## How to Test

```sh
$ vendor/bin/phpunit
```

## Test through PHP 5.4 ~ PHP 7.0

```console
$ docker-compose up
```

## License

MIT license
