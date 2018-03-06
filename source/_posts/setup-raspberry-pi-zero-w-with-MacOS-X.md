title: How to setup raspberry pi zero w with MacOS X
tags: raspberrypi
date: 2017-06-21 23:38:44
---


# Step 1 Prepare tools

To setup a brand new [raspberrypi zero w](https://www.raspberrypi.org/products/pi-zero-w/) you need prepare following tools:

* A raspberrypi zero w
* mini HDMI to HDMI convert
* Micro SD card
* USB OTG
* Mouse & Keyboard
* An external SD card reader

All coneectors can come from seller when you purchase Raspberrypi zero w.

<!--more-->


# Step 2 Download Raspbian Image

You can download official [Raspbian OS](https://www.raspberrypi.org/downloads/raspbian/) from here. If you want use [other operation systems](https://www.raspberrypi.org/downloads/), it is same setup setups.

# Step 3 Write Image to Micro SD card

**Be aware, writing image to micro sd card will wipe all data on it**

Connect micro sd card to your mac through micro sd card reader.

![Picture](https://drive.google.com/uc?export=download&id=0ByuKQDQ-pFtiSnZ4VW91d1lWRXc)

Once you can see your sd card in `finder`, you need find out the `disk` in system.

Open a `terminal` and run `diskutil list`:  

![Picture](https://drive.google.com/uc?export=download&id=0ByuKQDQ-pFtiemZJXzdLZzFHM3c)

Find your sd card disk by its name. On my machine, the `/dev/disk2` in red rectangle is the `disk` in system and copy `disk2` only (not `/dev/disk2`) into clipboard.

Unmount the sd card from finder but do **not** unplug it.

![Picture](https://drive.google.com/uc?export=download&id=0ByuKQDQ-pFtib0MxZUR5QUJOc3M)

Now, we need flash the image downloaded to sd card. You may need unzip it if the downloaded file is zipped by double click the file in finder. Below is the command to use:

```
sudo dd bs=4M if=<path to downloaded .img file>  of=/dev/r<disk>
```

Replace the `path` and `<disk>` to your own one. 

![Pic](https://drive.google.com/uc?export=download&id=0ByuKQDQ-pFticTM3R3lna0FudzA)

# Step 4 Plugin and setup

Now insert the flashed micro sd card to Raspberrypi Zero W and connect everything and you are ready to go.

[Picture]


# General Problems

* Resource busy: You need unmount the partitions mounted before you could use `dd` to write the disk. See above.
* Permission denied: Make sure write protection on your sd card is open. Also use external SD card reader instead of Mac built-in one which could cause this issue.
* Operation not permitted: Make sure your device exists in system (check /dev/diskN)


