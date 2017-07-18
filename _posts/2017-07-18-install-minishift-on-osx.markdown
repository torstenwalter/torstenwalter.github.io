---
layout: post
title:  "Installing Minishift on OS X"
date:   2017-07-18 13:30:00 +0100
categories: minishift openshift homebrew
---

If you want to play around with OpenShift locally then Minishift is a nice option.

When I tried to install it then I got an error message that the openshift docker image could not be loaded.
Luckily this [comment](https://github.com/minishift/minishift/issues/109#issuecomment-254895497) pointed me
in the right direction, that DNS was not working properly.

## Install dnsmasq

```
brew update
brew install dnsmasq
```

Configure dnsmasq to listen on all network interfaces:
```/usr/local/etc/dnsmasq.conf
listen-address=0.0.0.0
listen-address=::1
```

Restart dnsmasq
```
sudo brew services restart dnsmasq
```

test if nameserver listens on all ip adresses
```
# use 127.0.0.1 to lookup index.docker.io
nslookup index.docker.io 127.0.0.1
# use 192.168.64.1 to lookup index.docker.io
nslookup index.docker.io 192.168.64.1
```

## Install Minishift

### Install yhyve
https://docs.openshift.org/latest/minishift/getting-started/setting-up-driver-plugin.html
```
brew install docker-machine-driver-xhyve
```

### Install VirtualBox
```
brew cask install virtualbox
```

### Install Minishift
```
brew cask install minishift
minishift start
```

