---
title: 'Linux Devices and Hardware'
date: 2022-08-06T09:26:38+08:00
draft: false

images: []
resources: # https://www.pexels.com/photo/close-up-of-working-257886/
  - name: 'featured-image'
    src: 'featured-image.jpg'

tags: ['linux', 'certification', 'comptia linux+']
categories: ['notes', 'tutorial']
---

# CompTIA Linux+ Exam Objective 1.1.3

{{< admonition info >}}
This article is my third note for [Shawn
Powers'](https://www.youtube.com/c/shawnp0wers) [tutorial
playlist](https://www.youtube.com/playlist?list=PL78ppT-_wOmuwT9idLvuoKOn6UYurFKCp)
on the [CompTIA Linux+ Exam](https://partners.comptia.org/certifications/linux).
The notes starts [here]({{< ref
"posts/the-linux-file-hierarchy-structure/index.md" >}}).
{{< /admonition >}}

## Block and character devices

Block and character devices are ways to get data in and out of a Linux system,
i.e. how Linux communicates with things like a hard drive (which is a block
device) and a serial port (which is a character device). The diffference between
block and characters devices is that the former use buffers and caches to
transfer large amounts of data (e.g., storage devices) while the latter
literally transfer streams of characters (e.g., a stream of null characters)
without buffering. To make an analogy, if water is data then block devices are
like pails where you can store water first (buffering) before transfering it
while character devices are like fire hose (to copy Shawn Powers' analogy) which
transfers water in a gushing, continuous stream.

## Where devices live in Linux

As specified in the [Linux filesystem
standard](https://refspecs.linuxfoundation.org/fhs.shtml) devices are located in
`/dev`. Listing out the files under my `/dev` folder shows something like this

```bash
$ ls -F /dev
acpi_thermal_rel  disk/        hidraw2	     mei0     nvme0n1	 rfkill    tty	  tty18  tty28	tty38  tty48  tty58  ttyS1   ttyS2   ttyS3    uinput   vcs6   vcsu1	   vhost-vsock
autofs		      dma_heap/    hpet	         mem      nvme0n1p1  rtc@	   tty0   tty19  tty29	tty39  tty49  tty59  ttyS10  ttyS20  ttyS30   urandom  vcs7   vcsu2	   video0
block/		      dri/	       hugepages/    mqueue/  nvme0n1p2  rtc0	   tty1   tty2	 tty3	tty4   tty5   tty6   ttyS11  ttyS21  ttyS31   usb/     vcsa   vcsu3	   video1
btrfs-control	  drm_dp_aux0  hwrng	     mtd0     nvme0n1p3  shm/	   tty10  tty20  tty30	tty40  tty50  tty60  ttyS12  ttyS22  ttyS4    userio   vcsa1  vcsu4	   watchdog
bus/		      fb0	       input/	     mtd0ro   nvram	     snapshot  tty11  tty21  tty31	tty41  tty51  tty61  ttyS13  ttyS23  ttyS5    v4l/     vcsa2  vcsu5	   watchdog0
char/		      fd@	       kmsg	         mtd1     port	     snd/	   tty12  tty22  tty32	tty42  tty52  tty62  ttyS14  ttyS24  ttyS6    vcs      vcsa3  vcsu6	   wmi/
console		      full	       kvm	         mtd1ro   ppp	     stderr@   tty13  tty23  tty33	tty43  tty53  tty63  ttyS15  ttyS25  ttyS7    vcs1     vcsa4  vcsu7	   zero
core@		      fuse	       log@	         net/     psaux	     stdin@    tty14  tty24  tty34	tty44  tty54  tty7   ttyS16  ttyS26  ttyS8    vcs2     vcsa5  vfio/
cpu/		      gpiochip0    loop-control  ng0n1    ptmx	     stdout@   tty15  tty25  tty35	tty45  tty55  tty8   ttyS17  ttyS27  ttyS9    vcs3     vcsa6  vga_arbiter
cpu_dma_latency   hidraw0      mapper/	     null     pts/	     tpm0	   tty16  tty26  tty36	tty46  tty56  tty9   ttyS18  ttyS28  udmabuf  vcs4     vcsa7  vhci
cuse		      hidraw1      media0	     nvme0    random	 tpmrm0    tty17  tty27  tty37	tty47  tty57  ttyS0  ttyS19  ttyS29  uhid     vcs5     vcsu   vhost-net
```

Some of the files I recognize from the list are `nvme0n1` which is my SSD,
`nvme0n1p1`, `nvme0n1p1`, and `nvme0n1p3` are partitions of the SSD, are
`stdin`, `stderr`, and `stderr` are three data streams which we can use for
piping and [redirection](<https://en.wikipedia.org/wiki/Redirection_(computing)>).

{{< admonition tip >}}
To know which are devices are block and character devices in `/dev` you can use
the long listing format of `ls` like so

```bash
$ ls -l /dev
...
crw-------   1 root root   239,     0 Aug  8 16:25 nvme0
brw-rw----   1 root disk   259,     0 Aug  8 16:25 nvme0n1
...
```

The first character of the list is the indicator -- `c` is for character devices
and `b` is for block devices.
{{< /admonition >}}

# Some important character devices

## `/dev/zero` and `/dev/null`

Two character devices that are very important and used a lot that they deserve
to be mentioned are `/dev/zero` and `/dev/null`.

`/dev/zero` is simply like a source of the null character `\0` (not to be
confused with the character for the number 0). One use of `/dev/zero` for
example is to erase a disk drive using the `dd` command

```bash
dd if=/dev/zero of=/dev/sda count=100000
```

In the example above, we used `/dev/zero` as the input file for `dd` to zero out
the disk drive `/dev/sda`.

On the other hand, `/dev/null`, also known as the **bitbucket**, is a write-only
filesystem where you write data to that you want to be lost forever. If data is
to light then `/dev/null` is to black hole. It is commonly use as the
redirection destination of data streams, for example

```bash
$ cat nonexisting_file.txt
cat: nonexisting.file: No such file or directory

$ cat nonexisting_file.txt 2>/dev/null
$
```

Trying to display the contents of a file that does not exist is an error but
when redirecting `stderr` to `/dev/null`shows no error.

## `/dev/urandom` and `/dev/random`

Another two character devices that deserve our attention are `/dev/urandom` and
`/dev/random`. Both of them provide stream of random characters. The difference
between `/dev/random` and `/dev/urandom` is that when you query `/dev/random` it
will not return anything until it has determined that enough entropy has been
built up in the system while `/dev/urandom` always returns immediately
regardless of the amount of entropy in the system.

# Knowing your hardware

In this section, we'll familiarize ourselves with some tools to get hard
information in your system.

## `lspci`

The `lspci` command shows a list [PCI](http://www.pcisig.com/specifications)
devices. For instance if I use `lspci` in my computer it will show something
like

```bash
$ lspci
0000:00:00.0 Host bridge: Intel Corporation Device 9a04 (rev 01)
0000:00:02.0 VGA compatible controller: Intel Corporation Device 9a78 (rev 01)
0000:00:04.0 Signal processing controller: Intel Corporation TigerLake-LP Dynamic Tuning Processor Participant (rev 01)
0000:00:0a.0 Signal processing controller: Intel Corporation Tigerlake Telemetry Aggregator Driver (rev 01)
0000:00:0e.0 RAID bus controller: Intel Corporation Volume Management Device NVMe RAID Controller
...
```

## `lsusb`

The `lsusb` command lists out USB devices.

```bash
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 002: ID 0c45:671e Microdia Integrated_Webcam_HD
Bus 001 Device 003: ID 8087:0aaa Intel Corp. Bluetooth 9460/9560 Jefferson Peak (JfP)
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

## `dmidecode`

Its man page says, `dmidecode` is a tool for dumping a computer's DMI table
contents in a human-readable format. When I ran the command it indeed dumped so
much information that it could have filled up a few pages of paper. However, we
can specify using flags what specific information we want. For example to get
processor information we run `dmidecode -t processor` or to get memory
information `dmidecode -t memory`. Running the last command in my system displayed

```bash
$ sudo dmidecode -t memory
# dmidecode 3.4
Getting SMBIOS data from sysfs.
SMBIOS 3.2 present.

Handle 0x1000, DMI type 16, 23 bytes
Physical Memory Array
	Location: System Board Or Motherboard
	Use: System Memory
	Error Correction Type: None
	Maximum Capacity: 128 GB
	Error Information Handle: Not Provided
	Number Of Devices: 8
```

{{< admonition note >}}
`dmidecode` requires superuser privileges to run.
{{< /admonition >}}

Using `dmidecode` and the tools mentioned (and more not mentioned) you can
extract information about your system and if you combine them with other tools
you can use them in scripting to achieve/automate a lot of tasks and things.

## Reference

- [Linux Devices & Hardware (Linux+ Objective
  1.1.3)](https://www.youtube.com/watch?v=IK5ma-2jeWk)
