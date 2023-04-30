---
layout: post
title:  "A Poor Man's Geotagging for DSLR/cameras"
date:   2023-04-29 01:00:00
categories: photography camera slr gps geotagging geo
---

# Problem Statements

I took photos with both my phone (of course) and a DSLR in [this vacation](/california-holiday). However, my SLR doesn't come with GPS built in and an external SLR GPS is not only expensive, but also bulky to carry around (and may break if you are not careful when mounted). So after coming back from the trip, I was pondering if there a way to remedy this, or simply put:

_Is there away to geotag a batch of photos easily and (relatively) accurately?_

It turns out this is more less achievable with a result that is to my satisfaction.

# Solutions

## Ahead of Time

If you thought about this and would prepare ahead of time, a very simple solution would be using the (completely free and open-sourced) [Open GPX Tracker](https://wiki.openstreetmap.org/wiki/OpenGpxTracker) (There should be some Android equivalents, but I'm not sure which App is the best.) to record your trips where you would take photos, then simply import the GPX log file(s) following the guide of [DigiKam](https://userbase.kde.org/Digikam/Geotagging) or [exiftool](https://exiftool.org/geotag.html). I have yet to try this out, but I believe this should be an easy task.

## Postprocessing (a.k.a. I didn't prepare for it)

What about when you didn't think about this and never prepared (like me)? The solution won't be that perfect as the above one, but it is quite close in my opinion, provided that you have a habit of taking photos with both your phone and camera at a very close time when you are at a photo spot. The idea is simple - Use the GPS information stored in your phone photos to geotag the photos taken by your DSLR/camera which doesn't come with a GPS unit. But how? Again we rely on this _very_ powerful photo metadata managing tool - [exiftool](https://exiftool.org/).

Here are the steps:

### 1. Extract GPS log from phone photos. (I'm using iPhone, I'm not sure the Android metadata/exif are stored in a different format, you may need to tinker a bit yourself). The main guide is [here](https://exiftool.org/geotag.html#Inverse), however, there are some minor changes needed.

#### First, you need to modify the `gpx.fmt` template a little bit, as for my iPhone, it doesn't record the `gpsdatetime` metadata, instead I use `datetimeoriginal` (NOTE: As I tried out, you need to still keep the whole `DateFmt` line but just replace `gpsdatetime` with `datetimeoriginal` from the original `gpx.fmt` from the `exiftool` manual page, if you don't do this `iso8601` date time formatting, you won't be able to import the gps information to your DLSR/camera photos in the next step using `exiftool`. The line above (starting with `###`) is commented out.)

```text
#------------------------------------------------------------------------------
# File:         gpx.fmt
#
# Description:  Example ExifTool print format file to generate a GPX track log
#
# Usage:        exiftool -p gpx.fmt -ee3 FILE [...] > out.gpx
#
# Requires:     ExifTool version 10.49 or later
#
# Revisions:    2010/02/05 - P. Harvey created
#               2018/01/04 - PH Added IF to be sure position exists
#               2018/01/06 - PH Use DateFmt function instead of -d option
#               2019/10/24 - PH Preserve sub-seconds in GPSDateTime value
#
# Notes:     1) Input file(s) must contain GPSLatitude and GPSLongitude.
#            2) The -ee3 option is to extract the full track from video files.
#            3) The -fileOrder option may be used to control the order of the
#               generated track points when processing multiple files.
#------------------------------------------------------------------------------
#[HEAD]<?xml version="1.0" encoding="utf-8"?>
#[HEAD]<gpx version="1.0"
#[HEAD] creator="ExifTool $ExifToolVersion"
#[HEAD] xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
#[HEAD] xmlns="http://www.topografix.com/GPX/1/0"
#[HEAD] xsi:schemaLocation="http://www.topografix.com/GPX/1/0 http://www.topografix.com/GPX/1/0/gpx.xsd">
#[HEAD]<trk>
#[HEAD]<number>1</number>
#[HEAD]<trkseg>
#[IF]  $gpslatitude $gpslongitude
#[BODY]<trkpt lat="$gpslatitude#" lon="$gpslongitude#">
#[BODY]  <ele>$gpsaltitude#</ele>
###[BODY]  <time>$datetimeoriginal</time>
#[BODY]  <time>${datetimeoriginal#;my ($ss)=/\.\d+/g;DateFmt("%Y-%m-%dT%H:%M:%SZ");s/Z/${ss}Z/ if $ss}</time>
#[BODY]</trkpt>
#[TAIL]</trkseg>
#[TAIL]</trk>
#[TAIL]</gpx>
```

#### Second, the command line I use is something like this below:

`exiftool -ee3 -globaltimeshift +7 -fileOrder datetimeoriginal -p gpx.fmt <iPhone photo export dir>/*.jpeg > trip.gpx`

There are 2 things to note here:
- `-globaltimeshift +7`: Because the date time format written via the `gpx.fmt` template is UTC based (`iso8601`), you *need* to compensate the timezone. You can achieve this by putting an *opposite* sign to the same number, and then pass to the `-globaltimeshift` flag. In the example above, my photos are taken in the `-7h` timezone, and I need to put `+7` as the parameter value.

- The other set of argument `-fileOrder datetimeoriginal` that is also good to pass in so that the gps locations are stored in the chronological order.

### 2. Import the GPS location into your DSLR/camera, just run this simple command:

`exiftool -geotag trip.gpx <DSLR dir>`
