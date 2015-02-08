针对小米wifi 修改

1.  lsusb

	Bus 001 Device 033: ID 2717:4106  (这2个数字就是小米wifi的id)

2.  cd  mt7601/src
	vim common/rtusb_dev_id.c 
        
	在#ifdef MT7601U段添加

	        	{USB_DEVICE(0x2717,0x4106)}, /* xiaomi wifi */

然后继续下面的make


## MT7601U Linux driver

Many cheap USB wifi dongles use the MT7601U chip.

<img src="http://porjo.github.io/mt7601/dongle1.jpg" width="150">
<img src="http://porjo.github.io/mt7601/dongle2.jpg" width="150">

Unfortunately, the driver is not part of the Linux kernel source tree (yet?). This repository contains:
* source code from [`DPO_MT7601U_LinuxSTA_3.0.0.4_20130913.tar.bz2`](http://www.mediatek.com/en/downloads/mt7601u-usb/) (md5sum `5f440dccc8bc952745a191994fc34699`) 
* file `DPO_MT7601U_LinuxSTA_3.0.0.4_20130913-Linux-3.17.0-v2.patch`
 has been applied

The patch improves stability and performance for kernels >= 3.x and has been tested with the following kernels:

- 3.15.10-200.fc20.x86_64
- 3.16.1-301.fc21.x86_64
- 3.16.1-301.fc21.i686
- 3.17.0-0.rc2.git3.1.fc22.i686
- 3.17.0-0.rc2.git3.1.fc22.x86_64
- 3.12.26-1.20140808git4ab8abb.rpfr20.armv6hl.bcm2708

### Usage

First install kernel-devel for your Linux distro

```sh
$ git clone https://github.com/porjo/mt7601.git
$ cd mt7601/src
$ make
$ mkdir -p /etc/Wireless/RT2870STA/
$ cp RT2870STA.dat /etc/Wireless/RT2870STA/
$ insmod os/linux/mt7601Usta.ko
```

If the module has loaded OK, you should see `mt7601Usta` listed in the output of `lsmod` and a new network interface `ra0` should be present in the output of `ip link`.

If all goes well, you can permanently install the driver with `make install`.

#### Gentoo

In order to successfully compile this driver for Gentoo, you must compile your kernel with the appropriate wireless extensions included. One way to do that is by enabling *Cisco/Aironet 34X/35X/4500/4800 PCMCIA cards* wireless module. If you see errors when compiling the driver, check to see if you have the necessary wireless extensions by running `zgrep -i wext /proc/config.gz`. The output should look something like:

```
CONFIG_WEXT_CORE=y
CONFIG_WEXT_PROC=y
CONFIG_WEXT_SPY=y
CONFIG_WEXT_PRIV=y
CONFIG_CFG80211_WEXT=y
```

More discussion can be found [here](http://rt2x00.serialmonkey.com/pipermail/users_rt2x00.serialmonkey.com/2013-January/005587.html)

#### Debian/Ubuntu

There is a PPA repo available containing a DKMS-capable package based on this repo:

https://code.launchpad.net/~thopiekar/+archive/ubuntu/mt7601

Thanks to @thopiekar 


### History

On 26 Aug, 2014 user **@poma** posted to the [linux-wireless](http://wireless.kernel.org/en/developers/MailingLists) mailing list discussing the poor state of driver support for this chipset. Thread can be seen here:

http://www.spinics.net/lists/linux-wireless/msg126115.html

An inital patch was released on 28 Aug, 2014 with the following comment:
```
A patch[1] is composed partly from the RT3573 source code patched by ashaffer, from Andreas work, some of the ideas are from the beagleboard community, and some of my. :)
Debug(trace) is turned off.

Device now works more or less OK but slow, max. 10 Mbit, although connectable is only within the "N" & "N/G" modes.
What is important is the system no longer crashes, and disconnection are rare.
Generally better than before.

```

A second patch was released on 31 Aug, 2014 with the following comment:

```
A new patch[1] mainly based on patches at 
https://github.com/ashaffer/rt3573sta
and several network throughput tests via the Iperf.
```

### Credits

**Source code:** (c) Copyright 2002-2013, MediaTek Inc. (released under GPLv2)

**Patch:** @poma at [linux-wireless](http://wireless.kernel.org/en/developers/MailingLists) mailing list
