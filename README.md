bioinfo-iso
===========
### Background
This repo stores an iso file for a bootable Linux USB system with pre-installed bioinformatics tools, with a particular focus on software for analysis of massively-parallel DNA sequencing data (e.g. data from Illumina, IonTorrent, or SOLiD platforms). This system was developed for teaching purposes, so that students learning Linux and command-line data analysis would have access to a Linux system without any requirement for access to remote systems or permanent installation of a new OS on a local computer. The bootable USB system is 'persistent', meaning that changes to installed software or modification of files during the course of one session are saved when the system shuts down, and are present the next time the system is booted.

The current version is based on 64-bit Lubuntu 14.04 LTS, to minimize operating system overhead and leave as much of the system resources as possible available for data analysis. Hardware requirements for installation are minimal - a 64-bit-capable processor is required, but no particular amount of RAM is absolutely required, and hard drive space is not required at all. That being said, RAM is the limiting factor for many sequence data analysis programs, and more is better, but 8 Gb is enough to run the exercises developed for use with this OS package.

Clicking on the Download ZIP link on the right side of the page downloads the GPL2 license and this readme file. To download the .iso file, click on the Release tab near the top of the page, then scroll to the bottom of the Releases page and click the green download button.

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
    
### Creating a bootable USB drive with the bioinfo-iso Linux system
#### The easiest way
Download the .iso file from the release tab (above), and save it on your Windows machine. Find a program capable of creating a bootable USB drive from ISO files - one example is [LinuxLive USB Creator](http://linuxliveusb.com), but there are others. Download and install the live USB creator program, and start it. Using LinuxLive USB Creator as an example, it will ask which USB key you want to use. Plug in a USB drive (4 Gb is about the minimum useful size if you want to keep example datasets on it; 8 Gb is good and 16 Gb is better), and select it. The next step is to choose which Linux system to install. LinuxLive USB Creator gives you the option of installing from a downloaded .iso file, or going to the web to download a standard distribution .iso file. Choose the ISO file option, and navigate to the location of the downloaded bioinfo-iso file from this site. You can format the USB drive if desired, and choose whether to have the installed operating system folders 'hidden' from view in Windows - this is a good idea, because it reduces the chance of accidently changing them. LinuxLive USB Creator also offers the option of being able to run Linux from within Windows - unclick this option. It requires you to have administrator rights on the computer where you want to run Linux, and also involves downloading and installing a portable version of VirtualBox on the USB drive, which takes up more space and increases the computational overhead relative to simply booting the system into Linux. Depending on the space available on the USB drive, you can choose to make up to 2 Gb available to the Linux system as 'persistent' space - this is space available to the Linux system to save any changes made while the system is in use. Allocate as much space as possible to this, because it is very helpful to have additional space available to store datasets and results of analyses in the Linux system. 

#### A less easy but more flexible way
A flexible but more complex way to create a live USB system with a downloaded copy of this .iso file is to create three partitions on a flash drive, using GParted or another partition editor capable of creating multiple partitions on a removable drive. A GParted live CD system can be downloaded from [GParted.org](http://gparted.org/livecd.php), burned to a CD, and used to boot a Windows computer into a lightweight Linux system capable of creating the appropriate partition types on an empty USB drive. The .iso file should be saved to a different flash drive than the one where the live USB system will be created, because the process of partitioning the flash drive for installation of the live USB system will destroy any files on the drive.
##### Note
If GParted shows a 1 Mb sector at the end of the flash drive that cannot be allocated to a partition, your drive may have a GUID Partition Table (GPT) rather than a Master Boot Record (MBR). GPT has advantages for managing large hard drives, but creating a bootable USB drive requires the drive to have an MBR, so you will either need a different flash drive or you will have to use gdisk (a program provided on the GParted Live cd) to convert the drive from GPT to MBR. See [Stackexchange 61142](http://unix.stackexchange.com/questions/61142/remove-gpt-default-back-to-mbr) for more details, but in essence, start the gdisk program at the command prompt, type the name of the device that corresponds to your flash drive (eg /dev/sdb - _BE VERY CAREFUL YOU TYPE THE RIGHT DEVICE!_), then type r to enter the recovery menu, and g to convert the device from GPT to MBR. Type w to write the changes to the flash drive, then y to finalize changes and exit. 

An 8-Gb flash drive provides enough space for a 3-Gb fat32 partition at the beginning of the disk (which will be visible to Windows systems), a 1-Gb fat32 partition where the OS will be installed, and a ~4-Gb ext3 partition named casper-rw which will be available to the live USB system to save files modified during live USB sessions. The 3-GB Windows-accessible partition can be used to store files created during a Linux live USB session for later access from a Windows computer; this can be useful for saving results of analyses and summaries of exercises completed.

A larger flash drive allows these partition sizes to be increased. The second, smaller fat32 partition, where the OS is installed, does not need to be much larger than 1 Gb, but the size of the Windows-accessible first fat32 partition and the third Linux ext3 partition can both be increased to provide additional storage space as desired.

Be sure that the boot flag is set *only* on the second fat32 partition - if it is not, the system will not boot, and if either of the other two partitions has the boot flag set, it will create problems.

There are several tools available to create a bootable USB from the .iso file, once the appropriate partition structure has been created. Startup Disk Creator is a Linux package available for Ubuntu systems; or a command-line option available using the GParted Live CD system may work, using the 'dd' command to copy the raw .iso file to the appropriate partition on the USB drive.

##### Final optimization
Once the Linux OS has been installed from the .iso file onto the USB drive, there is one final modification that expedites the boot process. The default boot process for a live CD or live USB sytem offers several choices, and waits until the user chooses one, before moving on to complete booting. To eliminate that step and simply boot directly into a live Linux session, one must modify the 'syslinux.cfg' file in the 'syslinux' directory of the installed system (in the second partition of the flash drive). 

This modification must be done from a Linux system, because, as noted above, Windows will not see the second or third partitions of the flash drive, but it cannot be done from a Linux system booted from the flash drive. The GParted Live CD system provides the nano text editor, which would be one way to modify the 'syslinux.cfg' file. The modification to make is to comment out (put a # character at the beginning of) all existing lines of the 'syslinux.cfg' file, and add the following lines:
```
default live
label live
  say Booting a live USB bioinformatics session...
  kernel /casper/vmlinuz.efi  
  append  file=/cdrom/preseed/lubuntu.seed boot=casper persistent initrd=/casper/initrd.lz quiet splash noprompt --


```
