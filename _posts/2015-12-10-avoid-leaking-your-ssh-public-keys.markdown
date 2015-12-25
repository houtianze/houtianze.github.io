---
layout: post
title:  "Avoid leaking your SSH public keys"
date:   2015-12-10 00:00:00
categories: ssh privacy
---

In default SSH config, it's most likely that if you connect an unknown SSH server, the server is able to enumerate all your public keys in your agent by keeping rejecting your public keys tried. This seems how the SSH protocol work. It doesn't pose a security risk, but it does have some Privacy concerns - those public keys can identify who you are on the web. The fix? It's simple: I think either of the following will work (I personally tried method #2):

1. Append to the end of `/etc/ssh/ssh_config` or put at the beginning of `~/.ssh/config` (as suggested by `chrisfosterelli` on HN[^1]) the following:
{% highlight bash %}
# Ignore SSH keys unless specified in Host subsection
IdentitiesOnly yes
{% endhighlight %}
2. Append to the end of `/etc/ssh/ssh_config` or `~/.ssh/config` the following:
{% highlight bash %}
Host *
  IdentityFile /nosuchkey
  IdentitiesOnly yes
{% endhighlight %}

Reference:

- [^1]: [https://news.ycombinator.com/item?id=10004678](https://news.ycombinator.com/item?id=10004678)