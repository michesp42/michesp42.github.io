---
title: 'The Linux Boot Process'
date: 2022-08-06T09:26:14+08:00
draft: false

images: []
resources: # https://unsplash.com/photos/hRI8f-2WyDw
  - name: 'featured-image'
    src: 'featured-image.jpg'

tags: ['linux', 'certification', 'comptia linux+']
categories: ['notes', 'tutorial']
---

# CompTIA Linux+ Exam Objective 1.1.2

{{< admonition info >}}
This article is my second note for [Shawn
Powers'](https://www.youtube.com/c/shawnp0wers) [tutorial
playlist](https://www.youtube.com/playlist?list=PL78ppT-_wOmuwT9idLvuoKOn6UYurFKCp)
on the [CompTIA Linux+ Exam](https://partners.comptia.org/certifications/linux).
The notes starts [here]({{< ref
"posts/the-linux-file-hierarchy-structure/index.md" >}}).
{{< /admonition >}}

## UEFI and BIOS

[UEFI](https://en.wikipedia.org/wiki/UEFI) and
[BIOS](https://en.wikipedia.org/wiki/BIOS) are both firmwares on the hardware of
the computer that allows the operating system (and other programs) to interface
with the motherboard by providing runtime services during the booting process.
UEFI stands for Unified Extensible Firmware interface while BIOS means Basic
Input/Output System. UEFI was designed to overcome the shortcomings of BIOS
especially for more modern hardware. The following summarizes the differences
between UEFI and BIOS:

| UEFI                                             | BIOS                                  |
|--------------------------------------------------|---------------------------------------|
| newer                                            | old/legacy                            |
| uses hard drive partition to store configuration | configured and stored in mother board |
| supports drive sizes up to 9 zettabytes          | supports only up to 2.2 TB drives     |
| provides faster boot time than BIOS              | slower boot time than UEFI            |
| offers secure boot                               | less secure than UEFI                 |
| runs on 32- or 64-bit mode                       | runs on 16-bit mode                   |
| offers GUI (with mouse navigation) interface     | keyboard only interface               |


## Boot sources

The following are sources from which Linux can boot:

- hard disk
- usb devices
- cd/dvd
- PXE and iPXE

The first three options are the most common boot sources, for instance, booting
from the hard drive from which the OS is installed or a bootable usb stick.
Pre-boot eXecution Environment
([PXE](https://en.wikipedia.org/wiki/Preboot_Execution_Environment)) and iPXE
allows you to boot from the network.

## GRUB2

The GRand Unified Bootloader ([GRUB](https://en.wikipedia.org/wiki/GNU_GRUB)) is
a _boot loader_ that is the first software program that runs when a computer
starts. From a casual user standpoint, its purpose is it lets you select where
you want to boot to. The GRUB bootloader allows you to boot off the following
sources:

- ISO file
- memtest
- other operating systems

GRUB2 is the latest iteration of GRUB implementation and is the default version
used.

## The Boot Process

The following diagram illustrates a normal Linux boot process:

```goat
              .------.   .---------.    .-------.
             | initrd | | initramfs |  | modules |
              '----+-'   '-+-------'    '---+---'
                   |       |                |
                   v       v                v
 .------.         .---------.          .--------.
 | UEFI |         | vmlinux |          |  full  |
 |  or  +-------->|   or    +--------->| kernel |
 | BIOS |         | vmlinuz |          '--------'
 '------'         '---------'
```

Starting with UEFI or BIOS, we go to the GRUB bootloader (assuming we boot off
the hard drive normally). GRUB then loads `vmlinux`. The
[vmlinux](https://en.wikipedia.org/wiki/Vmlinux) file is a linked executable
that contains the Linux kernel. What you'll typically find however in `/boot` is
its the compressed version `vmlinuz` which is the case with my computer

```bash
$ ls -F /boot
EFI/   initramfs-linux-fallback.img*  intel-ucode.img*
grub/  initramfs-linux.img*	      vmlinuz-linux*
```

`Vmlinux` is a generic kernel compiled for a multitude of hardware. Because the
kernel modules needed for a system is dependent on the specific hardware it has,
we need a way to load the kernel modules that a particular system hardware
needs. That is where [initrd](https://en.wikipedia.org/wiki/Initial_ramdisk)
comes in. `Initrd` is a scheme to load just enough software like kernel modules
to recognize the system hardware.`Initramfs`, an alternative scheme to `initrd`
is a filesystem takes care of mounting important file systems by also loading
the proper kernel modules and drivers for the system hardware.

## Configuring GRUB

The configuration file for grub is located in `/boot/grub/grub.cfg` but the file
you edit is `/etc/default/grub`. For example, during installation of my system I
edited the settings for `GRUB_GFXMODE` to `1920x1080` screen resolution and set
the `GRUB_TIMEOUT` to 3 so that the GRUB menu disappears faster.

After you edit `/etc/default/grub`, you can then run a command to create the
grub configuration which is `update-grub` or `update-grub2` for Ubuntu-based
distributions, `grub2-mkconfig` for RHEL-based distributions, and simply
`grub-mkconfig` for Arch-based distributions.

{{< admonition info >}}
Ubuntu's `update-grub`/`update-grub2` just uses `grub-mkconfig` or
`grub2-mkconfig` behind the scenes. It should also be noted that there is also a
command called [grubby](https://linux.die.net/man/8/grubby) for configuring GRUB
in RHEL-based distributions.
{{< /admonition >}}

We use command mentioned like so:

```bash
# for Ubuntu-based distros
update-grub

# for RHEL-based distros
grub2-mkconfig - /boot/grub/grub.cfg
```

## References and Further Reading

- [The Linux Boot Process (Linux+ Objective 1.1.2)](https://www.youtube.com/watch?v=esH6GUjVa8Y&t=261s)
- [UEFI vs BIOS: What's the Difference?](https://www.freecodecamp.org/news/uefi-vs-bios/)
