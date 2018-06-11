---
layout: post
title: Assign Names To Each Teensy Board in Serial Ports
---

##First Step: find a unique identifiers for the devices. We can select the serial ID.


```bash
udevadm info -a -n /dev/ttyACM0 | grep '{serial}' | head -n1
        ATTRS{serial}=="3968360"
```

##Second Step: create symbolic link for the serial devices

In /etc/udev/rules.d/, edit the file 49-teensy.rules , add the following lines there.

```bash
KERNEL=="ttyACM0", ATTRS{idVendor}=="16c0", ATTRS{idProduct}=="04[789]?", ATTRS{serial}=="3968360",SYMLINK+="channel1"
KERNEL=="ttyACM1", ATTRS{idVendor}=="16c0", ATTRS{idProduct}=="04[789]?", ATTRS{serial}=="4120870",SYMLINK+="channel2"
```


##Third Step: activate the rule and check
```bash
sudo udevadm trigger
ls -l channel1
    lrwxrwxrwx 1 root root 7 Jun 11 14:21 channel1 -> ttyACM0
ls -l channel2
    lrwxrwxrwx 1 root root 7 Jun 11 14:21 channel2 -> ttyACM1

```