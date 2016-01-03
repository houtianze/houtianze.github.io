---
layout: post
title:  "Another Python Unicode Fiasco on JSON"
date:   2016-01-03 00:00:00
categories: python unicode json
---

It's a re-post of my comment here: [^1]

Basically, I think there is a bug in the `json.dump()` function in Python **2** (only) - It can't dump a Python (dictionary / list) data containing non-ASCII characters, *even* you open the file with the `encoding = 'utf-8'` parameter. (i.e. no matter what you do). However, `json.dump()` works fine on Python 3 and `json.dumps()` works on both Python 2 and 3.

The following code breaks in Python 2 with exception `TypeError: must be unicode, not str` (Python 2.7.6, Debian):

{% highlight python %}
import json
data = {u'\u0430\u0431\u0432\u0433\u0434': 1} #{u'абвгд': 1}
with open('data.txt', 'w') as outfile:
    json.dump(data, outfile)
{% endhighlight %}

It, however, works fine in Python 3.

So in summary: To dump a JSON *correctly* on both Python 2 and 3, you need the following code (`json.dumps()` uses `'utf-8'` as the default encoding, which agrees with the `io.open()` parameter) 
{% highlight python %}
import io, json
with io.open('data.txt', 'w', encoding='utf-8') as f:
    f.write(json.dumps(data))
{% endhighlight %}

In Python 3, however, you can simply use the following:
{% highlight python %}
import io, json
with io.open('data.txt', 'w', encoding='utf-8') as f:
    json.dump(data, f)
{% endhighlight %}

### References:

[^1]: [http://stackoverflow.com/a/34574105/404271](http://stackoverflow.com/a/34574105/404271)