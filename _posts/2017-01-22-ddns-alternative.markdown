---
layout: post
title:  "An alternative to Dynamic DNS (DDNS) for your home server"
date:   2017-01-22 00:00:00
categories: ddns ngrok localtunnel
---

If you set up you own web server at your home (e.g. you own a sweet Raspberry Pi), and want to make it accessible to the world (assuming your server is behind NAT), one common way of doing this (without purchasing a domain) is to set up a Dynamic DNS domain pointing to your server.

It simply works. However, there's one tiny concern for me: by doing so, you expose your home router's IP to the internet, this is sort of privacy leak besides a tiny security risk of being scanned for exploits, may not be a big deal but it's something that I would bear in mind.

Recently I found an alternative to this and wish to share it here - [localtunnel.me](https://localtunnel.github.io/www/)[^1]. It will provide you with a `*.localtunnel.me` subdomain without exposing your server's actual IP. The way it does this is by tunneling traffics from/to your server. It supports https protocol which means you get a TLS certificate for free as well.

The setup is also quite straighfoward:
- Install NodeJS if haven't done so
- Run (sudo) `npm install -g localtunnel`
- Run `lt --port 80 --subdomain mycooluniquedomain` (assume your http server runs on port 80)
- Visit your site at https://mycooluniquedomain.localtunnel.me and sit back

[^1]: I know [ngrok](https://ngrok.com/) does things similarly, however, to choose your own subdomain, you have to fork some dollar, otherwise a new random subdomain is generated each time your run ngrok. That, in my opinion, makes it act like DDNS with limited practibility.

