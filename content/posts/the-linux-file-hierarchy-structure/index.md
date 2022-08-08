---
title: 'The Linux File Hierarchy Structure'
date: 2022-08-05T16:01:24+08:00
draft: false

images: []
resources: # https://unsplash.com/photos/8eoRM2zHkHc
  - name: 'featured-image'
    src: 'featured-image.jpg'

tags: ['linux']
categories: ['notes', 'tutorial']
---

# CompTIA Linux+ Exam Objective 1.1.1

{{< admonition info >}}
This article is the first of my notes for [Shawn
Powers'](https://www.youtube.com/c/shawnp0wers) work-in-progress [tutorial
playlist](https://www.youtube.com/playlist?list=PL78ppT-_wOmuwT9idLvuoKOn6UYurFKCp)
on the [CompTIA Linux+ Exam](https://partners.comptia.org/certifications/linux).
I have already seen his [Linux Server
Course](https://www.youtube.com/watch?v=WMy3OzvBWc0) which I think covers
roughly the same topics as his Linux+ playlist will but unfortunately I lost all
my hand-written notes for the Linux server course and admittedly I have already
forgotten the nitty-gritty of the topics he discussed in the course. And so I'll
be reviewing the topics gradually and take notes as he uploads the videos for
the Linux+ series.
{{< /admonition >}}

## The Linux Filesystem Standard

The abstract of the [official
documentation](https://refspecs.linuxfoundation.org/fhs.shtml) for the Linux
Filesystem Standard (LFS) says:

{{< admonition abstract >}}
This standard consists of a set of requirements and guidelines for file and
directory placement under UNIX-like operating systems. The guidelines are
intended to support interoperability of applications, system administration
tools, development tools, and scripts as well as greater uniformity of
documentation for these systems.
{{< /admonition >}}

Simply said, the LFS specifies where certain files go in the file hierarchy of a
[UNIX-like](https://en.wikipedia.org/wiki/Unix-like) operating system like
Linux. Having a standard like this is important especially since, as the common
saying goes, [everything in Linux is a
file](https://en.wikipedia.org/wiki/Everything_is_a_file). This is not an
exaggeration -- everything in Linux is indeed a file; a plain old normal file is
a file, a directory/folder is a file, audio is a file, video is a file, even
network communication is a file, even hard drives are represented as files and
so on. This standard makes filesystems across different Linux installation
relatively uniform and so easier to manage for system administrators and even
casual users like me who like to hack around the command line.

If I run the `tree` command at the root level `/` directory of my Linux machine it
will show something like this:

```bash
tree -d -L 1 /
/
├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── mnt
├── opt
├── proc
├── root
├── sbin
├── srv
├── sys
├── tmp
├── usr
└── var

16 directories
```

These are the directories under the root level `/` directory specified in the LFS.
Now, let's go over the purpose of each of them.

## `/home` and `/root`

The `/home` directory contains the home directories of the users. For example, a
user called `foo` will have its dedicated `/home/foo` directory. Found in
`/home/foo` are all the files user `foo` has like documents, images, media
files, etc.

Meanwhile, the root user has its own `/root` home directory which is separate
from all the ordinary users of the system. Having `/root` separate from `/home`
is quite handy especially in case you want to be in [single-user
mode](https://en.wikipedia.org/wiki/Single-user_mode) the root user will still
have its configurations.

## `/boot`

In the `/boot` directory are static files that are needed at sytem boot. The `/boot`
directory of my machine has this files:

```bash
$ ls -F /boot
EFI/   initramfs-linux-fallback.img*  intel-ucode.img*
grub/  initramfs-linux.img*           vmlinuz-linux*
```

{{< admonition info >}}
The `-F` flag of `ls` classifies the files it lists using certain markers (one
of `*/=>@|`). In the listing above, files appended with `/` are directories and
those appended with `*` are executables.
{{< /admonition >}}

Here is a brief rundown of the files under my `/boot` directory:
- `/boot/EFI` contains files for [UEFI](https://en.wikipedia.org/wiki/UEFI)
- inside `/boot/grub` are files for the
[GRUB](https://en.wikipedia.org/wiki/GNU_GRUB) bootloader
- the [vmlinuz-linux](https://en.wikipedia.org/wiki/Vmlinux) is a (compressed?)
statically linked executable file that contains the Linux kernel
- the `initramfs` image (along with the kernel) is loaded by the bootloader at
  boot to start the kernel.
- `intel-ucode.img` is the [microcode](https://en.wikipedia.org/wiki/Microcode)
image provided for Intel CPus.

## `/etc`

The `/etc` (sometimes called _et cetera_ or read as _etsy_) directory houses
(text) configuration files. These files must be static and non-executable.

These are the content of my `/etc` directory (eliding other files to save
space):

```bash
$ ls -F /etc
adjtime               gtk-2.0/          mkinitcpio.d/     rsyncd.conf
alsa/                 gtk-3.0/          modprobe.d/       sasl2/
anacrontab            healthd.conf      modules-load.d/   securetty
arch-release          host.conf         monerod.conf      security/
at.deny               hostname          mpd.conf          sensors3.conf
audit/                hosts             mpv/              sensors.d/
avahi/                i3/               mtab@             services
avrdude.conf          i3status.conf     mtools.conf       shadow
bash.bash_logout      ImageMagick-7/    multipath/        shadow-
bash.bashrc           initcpio/         named.conf        shells
bash_completion.d/    inputrc           nanorc.pacnew     skel/
```

Some of the configuration files that I recognize are

- `i3/` contains configurations for the [i3](https//i3wm.org/) window manager
- `mpd.conf` is the configuration file for the music player daemon
  ([MPD](https//www.musicpd.org/))
- `ssh/` contains secure shell ([SSH](https//en.wikipedia.org/wiki/Secure_Shell))
  configuration
- `skel/` contains default bash configurations (in my system at least) that
  will be copied for every users created

## `/opt`

The `/opt` directory is reserved for add-on packages. Listing out my `/opt` directory shows

```bash
$ ls -F /opt
/opt
├── appimages
│   └── Sioyek.AppImage
```

[Sioyek](https://github.com/ahrm/sioyek) PDF viewer is an
[appimage](https://docs.appimage.org/introduction/index.html) package I
installed from the [AUR](https://aur.archlinux.org/), i.e. not from the official
Arch repositories, hence it is installed in `/opt`.

## `/media` and `/mnt`

The mount point for removable media devices such as floppy disks and cdrom is
`/media`. While temporarily mounted filesystems are mounted in `/mnt`. For
example, if you have a hard drive attached as `/dev/sda` you want to copy files
from. You can make a directory say `/mnt/hddmnt ` and mount the hard drive in
that directory like so `mnt /dev/sda1 /mnt/hddmnt`. Now you can access
(temporarily or as long as the hard drive is mounted) the contents of your hard
drive in `/mnt/hddmnt` as if it were a normal directory in your filesystem.

## `/tmp`

Temporary files that are usually deleted on reboot are stored in `/tmp`. For
example, the cached files (probably produced when I ran the command `hugo serve`) for [Hugo](https://gohugo.io) is stored in `/tmp/hugo_cache`.

## `/bin`, `/sbin`, and `/lib`

Essential system binaries and other root-only commands are stored in `/sbin` (or
`/usr/sbin` and `/usr/local/sbin`). For example, as specified in the official
documentation, the `shutdown` command (or a symbolic link to it) should be found
in `/sbin`. Essential binaries that may be used by root and users are stored in
`/bin`. For example, the binary for the shell should be found in `/bin/sh` (
which I symlinked in my computer to `/bin/dash`) since both the root and users
needs to run the shell.

The `/lib` directory contains shared library files needed at boot and required
to run the commands stored in `/sbin` and `/bin`.

## `/dev`, `/proc`, and `/sys`

Hardware represented as files (such as hard drive and com ports) are stored in
`/dev`. For example my primary SSD is represented as `/dev/nvme0n1`. Process and
kernel information generated on the fly are located in `/proc`. Listing out my
`/proc` directory shows:

```bash
$ ls -F /proc
1/	     18/	  356744/  444/     60/      71/      89/	      kpagecount
101/	 180/	  358644/  445/     614345/  710057/  9/	      kpageflags
10130/	 181/	  358744/  446/     614359/  711096/  94/	      latency_stats
102/	 182/	  358773/  447/     62/      711843/  95/	      loadavg
102897/  183/	  358836/  448/     63/      713/     acpi/	      locks
102900/  195/	  359154/  449/     64/      714102/  asound/	  meminfo
102904/  196/	  36/	   45/	    647227/  715351/  bootconfig  misc
```

The numbers are process identifiers
([PID](https://en.wikipedia.org/wiki/Process_identifier)) generated on the fly
by their respective processes. The command `acpi` shows battery and other ACPI
information from the `/proc/acpi` (or `/sys` filesystem) that is again
generated on the fly depending on the battery status.

The `/sys` directory contains information about devices, drivers, kernel and
other system devices. It overlaps with respect to functionality with the `/proc`
directory and was in fact created to declutter and move over some files from
`/proc`. For example, you may also get battery information from
`/sys/class/power_supply/BAT0/`.

## `/var` and `/var/tmp`

Files that change or read and write a lot over time are stored in `/var`. For
example, log files are stored in `/var/log`. A noteworthy subdirectory of
`/var` is `/var/tmp` where you can store temporary files that are not deleted
on reboot in contrast with `/tmp`.

## `/usr`

Files that are not necessary for single-user mode are stored in `/usr`. Most
command binaries are stored in `/usr/bin`. Libraries for most command are
stored in `/usr/lib`. Files and binaries specific to the local Linux system is
located in `/usr/local` and `/usr/local/bin`, respectively.

## References and Further Reading

- [Why Linux Puts Files ALL OVER?!?(Linux+ Objective
  1.1.1)](https://www.youtube.com/watch?v=tZZinDVu0J8)
- [The Linux Filesystem Standard](https://refspecs.linuxfoundation.org/fhs.shtml)
