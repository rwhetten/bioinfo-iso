bioinfo-iso
===========
### Background
This repo stores an iso file for a bootable Linux USB system with pre-installed bioinformatics tools, with a particular focus on software for analysis of massively-parallel DNA sequencing data (e.g. data from Illumina, IonTorrent, or SOLiD platforms). This system was developed for teaching purposes, so that students learning Linux and command-line data analysis would have access to a Linux system without any requirement for access to remote systems or permanent installation of a new OS on a local computer. The bootable USB system is 'persistent', meaning that changes to installed software or modification of files during the course of one session are saved when the system shuts down, and are present the next time the system is booted.

The current version is based on 64-bit Lubuntu 14.04 LTS, to minimize operating system overhead and leave as much of the system resources as possible available for data analysis. Hardware requirements for installation are minimal - a 64-bit-capable processor is required, but no particular amount of RAM is absolutely required, and hard drive space is not required at all. That being said, RAM is the limiting factor for many sequence data analysis programs, and more is better, but 8 Gb is enough to run the exercises developed for use with this OS package.

#### Two Important Notes
  1. The custom .iso was produced on a system running Ubuntu 12.04 LTS, using the Ubuntu Customization Kit (uck) package. The version of the uck package installed from the repository in summer 2013 contained a bug that produces an error message about "Failed to copy resolv.conf, error=1" when trying to open a liveCD .iso file.
To fix this, a line in the remaster-live-cd.sh script file in /usr/lib/uck/ was changed from
    `cp -f /etc/resolv.conf "$REMASTER_DIR/etc/resolv.conf" || (some other stuff)`
to 
    `cp -d /etc/resolv.conf "$REMASTER_DIR/etc/resolv.conf" || (the same other stuff)`  This modification was based on information at [Ubuntu Forums thread 2073251](http://ubuntuforums.org/showthread.php?t=2073251),
[Launchpad bug 946480](https://bugs.launchpad.net/uck/+bug/946480/comments/10), and [Launchpad bug 1000244](https://bugs.launchpad.net/ubuntu/+source/resolvconf/+bug/1000244/comments/82). After booting the new system for the first time, create a link from /run/resolvconf/resolv.conf to /etc/resolv.conf to fix the DNS resolution problem created by this change, by executing the following at a terminal command line:
`sudo ln -s /run/resolvconf/resolv.conf /etc/resolv.conf`

  2. The Ubiquity package has been removed from this .iso file, to reduce the risk of having a student accidently install the Lubuntu 14.04 OS from the live USB to the local hard drive of a computer booted from the USB system. Those who want the ability to install the system from the live USB can install the Ubiquity package from the Ubuntu repository using
    `sudo apt-get install ubiquity`
    
### Creating a bootable USB drive
One useful way to create a live USB system with a downloaded copy of this .iso file is to create three partitions on a flash drive, using GParted or another partition editor capable of creating multiple partitions on a removable drive. A GParted live CD system can be downloaded from [GParted.org](http://gparted.org/livecd.php) and used to boot a Windows computer into a lightweight Linux system capable of creating the appropriate partition types. The .iso file should be saved to a different flash drive than the one where the live USB system will be created, because the process of partitioning the flash drive for the live USB system will destroy any files on the drive.

An 8-Gb flash drive provides enough space for a 3-Gb fat32 partition at the beginning of the disk (which will be visible to Windows systems), a 1-Gb fat32 partition where the OS will be installed, and a ~4-Gb ext3 partition named casper-rw which will be available to the live USB system to save files modified during live USB sessions. The 3-GB Windows-accessible partition can be used to store files created during a Linux live USB session for later access from a Windows computer; this can be useful for saving results of analyses and summaries of exercises completed.

A larger flash drive allows these partition sizes to be increased. The second, smaller fat32 partition, where the OS is installed, does not need to be much larger than 1 Gb, but the size of the Windows-accessible first fat32 partition and the third Linux ext3 partition can both be increased to provide additional storage space as desired.

Be sure that the boot flag is set *only* on the second fat32 partition - if it is not, the system will not boot, and if either of the other two partitions has the boot flag set, it will create problems.

There are several tools available to create a bootable USB from the .iso file, once the appropriate partition structure has been created. Startup Disk Creator is a Linux package available for Ubuntu systems; [Linux Live USB](http://www.linuxliveusb.com/) is a Windows program that can create bootable USB drives on Windows systems, although I have not tested its ability to install from an .iso file onto the second partition in the partitioning scheme outlined above. A command-line option available using the GParted Live CD system should be available, using the 'dd' command to copy the raw .iso file to the appropriate partition on the USB drive.

Once the Linux OS has been installed from the .iso file onto the USB drive, there is one final modification that expedites the boot process. The default boot process for a live CD or live USB sytem offers several choices, and waits until the user chooses one, before moving on to complete booting. To eliminate that step and simply boot directly into a live Linux session, one must modify the 'syslinux.cfg' file in the 'syslinux' directory of the installed system (in the second partition of the flash drive). 

This modification must be done from a Linux system, because, as noted above, Windows will not see the second or third partitions of the flash drive, but it cannot be done from a Linux system booted from the flash drive. The GParted Live CD system provides the nano text editor, which would be one way to modify the 'syslinux.cfg' file. The modification to make is to comment out (put a # character at the beginning of) all existing lines of the 'syslinux.cfg' file, and add the following lines:
```
default live
label live
  say Booting a live USB bioinformatics session...
  kernel /casper/vmlinuz.efi  
  append  file=/cdrom/preseed/lubuntu.seed boot=casper persistent initrd=/casper/initrd.lz quiet splash noprompt --


```
