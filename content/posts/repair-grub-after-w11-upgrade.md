---
title: "Repair GRUB after a messy Windows 11 upgrade"
date: 2021-12-24T20:16:45+01:00
draft: false
toc: true
images:
tags:
  - windows
  - linux
  - grub
---

So you have a dual boot with Windows 10 and you decided to upgrade to Windows 11 to enjoy all of its new [WSL](https://docs.microsoft.com/en-us/windows/wsl/install) functionalities or try the [WSA](https://docs.microsoft.com/en-us/windows/android/wsa/). Everything goes fine in the first time, you do everything necessary to please the Windows 11 system requirement checker, and you start the upgrade. Everything seems alright up until the moment you try to reboot your PC after Windows 11 finally finished its installation. But surprise !! You end up in GRUB Rescue de the GRUB recovery mode that start in case it is not able to mount your boot partition to then boot your system.

In all their wisdom Microsoft decided that if you went to the effort of going from Windows 10 to Windows 11 on a dual boot system, it's surely because you think Windows 11 is the superior OS and you want to ditch Linux, and then proceed to confuse your GRUB in that attempt.

I'm not really sure what exactly happened, but I'm pretty sure Windows 11 messed with my partition table and grub was then not able to find it back. So to solve the issue, we are gonna first boot back to Linux and then reinstall GRUB.

## Find the boot partition

When your computer boot in GRUB in those condition, you should be granted with a prompt looking like this

```shell
Error: unkown filesystem.
grub rescue>
```

(It happened last night at the time of writing this, so I'm not entirely confident at what was exactly displayed, but you should have something really similar.)

First thing you're going to want to do is print the list of available drives and partitions with the ls command. You should get an output like this:

```shell
grub rescue> ls
(hd0) (hd0,gpt5) (hd0,gpt4) (hd0,gpt3) (hd0,gpt2) (hd0,gpt1)
```

`(hdX)` represent a physical drive, while `(hdX,gptY)` or `(hdX,Y)` represent a partition. In our case, we then have 1 physical drive and 5 [GPT](https://en.wikipedia.org/wiki/GUID_Partition_Table) partitions.

Now we would like to know where our boot partition is so, first we will check each partition to find if there is a boot folder at the root. It's a bit tedious, but well, we've got to do what we've got to do.

So again, use the ls command on each partition to find that folder.

```shell
grub rescue> ls (hd0,gpt5)/
(hd0,gpt5) unknown filesystem
grub rescue> ls (hd0,gpt4)/
(hd0,gpt5) unknown filesystem
grub rescue> ls (hd0,gpt3)/
bin boot dev etc home lib lib64
```

Don't worry about the `unknown filesystem` errors, it only means it was not a Linux partition, so we can just ignore them.

Once you found a partition with a boot folder make sure it's the right partition, sometime some Dist like Ubuntu install the Linux kernel image on a separate partition and other store it on the same partition as your root usually. If your boot partition is separate, you should not have much on the partitions 4-5 folders (not a full Linux OS).

Next, you'll want to check in that boot folder if there is a folder called **Grub** :

```shell
grub rescue> ls (hd0,gpt3)/boot
grub initramfs-linux-fallback.img initramfs-linux.img intel-ucode.img vmlinuz-linux
```

If you do not find the grub folder, either you are on the wrong partition, in which case you'll just need to continue looking on other partitions for the boot folder. Or Windows 11 just nuked your install, and you'll probably need to use a tool like [boot-repair](https://sourceforge.net/p/boot-repair/home/fr/).

Let's just check if everything is in order in the grub folder, and then we'll move on to the next step :

```shell
grub rescue> ls (hd0,gpt3)/boot/grub
fonts  grub.cfg  grubenv  locale  themes  unicode.pf2  x86_64-efi
```

There you should at least have a folder with the name of your system architecture type, in my case it's `x86_64-efi`.

## Booting into Linux

Now that we know where our original GRUB configuration is located, we need to boot into Linux with it.

First we need to tell grub in which partition is located the files it needs to boot a system, to do that we will use the command set that is used to define an environment variable for GRUB.

```shell
grub rescue> set root=(hd0,gpt3)
```

Then we need to tell GRUB where to find the modules necessary, load its original configuration again with the comment set.

```shell
grub rescue> set prefix=(hd0,gpt3)/boot/grub
```

Just replace `(hd0,gpt3)/boot/grub` with the path to your grub folder.

Then to launch GRUB in its original configuration :

```shell
grub rescue> insmod normal
grub rescue> normal
```

From there, GRUB should be back the way it looked before. **But wait it's not finished !!** If you stop there on your next boot, you will be back on step one.

## Reinstall GRUB

For the final step, you'll need to boot into Linux and open a terminal. Next, you will need to mount your `efi` partition if you are on an efi system (which is probably the case if you upgraded to Windows 11). To do that, you first need to find the `efi` partition on your system:

```shell
> fdisk -l
Disk /dev/nvme0n1: 476.94 GiB, 512110190592 bytes, 1000215216 sectors
Disk model: BC711 NVMe SK hynix 512GB
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 0DC7E01E-9E0B-4C27-A86B-8F87A2043B1F

Device               Start        End   Sectors   Size Type
/dev/nvme0n1p1        2048    1050623   1048576   512M EFI System
/dev/nvme0n1p2     1050624    5244927   4194304     2G Linux filesystem
/dev/nvme0n1p3     5244928   47187967  41943040    20G Linux filesystem
/dev/nvme0n1p4    47187968  297259007 250071040 119.2G Microsoft basic data
/dev/nvme0n1p5   297259008  298844159   1585152   774M Windows recovery environment
/dev/nvme0n1p6   298846208  403703807 104857600    50G Linux filesystem
/dev/nvme0n1p7   403703808  420481023  16777216     8G Linux swap
/dev/nvme0n1p8   420481024  588251135 167770112    80G Linux filesystem
/dev/nvme0n1p9   588251136 1000148991 411897856 196.4G Linux filesystem
/dev/nvme0n1p10 1000148992 1000181759     32768    16M Microsoft reserved
/dev/nvme0n1p11 1000181760 1000214527     32768    16M BIOS boot
```

In the list of devices, there should be a partition marked `EFI System`. That's the one, note down the location of the device. We will now mount the partition, in my case it will be to /`boot/efi` :

```shell
root> mkdir /boot/efi
root> mount /dev/nvme0n1p1 /boot/efi
```

Now that everything is in place, we can proceed to reinstalling GRUB.

```shell
> grub-install --target=x86_64-efi --efi-directory=/boot/efi --boot-directory=/boot
```

You will probably need to change a few things in this command :

- `--target` :  your system Architecture Type in my case x86_64-efi,
- `--efi-directory` : tell grub where to install the bootloader which is the partition we just mounted, and finally
- `--boot-directory` : where your GRUB config is located so /boot.

And for the grand final :

```shell
root> grub-mkconfig -o /boot/grub/grub.cfg
```

If everything went fine, you should now have your GRUB functioning properly again. Assuming of course you had a problem and installation similar mine.

If you need any more assistance, I advise you to continue to look on the internet on websites like [unix.stackexchange.com](https://unix.stackexchange.com/) or the like.
