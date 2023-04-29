---
layout: post
title:  "(Python) pip install using the testpypi repository"
date:   2015-12-29 00:00:00
categories: python pip
---

After fiddling sometime with Python `pip` command parameters, trying to let it use the https://testpypi.python.org repository instead of the default `pypi` repository. I found the comnand to use is:

`pip install -i https://testpypi.python.org/simple/ <package name>`

Note the **simple** part... Even the offical website documented it wrong. [^1]

[^1]: [https://wiki.python.org/moin/TestPyPI](https://wiki.python.org/moin/TestPyPI)
