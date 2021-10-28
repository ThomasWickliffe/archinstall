# archinstall
Arch Install Documentation
### **Creating the New Virtual Machine**
>After downloading the arch iso, useVMware Workstaion to create a new virtual machine. Select the iso option  and refrence the arch iso as the installer. 
<br><br>
Next, select the **linux** guest operating system and version **Debian 10.x-bit**
>
>Continuing on to set the following settings:
>- Set 2 processors
>- 2GB of RAM
>- Use bridged networking 
>- Set LSI Logic for the I/0 controller type
>- ScSi For the visual disk tyoe
>- Create a new visual disk
>- Split virtual disk into multiple files
>- Hit next on the disk file or change how you like.
>- Select finish


### **Checking Internet setup**
>After booting, check to see if the internet is connected to the virtual machine by using the command.
>- `ip link`
>
>Additionally you can ping a website to check as well 
>-  `ping archlinux.org`

### **Update the system clock**

>To check the accutracy fo the systems clock you can use the ***timedatectl(1)***
>
> - `# timedatectl set-ntp true`
>
>In order to check the status of the time and date use the command
>
>- `# timedatectl status`
>
>To change timezones use the command 
>
 >- `# timedatectl set-timezone America/Chicago`
>
 >or whatever time zone you wish. You can check a list of timezones by using the command
>
 >- `# timedatectl list-timezones`

### **Disk Partitioning**
>Before partitioning the disks, you must check which hard disk you have by using the command 
>
>- `# fdisk -l`
>
>My machine has a ***/dev/sda*** label
>
>To enter into your hard disk use the command
>
>- `# fdisk /dev/sda`
>
>From here you want to create an ESP partition, which will be our ***sda1*** partition. While in the partition settings: 
>
>- Use the command ***`n`*** to create a new partition.
>- Next use the command **`p`** to set the primary. 
>- Next enter the value **`1`** to select the partition number
>- following this hit ***enter*** to default the first section 
>- For the second enter the value ***+500M*** in order to set the disk to ***500 MB***.
>- (**shortcut**: after typing `n` you could hit `enter` three times and then enter in the `+500M` selection)
 ><br>
 >
 >Once the disk is created change the **type** to ***the EFI System***.
 >While still in the fdisk command type ***`t`*** to change the type and for the partition type, type in the value ***ef*** and hit **Enter**
 >
>Next create a root partition, ***sda2***  that uses the remainder of the disk space. Follow the previous steps using the defaul settings by hitting enter all the way down. (No need to chanhge the type)<br> <br> Exit and save the disk settings by using the ***`w`*** command to write, save and exit the disk settings. 
<br><br>

## Creating a Filesystem on the Disks ##
>Next, you need to create a filesystem on the disks by using the commands
>
>- >`# mkfs.fat -F32 /dev/sda1`
>
>- >`# mkfs.ext4 /dev/sda2`

## Mirror and Arch Install ##
>First syng the pacman repository and install the reflector with the following commands:
>
>- >`pacman -Syy`
>- >`pacman -S reflector`
>
>Now its time to install Arch Linux by 
>-  First mount the disk with the commad
>- >`mount /dev/sda2 /mnt` 
>
>- Next install the necessary packages with the command 
>- >`# pacstrap /mnt base lunix linux-firmware nano`
>- Complete the installation and wait for it to complete.

## Configure Arch ##
>Create a fstab file to help define the the partitions that we made on the disk
>- >`genfstab -U /mnt >> /mnt/etc/fstab`
>
>Now its time to use **chroot** to configure the installation.
>- >`arch-chroot /mnt`
>
>Set up the locale
>- >`nano /etc/locale.gen`
>- >`nano /etc/locale.conf`
>- now type in your language prefrences
>- >`LANG=en_US.UTF-8e` 
>- save with **control o** and exit with **control w**
>
>
>Now edit the host name
>- >`nano /etc/hostname`
>- Here we are defining a name for the machine
>- Enter your desired name, for me **thomasw**
>- save with **control o** and exit with **control w**
>
>Next we need to edit the **hosts** file 
>- >`nano /etc/hosts`
>- Here you need to type in the following changes
>- >`127.0.0.1`  <"tab">   `localhost` <br> `::1` <"tab"><"tab"> `localhost` <br> `127.0.1.1`<"tab"> `{hostname}`
>- save with **control o** and exit with **control w**
>
>Create root user password
>- >`passwd`
>- type in and confirm new password
>
>Next download and install grub bootloader and other packages
>- >`pacman -S grub efibootmgr networkmanager network-manager-applet wireless_tools wpa_supplicant dialog os-prober base-devel linux-headers reflctor git openssh bluez bluez -utils cups xdg-utils xdg-user-dirs`
>- accept the defaults and finish download
>
>Generate the directory where the EFI partition will be mounted and mount it 
>- >`mkdir /boot/efi`
>- >`mount /dev/sda1 /boot/efi`
>
>Now to install our GRUB bootloader
>- >`grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB`
>
>Next generate a configuration file for GRUB
>- >`grub -mkconfig -o /boot/grub/grub.cfg `
>
>
>Setup Network Manager
>- >`systemctl enable NetworkManager`
>
>Create new user and give password
>- >`useradd -m thomas`
>- >`passwd thomas`
>- Enter and confirm new password for the user "thomas"
>
>Time to install the Desktop Environment (KDE for this machine)
>- Install the display server
>- >`pacman -S xorg`
>- hit enter to download and install
>- NOW install the Desktop Environment; after each command set default and continue
>- >`sudo pacman -S sddm`
>- >`sudo system enable sddm`
>- >`pacman -S plasma kde-applications packagekit-qt5`
>- Finish the install and wait to download
>
>`Exit` the **chroot**,
>
>`shutdown now`
>
>Upon reboot the machine should boot onto the KDE login screen. 

## Inside the KDE GUI Desktop Environment ##
>Time to add two users Sal and Codi and set an expiring password. Use the command 
>
>- `sudo useradd -m <enter user name>`
>
>to add the two users.
>
>For the passwords use the command 
>- `passwd -e <user name>`
and enter in the password and confirm it.
>
>Next, we will add the shell fish as an interactive shell. Download the shell and other corresponding packages with the following command:
>
>- `sudo pacman -S fish pkgfile ttf-dejavu powerline-fonts inetutil`
>
>enter the sudo password and complete the download. After this we need to edit the bashrc file to execute the fish interactive shell.
>
>- `vim .bashrc`
>
> Inside this file add to the bottom of the 
>- `exec fish`
>
>hit the **esc** button and type `:wq` to save and leave the file. Now exit the command prompt and open it back up to use the new fish interactive shell. 
>
>This interactive shell comes with a color coded command line as well as error detection when typing to see if a command or a file actually exists. Very useful and fun stuff!
