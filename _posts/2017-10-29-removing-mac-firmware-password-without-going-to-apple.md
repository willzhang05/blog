---
layout: post
title:  "Removing a Mac's Firmware Password Without Going to Apple"
date:   2017-10-29 00:00:00 -0400
preview: efirom.jpg
tags: mac efi firmware hack password apple rpi spi electronics
---

According to Apple, the only way to remove an unknown firmware password from a MacBook (2011 and later) is to take it to the Apple Store with the original proof-of-purchase. However, I've found that there is another way, which I've been successful with--it's essentially just modifying a couple bytes in the EFI ROM, which should be simple. What's not simple, however, is figuring out how to read and write to the EFI chip. In this post, I'll talk about the process that I figured out and what worked for me.



### The Official Method ###
Apple's method of resetting the firmware password is not reproducible, as Apple generates an SCBO file that unlocks the EFI using their private key. You can read more about this process [here](https://reverse.put.as/2016/06/25/apple-efi-firmware-passwords-and-the-scbo-myth). The problem with this system is that, if you are in the unfortunate situation of neither having the firmware unlock password nor the original proof of purchase, you have no other options. This is understandable from Apple's viewpoint, as it can deter theft; once the Mac is stolen, the thief cannot reimage the computer, making it useless. Many threads on the Internet show that this situation is often not a result of theft, however. For instance, one commenter on a thread I read explained that a family member had gifted the system to them, but no longer remembered the password nor had the receipt, leaving no options to reuse the computer.

### The Story ###
I was recently at another TJ student's house helping organize his basement full of computers and computer parts. I was volunteering for the non-profit he ran. The non-profit took donations of computers and refurbished them to be donated to families and other students without computer access at home. However, the student whose basement had been formerly used for the organization had graduated, so all the hardware had been moved to the new location. In the mess of computer parts, I found a 2012 MacBook Pro A1286 that was missing its battery, memory, and hard drive. The organization typically didn't bother with ordering specific parts for individual computers, so I bought it for $30, as it had sat there for a long time and would likely have been thrown away.

After I got it home, I found that it would power on intermittently, and that it had a firmware lock. I wasn't sure whether it was due to the 60W charger I was using rather than the 85W recommended, or whether it was due to the different A1278 battery I had temporarily rigged to power the laptop up. To find out which was the case, I took the risk and purchased a brand new OEM battery off of eBay for $48, as well as a hard drive retention bracket.

Once I got all the parts installed, the Mac powered on, but the problem of the firmware lock remained. As I searched for methods to remove the firmware lock, I saw the recommendation to bring the computer to the Apple Store over and over, and that there was no other way. 

There actually turns out to be an alternative, but I only found two sources online that described the process. One source was the [Rossmann Group Forum](https://www.rossmanngroup.com/boards/forum/board-repair-troubleshooting/2455-how-to-read-write-erase-apple-efi-spi-rom-with-raspberry-pi) and another from [ghostlyhaks.com](https://www.ghostlyhaks.com/blog/blog/hacking/18-how-to-bypass-apple-efi-firmware-lock).

However, the article on ghostlyhaks.com was somewhat dubious and provided inaccurate information, particularly with the schematic, but the general description of the steps was correct.

![The inaccurate schematic](https://www.ghostlyhaks.com/images/articles/diagram.png)

This schematic is not only somewhat confusing, it is incorrect. It has the resistors on precisely the wrong pins that they should be on; they should instead be on the select, clock, I/O and GND pins. Even then, they are not strictly necessary, they merely provide some protection from voltage fluctuations.

The post on the [Rossmann Group forum](https://www.rossmanngroup.com/boards/forum/board-repair-troubleshooting/2455-how-to-read-write-erase-apple-efi-spi-rom-with-raspberry-pi) did cite the latter source, but it actually had an accurate schematic!

### Tutorial

I've bored you enough with the story, let's get to the exciting part. I'll describe the method that worked for me; it may be somewhat different on your particular MacBook. I am also not responsible for any bricked Macs this may result in, **do this at your own risk with the possibility of a bricked Mac in mind**.

#### Stuff you'll need

- A way to connect to the Raspberry Pi -- I used SSH, but serial can work as well.
- Hex editor -- I recommend [HxD](https://mh-nexus.de/en/hxd/) if you're using Windows.
- Raspberry Pi -- I used a Pi 3 Model B with Raspbian 8.
- Flashrom -- This is a package in the Raspbian repositories, so you can install it with apt.


- Some jumper wires -- Many thanks to my friend Liam, who provided me with some.
- Breadboard -- Had this sitting around.
- A couple low resistance resistors -- I used 150Ω.
- A power supply capacitor -- A small ceramic capacitor will do, I used a 104μF.
- SOIC 8-pin clip -- This is probably the most important out of all the components, you'll want something quality that can make a solid connection with the pins of the EFI chip. I used a [Pomona 5250](https://www.amazon.com/CPT-063-Test-Clip-SOIC8-Pomona/dp/B00HHH65T4).

#### Setup

First, we'll need to verify that we can interface with the EFI flash chip at all. To get to the chip, you'll need to disassemble your MacBook to the point where you can see the top side of the logic board and identify the chip. On the A1286, the chip is located near the SD card reader and speaker. The chip will be fairly large and have eight pins.

![img](https://i.imgur.com/EDn5TwM.jpg)

The particular chip on my A1286 was a [Micron 25Q064A](https://www.micron.com/~/media/documents/products/data-sheet/nor-flash/serial-nor/n25q/n25q_64mb_1_8v_65nm.pdf).

First we'll need to set up the Raspberry Pi to function as an SPI programmer. To use it as an SPI programmer, we'll need to install flashrom from the Raspbian repositories.

{% highlight bash %}
$ apt install flashrom
{% endhighlight %}



![img](https://i.imgur.com/EAXwlfw.jpg)Next, follow the above schematic from the [Rossmann Group forum post](https://www.rossmanngroup.com/boards/forum/board-repair-troubleshooting/2455-how-to-read-write-erase-apple-efi-spi-rom-with-raspberry-pi) to connect the Raspberry Pi GPIO pins to the SOIC clip, then clip onto the chip on the logic board. As far as I know, the pinout should be identical for all Mac EFI flash chips, but be sure to check the specific datasheet for your chip and modify the wiring as needed.

![img](https://lh3.googleusercontent.com/-_vqWZZHRR1a17qWbvNhXLqJuDrwzPVHT5HbJ_Y_-DTKEbgL7SCF8G6oaZ3NBE7JobLtAyuHPkEiBAqOhRM7uemscv4LD-ZndKfpK9K5cYR2m3DgfFC6oMkqehojjlQBSbI8dZq0Si4PC6YbuRVYDNM7qeWfHa75RXpildmnDVeaAcR7qvjsD7bTbbqdWzeRimkWaCbK_icWHVlJwU5MfFB1iFx_0WNlo-9dVKq_BPKupvtUJzj4Dgd0CmdiTn5IfC4RDw5myr8WInlQQfmrkMg1Dj-RevsKepBf-m3dt8gGWP84FaBn7SWPiZiMsm6xw4GLA50Asrkk3p69DAqXnOjPMgzEyuLTTaoO3RG1_rrM7LGvNcwNv_5AY2JaL6gc2ijVDrkoNyGtOQ0vfXyP6K5SQuxewE7Sy4yY6wYShHCbxn-k5A6qtoU-MeT80B1wIOU6tTncRnCihwEKgomkX8rdo8N2G1ZS2Nt2TesAuzILwHcEMSwMNy9JC2_pAHPO6DlIwIzXtcKqUKKgKFoe39A7KN0GVOkatrUTrecJl1waubUbUa2FoBEX113Sd0mLQtkkP5uoCkp6fSO_i02o3tyGFq9glx4HZv0Gijfc3EwEiAZckJykshvWUMnykQlxrqfLJIqzvzPupkzOYJtlo0AlnxkU9ZNLt6Y=w1200-h900-no)

This is what my setup looked like.



{% highlight bash %}
$ modprobe spi_bcm2835
{% endhighlight %}

This will load the kernel module we need to do SPI programming on the Pi. Then, if you've wired everything correctly, check /dev for the SPI device and it will probably appear as /dev/spidev0.0. 

I was initially worried about the fact that my exact chip wasn't on the [flashrom supported devices list](https://www.flashrom.org/Supported_hardware), but it turns out that the N25Q064..3E is identical in pinout and uses the same 3.3V logic, so it can be safely selected in flashrom.

To read to a file read.bin from the chip, I used the following command.

{% highlight bash %}
$ flashrom -r read1.bin -c "N25Q064..3E" -V -p linux_spi:dev=/dev/spidev0.0
{% endhighlight %}

You'll probably want to make more than one read of the chip, just in case anything was corrupted. We can check that the reads are identical by checking the md5 checksum of the files.

{% highlight bash %}
$ md5sum read1.bin
b9d90db64f953cb5879f1a8cd100b233  read1.bin
$ md5sum read2.bin
b9d90db64f953cb5879f1a8cd100b233  read2.bin
{% endhighlight %}

Since the checksums are identical, we know that the files are identical too. As an extra precaution, I made two more reads and copied the files to another computer.

Now, with the files on another computer, I opened one of the files up in HxD, and searched for the ASCII string "\$SVS" without quotes. This string denotes the blocks of hex values with the firmware password. To get rid of these blocks, we just need to fill in the hex values with "FF" without quotes. You'll want to do this to each block starting with "\$SVS" in the file.

![img](https://i.imgur.com/zTQr3fV.png)

Once we're done, we can copy the modified file back to the Pi. I once again copied multiple times and checked the MD5 checksums, just in case.

Now on to the scary part: we can now erase the chip on the logic board.

{% highlight bash %}
$ flashrom -E -V -p linux_spi:dev=/dev/spidev0.0
{% endhighlight %}

And, with our modified file mod.bin, we can now write the modified firmware without the firmware password back onto the EFI flash chip.

{% highlight bash %}
$ flashrom -w mod.bin -V -p linux_spi:dev=/dev/spidev0.0 -c "N25Q064..3E"
{% endhighlight %}

Once the process completes, you can turn off the Raspberry Pi and put the MacBook back together. If you've done everything correctly, the MacBook should just power right up.

The firmware password may appear upon initial boot up, but don't worry! All you need to do is to reset the NVRAM using the key combination Command + Option + P + R as soon as you press the power button. This will clear the firmware password from memory, and you should now have no firmware password.

