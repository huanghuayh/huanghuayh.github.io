---
layout: post
title: How to setup a wireless camera network using beaglebone
---

Hardware




* Install the Debian image on beaglebone. The image I use is Debian 7.8 (BeagleBone, BeagleBone Black - 4GB SD) 2015-03-01.

* Setup control of the beaglebone. ssh 192.168.7.2 -l root
  if the following error occurs, it means that you have used the same ip address for ssh connection before.
{%highlight shell%}
Host key verification failed.
{% endhighlight %}

  (optional) use the following command should the error occurs

  {%highlight shell%}
    ssh-keygen -R 192.168.7.2 -f /home/harry/.ssh/known_hosts
  {% endhighlight %}


* enable beaglebone to the internet via the host computer


In the BBB console type the following

{%highlight shell%}
ifconfig usb0 192.168.7.2
route add default gw 192.168.7.1
 {%endhighlight %}

In the linux console of host system type

{%highlight shell%}
sudo su
#wlp3s0 is my internet facing interface, enp0s20u1 is the BeagleBone USB connection
ifconfig eth5 192.168.7.1
iptables --table nat --append POSTROUTING --out-interface wlp3s0 -j MASQUERADE
iptables --append FORWARD --in-interface enp0s20u1 -j ACCEPT
echo 1 > /proc/sys/net/ipv4/ip_forward
{%endhighlight%}

In case network not accessible then type the following in BBB terminal

{%highlight shell%}
echo "nameserver 8.8.8.8" >> /etc/resolv.conf
{%endhighlight%}



* Load the camera driver to the beaglebone. https://github.com/twam/v4l2grab

    1. Install the required libraries (libv4l and libjpeg) and autotools

       sudo apt-get update

       Ubuntu/Debian: sudo apt-get install libjpeg8-dev libv4l-dev autoconf automake libtool git

       Gentoo: emerge -va libjpeg-turbo libv4l
    2. Clone the repository:
       sudo apt-get install git
       git clone https://github.com/twam/v4l2grab.git

       if you get the following error:

       error: server certificate verification failed. CAfile: /etc/ssl/certs/ca-certificates.crt CRLfile: none while accessing https://github.com/twam/v4l2grab.git/info/refs
       fatal: HTTP request failed


       Then use the command: export GIT_SSL_NO_VERIFY=1, and repeat git clone https://github.com/twam/v4l2grab.git

    3. Go into directory
      cd v4l2grab
    4. Creating autotools files
      ./autogen.sh
    5. Run configure
      ./configure
    6. Run make
      make
      sudo make install


* Setup wireless network.
    {%highlight shell%}
    ifconfig wlan0 up
    ifconfig wlan0
    iwconfig wlan0 essid CEWIT-guest
    dhclient wlan0
    ifconfig
    {% endhighlight %}
    
    if successful, we will see something like what follows:

    {%highlight shell%}
    wlan0     Link encap:Ethernet  HWaddr 00:e0:4c:0b:e4:bf  
              inet addr:172.20.249.41  Bcast:172.20.251.255  Mask:255.255.252.0
              inet6 addr: fe80::2e0:4cff:fe0b:e4bf/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:29 errors:0 dropped:232 overruns:0 frame:0
              TX packets:28 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000
              RX bytes:3801 (3.7 KiB)  TX bytes:6711 (6.5 KiB)
    {%endhighlight%}

    copy the ip address: 172.20.249.41


* Remotely control the camera node using ssh. In another terminal, do the following
    {%highlight shell%}
    ssh root@172.20.249.41  '~/v4l2grab/v4l2grab -o ~/img.jpg -I -1'
    scp root@172.20.249.41:~/img.jpg  "~"
    {% endhighlight %}



publication:
