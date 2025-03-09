Arch Linux Installations for a fully dunctional desktop Enviornment
usually ranges in size from 3-5GB, Still it is recommended to use atleast a 16GB stick.
* SageMath - Math based calculations.
* TeX Live - Document Production.

To write a bootable USB installation medium in Linux, run the following command; /path/archlinux.iso is the path to the downloaded ISO and /dev/sdX is the path to your unmounted target USB drive:

dd bs=4M if=/path/archlinux.iso of=/dev/sdX status=progress && sync


BIOS - Basic Input/Output System <-- Legacy Mode
UEFI - Unified Extensible Firmware Interface
^ Both boot methods require a seperate partition scheme in the USB media.

Selecting a mode that the motherboard is not set to boot in will not damage anything or touch any of the other drives on the machine, the boot will simply fail and one can reboot in the other mode.

secure boot
The new UEFI specification also includes an optional mechanism to protect against pre-boot malware. The secure boot protocol is designed to only allow the booting of images signed with a cryptographic key contained in a machine's NVRAM.

i686 vs x86_64
32bit image vs the 64bit image.





Installing Arch Linux
https://wiki.archlinux.org/index.php/Official_Arch_Linux_Install_Guide 
Installation process is quite simple:
	Disk partioning and formatting
	Mount partitions, generate filesystem table
	Installing Arch Linux base system
	Chroot to configure locale, time, install grub etc. and reboot and done!

Refer to www.valleycat.org/linux/ArchUSB


###
Different keymap and/or language
###
#View the available keymaps:
ls /usr/share/kbd/keymaps/**/*.map.gz | less

#Load the required keymap.
*Here, mapname is the filename of the required map without path or file extension:
loadkeys mapname

#Change Language
nano /etc/locale.gen				// uncomment en_US.UTF-8 UTF-8 or any other language as desired
locale-gen					// Generate the locale information
cat /etc/locale.conf				// Ensure the LANG varable is set in /etc/locale.conf
echo LANG = localeline> /etc/locale.conf	// IF not present or incorrect create a new /etc/locale.conf 
^ localeline is the first word of the uncommented line in /etc/locale.gen	* for US English, this is en_US.UTF-8


#Connect to the Internet
wired

If you have an active networking cable is plugged into the machine and you are unable to connect to the internet, begin troubleshooting by viewing the interface names and statuses:
ip link

Ensure the network device is powered; ethname is the name of the interface of type link/ether (most likely the name is eno1 or eno0):
ip link set ethname up

Now attempt to manually lease a DHCP IP address from the network:
dhcpcd ethname

If you are issued a lease, try to ping an outside server again:
ping -c1 archlinux.org

If you still aren't connected or were unable to lease an IP address, view all the instances of dhcpcd to see what the hell is going on:
systemctl list-units | grep dhcpcd

See detailed instance information to troubleshoot any hardware issues:
systemctl status dhcpcd@ethname.service

wireless
If a wired connection is unavailable or you prefer to use wifi, most wireless interfaces are supported by the drivers on the installation ISO. To check if this is possible on your current machine, see if any kernel drivers have been loaded for the wireless interface:
lspci -k | grep -A3 "Network controller"

If no device is present or no drivers have been loaded, you are mostly out of luck; although it technically may be possible to import the proper drivers via some other removable memory device, such a procedure is far beyond the scope of this guide. If you really want to keep pursuing this route, checkout the official Linux wireless wiki and the ArchWiki wireless page to get started.


If the drivers have been loaded, view the names of any available wireless interfaces:
iw dev

Now bring the wifi interface up. Here, wifiname is the wifi interface name given by iw dev (the name will generally be six characters long, most likely starting with wlp followed by three additional numbers and/or letters):
ip link set wifiname up

Scan for available networks:
iw dev wifiname scan | grep "SSID:"

If you don't know what type of authentication is required by the network you want to connect to, view the full scan results:
iw dev wifiname scan | less

For a connection with no encryption; networkname is the network you wish to connect to:
iw dev wifiname connect "networkname"

For a connection with WPA/WPA2 encryption (most likely scenario); password is the wifi password:
wpa_supplicant -i wifiname -c <(wpa_passphrase "networkname" "password")

For a connection using WEP (old, not likely):
iw dev wifiname connect "networkname" key 0:"password"

Once a connection is established, fork the process to the background by pressing [ctrl]+z and typing bg.


Finally, attempt lease an IP address:
dhcpcd wifiname

Check your network connection:
ping -c1 archlinux.org

system time
Once connected to the internet, turn on the network time protocol to synchronize system time:

timedatectl set-ntp true





#Locate Disk
lsblk

#Wipe Drive - Takes time and optional
gdisk -l sdX
^Note the sectors and sector size (physical/logical)
*Use dd to write the USB with all zeros permanently erasing all data
dd if=/dev/zero of=/dev/sdX bs=logical-sector-size seek=0 count=sectors status=progress

# Partition disks
cfdisk /dev/sdX \tab [x]->your device
Delte All partitions
sda1 - boot partition -> 200 M [With bootable Flag]
sda2 - 8-10 G
*For Non Persistent partitions you may have a seperate swap partition. *sda3
sda4 - Rest of you Space
Write the partion scheme to device disk.

# Format partitions and make File system
mkfs.ext4 /dev/sda1
*mkswap /dev/sda[]
mkfs.ext4 /dev/sda2
mkfs.ext4 /dev/sda3

# Mount partitions
Root Mount:\tab mount /dev/sda2 /mnt
Boot Mount:\tab mkdir /mnt/boot
\tab\tab mount /dev/sda1 /mnt/boot
Home Mount:\tab mkdir /mnt/home
\tab\tab mount /dev/sda3 /mnt/home
*swapon /dev/sda[]\


### For UEFI + BIOS
gdisk /dev/sdX
WIPE ALL				d	[until 'no partition']
Create New GUID partition table		o
10MB MBR partition in the beginning	n	[+10MB	EF02	BIOS]
500MB ESP partition			n	[+500MB	EF00	EFI]
Linux partions				n	[Filesystem + home]
check new partion table			p
write it to disk and exit		w

lsblk /dev/sdX
mkfs.fat -F32 /dev/sdX2
mkfs.ext4 /dev/sdX3

mount /dev/sdX3 /mnt
mkdir /mnt/boot
mount /dev/sdX2 /mnt/boot
mkdir /mnt/home
mount /dev/sdX4 /mnt/home

# Fix mirror list
vi /etc/pacman.d/mirrorlist

# Install base system
pacman -Sy archlinux-keyring \tab\tab [IF errpor in step below]
pacstrap /mnt base base-devel\tab\tab [Other packages maybe added after a space like base-devel]

# Generate filesystem table
genfstab -U -p /mnt >> /mnt/etc/fstab\tab [for Persistent -U includespacUUID]
Verify:\tab cat /mnt/etc/fstab
*If more than two entries for each of the mounted partitions, 
*manually edit the /etc/fstab using 
nano /mnt/etc/fstab


###
Configure new System
###

# Chroot, config system
arch-chroot /mnt

# Set hostname [Comp Name]
echo [Comp Name] > /etc/hostname
*nano /etc/hosts and add line
* 127.0.0.1	hostname.localdomain	hostname
# Change root password
passwd

# Configure timezone
ln -s /usr/share/zoneinfo/Asia/Kolkata /etc/localtime

# gen /etc/adjtime 
hwclock --systohc

# Select en/US locale
vi /etc/locale.gen
locale-gen

#Some changes to Initial Ram Image
nano /etc/mkinitcpio.conf
Find the line HOOKS ="*"\tab [place block before autodetect]
*HOOKS=(base udev block filesystems keyboard fsck)
mkinitcpio -p linux\tab [Generating initial Ram image]

#Network interface name
*To ensure that wired and wifi interfaces are always
*named eth0 nad wlan0 respectively
ln -s /dev/null /etc/udev/rules.d/80-net-setup-link.rules


#Journal Config
*To ensure information about current process is stored in
*RAM instead of storage Disk.
nano /etc/systemd/journald.conf
storage = volatile	[To switch journal data storage to RAM]
SystemMaxuse = 16M	[To ensure OS doesn't overfill RAM with journal data]

#Mount Options
*disable the record keeping of file access times, no writes will occure when a file
*is read, only when it is modified.
nano /etc/fstab		[change relatime option to noatime]



# Install bootloader\par
************ DEPRECATED ****************
************ pacman -S grub-bios *******
************ mkinitcpio -p grub ********
pacman -S grub
grub-install --boot-directory=/boot --recheck --debug --target=i386-pc /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg\tab [Default Boot Screen]

##For UEFI + BIOS
pacman -S grub efibootmanager
grub-install --recheck --debug --target=i386-pc --boot-directory /boot /dev/sdX
grub-install --target=x86_64-efi --efi-directory /boot --boot-directory /boot --removable
grub-mkconfig -o /boot/grub/grub.cfg

#Getting on the Internet
pacman -S wpa_supplicant
systemctl enable dhcpcd

# Exit out of chrooted env
exit

# Cleanup reboot
umount -R /mnt
*swapoff /dev/sda[]
reboot




After rebooting to the installed system, I enabled bunch of services like \f1\fs20 dhcpcd\f0\fs24  so it autoconfigures network/ip for me, edit pacman conf file, update/upgrade using \f1\fs20 pacman\f0\fs24  Arch\rquote s package manager, configure sound, x-server, bunch of kernel modules, and install \f1\fs20 i3\f0\fs24  because all the good desktop environments are so messed up and I like tiling window managers.

# Configure network, edit stuff...
dhcpcd
systemctl enable dhcpcd
wifi-menu

# Add user
visudo  # Allow %wheel
useradd -m -g users -G storage,power,wheel -s /bin/bash 'username'
passwd 'username'
EDITOR=nano visudo
'username' ALL=(ALL) ALL

pacman -S polkit

# Pacman
vi /etc/pacman.conf
# Update
pacman -Syy
# Upgrade
pacman -Su

# Sound
usermod -a -G auddio [user] \tab Add user to audio group
pacman -S alsa-utils
alsamixer --unmute
speaker-test -c2
cat /proc/asound/cards\tab\tab List Sound Card
Then use the device name in the defaults added to ~/.asoundrc, like this:

pcm.!default \{
  type hw
  card PCH
\}

ctl.!default \{
  type hw
  card PCH
\}


#For hardware which is not loading
/etc/systemd/system.conf\tab Set Global timeout to 20s

# X
pacman -S xorg-server xorg-server-utils xorg-xinit

# VirtualBox drivers
pacman -S virtualbox-guest-utils
modprobe -a vboxguest vboxsf vboxvideo
vi /etc/modules-load.d/virtualbox.config

# X twm, clock, term
pacman -S xorg-twm xorg-clock xterm

#Video Driver
pacman -S mesa xf86-video-vesa
pacman -S xf86-video-ati xf86-video-intel xf86-video-nouveau xf86-video-vesa

#Input Synaptic
pacman -S xf86-input-synaptics

#Battery
pacman -S acpi


# i3
pacman -S i3
echo "exec i3" >> ~/.xinitrc

# Startx
startx



SOME APPS TO CONSIDER:
For Terminal.
gedit - Text Editor
feh - Image viewer
p7zip - Zip files
ranger - Terminal based file manager
thunar - GUI based file manager
cmus - music player
mpd - music player
calcus - calander
rofi - App launcher
compton - screen tearing remover
polybar - Status bar



Install Pacaur

cd ~
mkdir -p /tmp/pacaur_install
cd /tmp/pacaur_install

sudo pacman -S base-devel

sudo pacman -S expac yajl git

curl -o PKGBUILD https://aur.archlinux.org/cgit/aur.git/plain/PKGBUILD?h=cower
makepkg PKGBUILD --skippgpcheck --install --needed

curl -o PKGBUILD https://aur.archlinux.org/cgit/aur.git/plain/PKGBUILD?h=pacaur
makepkg PKGBUILD --install --needed

cd ~
rm -r /tmp/pacaur_install



#
