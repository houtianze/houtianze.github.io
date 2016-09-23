---
layout: post
title:  "Fix Linuxbrew Compilation Errors on Raspberry Pi"
date:   2016-09-23 00:00:00
categories: homebrew linuxbrew pi
---

After you install [Linuxbrew](http://linuxbrew.sh) on Raspberry Pi, and you want to give it a try by running `brew install hello`, you might be surprised to be greeted with the following error:
``Error in `/usr/bin/gcc-4.9': double free or corruption (top):``.
How to fix it?

Fix (or rather, a Workaround)
-----------------------------
```
cd `brew --prefix`/Library/Homebrew/extend/ENV
if [ ! -f std.rb.orig ]
then
  cp std.rb std.rb.orig
fi
sed -i -r 's/(^\s*DEFAULT_FLAGS.*)"-march=.*?"/\1""/g' std.rb
```

Explanations
------------
After some Googling, this seems to boil down to this GCC bug:
[https://gcc.gnu.org/bugzilla/show_bug.cgi?id=70132
](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=70132)

At the time of writing, Raspbian (or rather its upstream, Debian jessie) ships with GCC version 4.9.2, [which is plagued with this bug](https://github.com/raspberrypi/linux/issues/1580). And Linuxbrew sets the CFLAGS and CXXFLAGS with `-march=native` (you can verify this by typing `brew --env`), which will trigger the exception. What this workaround does is to patch the related ruby code to strip this `-march=native` from the flags. Pity that [I couldn't figure out a way to do this wihtout modifying the source code](https://github.com/Linuxbrew/legacy-linuxbrew/issues/375).

CAVEAT
-----
You _may_ need to revert back the change (`cp std.rb.orig std.rb`) before upgrading Linuxbrew and/or re-apply the patch after.

