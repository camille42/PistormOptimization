# Pistorm Optimization (fork)


The goal is to cleanup a bit the Raspbian OS and make the root partition read-only so that you can power off the Amiga without worrying about the SD corruption, but also start the Pistorm emulator the earliest possible.

# Prerequisite

You must partition the SD card. You can use GParted on Linux, a GParted live on USB pen if you use Windows or the partition tool from Paragon.
You have to shrink the root partition to something like 3.5GB, and create another primary partition after the root one using the remaining space.
![Partitions](https://user-images.githubusercontent.com/28825/118687251-1618bf80-b805-11eb-9142-072d5efb29bd.png)

# What these scripts are doing

* 01-Cleanup.sh will make other .sh file executable and do some cleanup on Raspbian OS by removing unnecessary services.
* 02-Edit-Fstab.sh will only open the editor, please read bellow before running it to learn how to modify the fstab.
* 03-Complete-RO.sh will do the necessary actions to prepare the system to use a read-only root filesystem.
* 04-Move-emulator.sh will move the pistorm emulator you previously installed in /home/pi/pistorm to the new partition and will install it as a service to be run as soon as the OS permit.

# What is left to be done manualy

* Edit the option file located in /boot/config.txt. You can plug the SD in a Windows/Linux PC and edit the file on the FAT partition.

Add the following content in the ending [all] section:

`# Disable the rainbow splash screen`

`disable_splash=1`

`# Disable bluetooth`

`dtoverlay=pi3-disable-bt`

`# Overclock the SD Card from 50 to 100MHz`

`# This can only be done with at least a UHS Class 1 card marked U with a '1' inside`

`dtoverlay=sdtweak,overclock_50=100`
 
`# Set the bootloader delay to 0 seconds. The default is 1s.`

`boot_delay=0`

* Edit the file /boot/cmdline.txt to add kernel parameters. As above it's also located on the FAT partition.

Add this options at the end of the line:

` fastboot noswap ro`

Last one to be able to make the system work in read-only mode.

Edit file /lib/systemd/system/systemd-random-seed.service

Then add the folowing line in [Service] section before ExecStart:

`ExecStartPre=/bin/echo "" >/tmp/random-seed`

![Random](https://user-images.githubusercontent.com/28825/118709811-c0044600-b81d-11eb-8afc-efafabd6299f.png)

# Usage 

To begin, you will need to pull this repository, do this by entering the following command:

`git clone https://github.com/Neo2003/PistormOptimization.git`

CD inside this new folder:

`cd PistormOptimization`

Make the first file executable:

`chmod +x 01-Cleanup.sh`

Then execute it with sudo:

`sudo ./01-Cleanup.sh`

Before running 02-Edit-Fstab.sh, read above

You will need to add a line in fstab, for this copy the line with "/  ext4" and past it above, then change "-02" by "-03" and complete the line like on the picture bellow.


![Fstab1](https://user-images.githubusercontent.com/28825/118681842-32662d80-b800-11eb-8fd6-ba336a1b81d2.png)

Now, make the root and boot partition read-only by adding ',ro' in the parameters:

![Fstab3](https://user-images.githubusercontent.com/28825/118725685-8db01400-b830-11eb-9024-615a7491c24c.png)

Now run 02-Edit-Fstab.sh to make these changes listed before to the fstab:

`sudo ./02-Edit-Fstab.sh`

Once done, you can go on with the rest of the files:

`sudo ./03-Complete-RO.sh`

`sudo ./04-Move-emulator.sh`

This last one will move the emulator to /mnt/Amiga we declared in fstab and create a service to start it.

# Safe gard

In case of problem, files /etc/bash.bashrc and /etc/bash.bash_logout have been updated.

You will then be able to execute `rw` to get write access and `ro` to lock it again.

# End

Now that everything is done, reboot and be happy with a fast startup and a safe system.

`sudo reboot`

# Author

Made by Rodrik Studio

Youtube channel: https://www.youtube.com/c/RodrikStudio
