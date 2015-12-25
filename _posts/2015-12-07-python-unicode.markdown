---
layout: post
title:  "Handling Unicode in Python"
date:   2015-12-07 00:00:00
categories: python unicode
---

After using Python for some time, I found Unicode in Python is somewhat confusing, so here I'd like to share some of my primary understandings.

In short, if there were only Python 3, life would be much happier, because Python 3 string semantics is quite clear. But reality is not that neat sometimes, and we have no choice but to deal with the legacy issues (Python 2).

The Data Type Differences
-------------------------

One of the major source of confusion is due to difference of the actual data type used to represent `str` in Python 2 and 3: In Python 2, `str` is `bytes`, and Unicode string is `unicode`. In Python, `str` is Unicode, thus Unicode string is represented in, well, `str`, and there is no such built-in type named `unicode` at all. This is where many confusions arise.

The following table illustrates the relationships of different data types in Python 2 and 3:

| Type    | Python 2 | Python 3 |
|---------|----------|----------|
| Bytes   | `str`    | `bytes`  |
| Unicode | `unicode`| `str`    |

<br />

Literals
========
For `Bytes`, the literal you use is _always_ `b'ascii'` in both Python 2 and 3, but things again get complex for `Unicode` data type:

- In Python 2, you can use literal like `u'unicode'` to denote unicode strings.
- In Python 3, _plain_ string literal like `'plain'` is _always_ treated as Unicode. `u'unicode'` denotes Unicode string as well, but it's not accepted in Python 3.0 - 3.2 [^1]

Then how about ubiquitous _plain_ string literal like `'plain'` in Python 2? Well, what it means depends on whether the following line is present at the beginning of a Python source file:

`from __future__ import unicode_literals`

- When the above line is not present, `'plain'` is treated as Bytes (`bytes` / `str` in Python 2)
- When the above line is present, `'plain'` is treated as Unicode (`unicode` in Python 2)

So if you understand the above, hopefully you won't be so confused reading old / other's codes.

How about writing a new Python program that you want both 2 and 3 compatible? Well, my suggestion is like this:

> Always use the `from __future__ import unicode_literals` statement.

The major advantages is that it gives the same meaning to `plain` string literals in both Python 2 and 3, and using Unicode is probably your best bet in string handlings.


<a name="ref-encoding-decoding"></a>Encoding and Decoding
---------------------------------------------------------
- Encoding: Convert `string` to `bytes`. (The `.encode()` function)
- Decoding: Convert `bytes` to `string`. (The `.decode()` function)

For Python 3, this is a clear-cut: A `string` (Unicode) can only be encoded to `bytes` using `encode()`, A `bytes` can only be decoded to a `string` (Unicode) using `decode()`. You can't do it the other way round - Calling `decode` on a `string` or calling `encode` on a `bytes` will give you an error.

Python 2 again is much messier, you can call `decode()` / `decode()` on either `str` (`bytes`) or `unicode`, though as stated, doing it the other way doesn't make much sense.

Both `.encode()` and `.decode()` can take an `encoding` parameter, and normally the default encoding is OK. But in some situations, exceptions can be raised:

- When encoding (to `bytes`), if the specified encoding can not represent some of the contents in the original string (e.g. you are trying to encode some Chinese characters using the 'ASCII' encoding), a UnicodeEncodingError is raised.
- Similarly, when decoding (from `bytes`), if the original bytes contain some invalid byte sequence (impossible in the given encoding, for example, when you try to decode using 'UTF-8', but the original bytes contain some sequence that is not going to appear in any valid 'UTF-8' encoded bytes), a UnicodeDecodeError occurs.

Check If an Object is `string`
-----------------------------
In Python 3, it's simple: `isinstance(obj, str)` since there is only one type of string  (`str`, which is Unicode), but in Python 2, you have to do: `isinstance(obj, basestring)`, this is because both `str` (`bytes`) and `unicode`(Unicode) are considered to be `string`, and their parent class is this `basestring`.


File System Encoding
--------------------
This isn't strictly a Python specific problem, but the knowledge is useful if you want to write robust programs that deals with files, even their names may be garbled. And this problem is thorny.

Problems:

- On *nix, underlying a File System, path (directory / file) names _don't_ have an encoding with it, so they are just `bytes` and can contain any sequence of bytes (except reserved '/' or `nul` on Linux if I'm not wrong). So to convert them to `strings`, we need an encoding, which in Python is provided by the `sys.getfilesystemencoding()` (almost always 'utf-8') function. Now I think you already sense a problem here right? Yes, the problem is that since the input `bytes` are arbitrary, it's possible that we can't decode them using the given encoding (garbled text). [^2] [^5]

- On Windows, however, due to the API design, you have to access the path names using Unicode [^2]  

So how would we tackle this problem? I think the "complete and proper" solution might be of much efforts, and here are just my opinions:

- For simplicity, always use Unicode for path names, and then you are always safe on Windows, and safe on *nix as long as there is no garbled path names on user's computer. Python 3 provides one more layer of protection - the `surrogateescape encoding error handler`. [^3] [^4] So this may work even for garbled path names, but I haven't tried and can't confirm about this.

- To be able to handle arbitrary path names on *nix, you then have to use `bytes` for path names, which I think will bring a lot of headaches doing string to `bytes` conversions, and the program probably won't work 100% for Unicode path names on Windows. This sounds a nuisance.   

Generally, you should use Unicode

Locale Settings
---------------
There is a bunch of other functions: `sys.getdefaultencoding()`, `locale.getdefaultlocale()`, `locale.getpreferredencoding()` I yet have to explore, and `sys.stdin` and `sys.stdout` both have their own encodings. They are not covered here (a nicer way to say I don't know) 

References
==========
- [https://docs.python.org/2/howto/unicode.html](https://docs.python.org/2/howto/unicode.html)
- [https://docs.python.org/3/howto/unicode.html](https://docs.python.org/3/howto/unicode.html)

[^1]: [http://stackoverflow.com/a/29149324/404271](http://stackoverflow.com/a/29149324/404271)
[^2]: [https://docs.python.org/3/library/os.path.html](https://docs.python.org/3/library/os.path.html)
[^3]: [https://docs.python.org/3/library/os.html#file-names-command-line-arguments-and-environment-variables](https://docs.python.org/3/library/os.html#file-names-command-line-arguments-and-environment-variables)
[^4]: [https://www.python.org/dev/peps/pep-0383/](https://www.python.org/dev/peps/pep-0383/)
[^5]: [https://github.com/houtianze/bypy/issues/195](https://github.com/houtianze/bypy/issues/195)
