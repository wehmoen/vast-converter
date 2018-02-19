PHP-VAST
========

[![Build Status](https://travis-ci.org/sokil/php-vast.png?branch=master&1)](https://travis-ci.org/sokil/php-vast)
[![Total Downloads](http://img.shields.io/packagist/dt/sokil/php-vast.svg?1)](https://packagist.org/packages/sokil/php-vast)
[![Coverage Status](https://coveralls.io/repos/github/sokil/php-vast/badge.svg?branch=master)](https://coveralls.io/github/sokil/php-vast?branch=master)

VAST Ad generator and parser library on PHP.

## Specs
* VAST 2.0 Spec: http://www.iab.net/media/file/VAST-2_0-FINAL.pdf
* VAST 3.0 Spec: http://www.iab.com/wp-content/uploads/2015/06/VASTv3_0.pdf
* VAST 4.0 Spec: http://www.iab.com/wp-content/uploads/2016/01/VAST_4-0_2016-01-21.pdf

## Install

Install library through composer:

```
composer require sokil/php-vast
```

## Quick start

```php
// create document
$factory = new \Sokil\Vast\Factory();
$document = $factory->create('2.0');
// or, if you have at least PHP5.4
$document = (new \Sokil\Vast\Factory())->create('2.0');
// creating through Document::create and other factory methods are now deprecated:
$document = \Sokil\Vast\Document::create('2.0');

// insert Ad section
$ad1 = $document
    ->createInLineAdSection()
    ->setId('ad1')
    ->setAdSystem('Ad Server Name')
    ->setAdTitle('Ad Title')
    ->addImpression('http://ad.server.com/impression');

// create creative for ad section
$ad1->createLinearCreative()
    ->setDuration(128)
    ->setVideoClicksClickThrough('http://entertainmentserver.com/landing')
    ->addVideoClicksClickTracking('http://ad.server.com/videoclicks/clicktracking')
    ->addVideoClicksCustomClick('http://ad.server.com/videoclicks/customclick')
    ->addTrackingEvent('start', 'http://ad.server.com/trackingevent/start')
    ->addTrackingEvent('pause', 'http://ad.server.com/trackingevent/stop')
    ->createMediaFile()
        ->setProgressiveDelivery()
        ->setType('video/mp4')
        ->setHeight(100)
        ->setWidth(100)
        ->setBitrate(2500)
        ->setUrl('http://server.com/media.mp4');

// get dom document
$domDocument = $document->toDomDocument();

// get XML string
echo $document;
```

This will generate:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<VAST version="2.0">
    <Ad id="ad1">
        <InLine>
            <AdSystem>Ad Server Name</AdSystem>
            <AdTitle><![CDATA[Ad Title]]></AdTitle>
            <Impression><![CDATA[http://ad.server.com/impression]]></Impression>
            <Creatives>
                <Creative>
                    <Linear>
                        <Duration>00:02:08</Duration>
                        <VideoClicks>
                            <ClickThrough><![CDATA[http://entertainmentserver.com/landing]]></ClickThrough>
                            <ClickTracking><![CDATA[http://ad.server.com/videoclicks/clicktracking]]></ClickTracking>
                            <CustomClick><![CDATA[http://ad.server.com/videoclicks/customclick]]></CustomClick>
                        </VideoClicks>
                        <TrackingEvents>
                            <Tracking event="start"><![CDATA[http://ad.server.com/trackingevent/start]]></Tracking>
                            <Tracking event="pause"><![CDATA[http://ad.server.com/trackingevent/stop]]></Tracking>
                        </TrackingEvents>
                        <MediaFiles>
                            <MediaFile delivery="progressive" type="video/mp4" height="100" width="100" bitrate="2500">
                                <![CDATA[http://server.com/media.mp4]]>
                            </MediaFile>
                        </MediaFiles>
                    </Linear>
                </Creative>
            </Creatives>
        </InLine>
    </Ad>
</VAST>
```
