---
layout: post
title:  "Some Docker(-machine) Tips"
date:   2015-12-11 00:00:00
categories: docker tips
---

After playing `Docker` for some time (including adding these [tweaks for boot2docker installation](https://gist.github.com/houtianze/bbe6d9af9f7e21bf1eef)), I found some more ~~pitfalls~~ tips for Docker/boot2docker:

- boot2docker is based on Tiny Core Linux, which runs entirely in memory, so almost every change you made are gone when the boot2docker VM reboots. [^1]
- I said _almost_ because when the boot2docker VM starts, it will automatically mount the harddisk (/dev/sdX) and softlink 2 directories to it (`/var/lib/docker`, `/var/lib/boot2docker`), anything resides in these 2 directories are persisted (most notably the docker images and containers). [^2]
- `/var/lib/boot2docker/bootlocal.sh` gets automatically executed when the boot2docker VM boots, so it's the appropriate place for you to do some customization of your boot2docker VM. [^2]
- If you are using self-signed certificates for your private Docker registry, as you may have expected, you have to add its certificate(s) as trusted (note that if it's a certificate chain, you have to add all the certificates in the chain) in boot2docker or Docker or OS level (if you are using Linux). There are several ways:
  1. Copy the certificates to `/etc/docker/certs.d/` (boot2docker only, can use `/var/lib/boot2docker/bootlocal.sh` to accomplish)
  2. Append the certificates to `/etc/ssl/certs/ca-certificates.crt` (boot2docker only)
  3. Use `irgeek`'s hashing script here: [https://gist.github.com/irgeek/afb2e05775fff532f960](https://gist.github.com/irgeek/afb2e05775fff532f960) (boot2docker only)
  4. Use your OS's commands to add: [https://docs.docker.com/registry/insecure/][^3]

  After you've finished adding them, restart Docker or boot2docker VM for the changes to take effect 

References:

[^1]: https://github.com/boot2docker/boot2docker/blob/master/README.md
[^2]: https://github.com/boot2docker/boot2docker/issues/347
[^3]: https://docs.docker.com/registry/insecure/
