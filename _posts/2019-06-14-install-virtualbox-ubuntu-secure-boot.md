---
layout: post
title:  "How to install VirtualBox on Ubuntu 18.04 when secure boot is active"
date:   2019-06-13 20:00:00 +0200
categories: virtualbox ubuntu
author: Torsten Walter
---

```
sudo update-secureboot-policy --new-key
find $(dirname $(modinfo -n vboxdrv)) -name "*.ko" -exec sudo kmodsign sha512 /var/lib/shim-signed/mok/MOK.priv /var/lib/shim-signed/mok/MOK.der {} \;
sudo update-secureboot-policy --enroll-key
```


# Generate a key for signing your kernel modules:

```
openssl req -new -x509 -newkey rsa:2048 -keyout MOK.priv -outform DER -out MOK.der -nodes -days 36500 -subj "/CN=DKMS signing key/"

sudo mokutil --import MOK.der

sudo mv MOK.* /root/
```

http://lektiondestages.blogspot.com/2018/04/signing-your-kernel-modules-on-ubuntu.html


# Reboot and add the key

> You will be presented a blue text screen. Choose "Enroll MOK", then you can choose to view your key or just "Continue". Choose "Yes" and input the password from before. Your key should be added now and you can reboot.

 http://lektiondestages.blogspot.com/2018/04/signing-your-kernel-modules-on-ubuntu.html

# Sign the virtualbox kernel modules

```
find $(dirname $(modinfo -n vboxdrv)) -name "*.ko" -exec sudo kmodsign sha512 /root/MOK.priv /root/MOK.der {} \;
```

# Load the kernel modules

```
modprobe vboxdrv
```

