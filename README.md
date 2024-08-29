# MyArchLinuxExperience

Hello, This readme file is about my challenge using Arch Linux for main Operating System.
I relize that, the hardbooting, hard installing Arch Linux , were very meaningful when you need to study the Linux system.
However, nonetheless the benefits of the full-controlling the Linux system, I faced a lot of problems of the 'not supported' or 'less- maintained' pkgs on the arch system. These  restrictions are not joking. It is very stressful to control it.

So, the conclusion is, I give up to change my main OS system to Arch Linux.
We may can strongly agree with the well maden OS, like windows and MacOs, Android, etc, they are just have the moat.

If you have interest in Installing ArchLinux in hardInstalling, Please reference this md file.
Thank you.

-Heisnotimposter

## Initial Installation

* **iwctl**
$ iwctl
help
device list
station '' scan
station '' get-networks
station '' connect SSID
* ****

## After the Installation

## Remote Desktop Challenges (AnyDesk)

* **Display Server Error:**
    - Encountered the "display_server_not_supported" error when connecting from macOS to Arch Linux.
    - Switched from Wayland to X11 as the display server to improve compatibility.
* **Sound Disruption:**
    - Switching to X11 caused the sound system to break, with Chrome displaying a "try restarting your device" message.
    - Reinstalling PulseAudio and associated packages fixed this.
* **Bluetooth Conflict:**
    - Connecting a Bluetooth headset caused conflicts and Chrome sound playback issues.
    - Resolved by removing and reinstalling PulseAudio.
    - System notification sounds also started working correctly.

## Persistent PulseAudio Issues:

* **Repeated Crashes:**
    - PulseAudio repeatedly crashed with the "Start request repeated too quickly" error.
    - The issue was likely due to conflicts between AnyDesk, Bluetooth, and PulseAudio.
    - Uninstalling AnyDesk, restarting PulseAudio, and reconnecting the Bluetooth headset resolved the problem.

## KDE Plasma Crashes:

* **Segmentation Fault:**
    - Plasma's `plasmashell` crashed with a segmentation fault.
    - Initially thought to be caused by removing a default Breeze wallpaper.
    - Troubleshooting included resetting Plasma and KWin configurations, checking for conflicting themes, and updating packages.
    - Ultimately, the issue was deeply rooted in the system configuration and a clean reinstallation was considered the most effective solution.

## Post-Installation Challenges:

* **No Internet Connection (After Reboot):**
    - The `iwctl` tool from the live environment was unavailable.
    - Installed `iwd` to connect to Wi-Fi using the familiar `iwctl` interface.
* **DNS Resolution Failure:**
    - Unable to ping external domains due to "Temporary failure in name resolution."
    - Enabled and configured `systemd-resolved`, ensuring the correct DNS server was being used.
    - Verified network settings and resolved potential conflicts with NetworkManager.
    - Finally, fixed the issue by editing the `/etc/resolv.conf` file and using `nameserver 8.8.8.8`.

## Lessons Learned:

* **Display Servers:** Understand the differences between X11 and Wayland and their compatibility with various applications.
* **PulseAudio:** Be aware of potential conflicts between PulseAudio, Bluetooth devices, and remote desktop software.
* **Reinstallation:** While a last resort, reinstalling can be a valid solution when persistent issues arise.
* **Backup:** Always back up your data before making major changes or attempting a reinstallation.
* **Arch Wiki:** The Arch Wiki is an invaluable resource for installation guides and troubleshooting.

## Future Considerations:

- Explore containerization to isolate applications like AnyDesk.
- Investigate alternative Bluetooth managers if issues persist.
- Consider using XWayland to run X11 applications within Wayland if needed.
- Regularly update your system and packages to minimize compatibility issues.

I hope this README serves as a helpful guide and reference for my future Arch Linux endeavors and for others who might encounter similar challenges.

1. Boot from USB: (BIOS/UEFI settings)

2. Connect to Wi-Fi:

Bash
iwctl
device list
station <device_name> scan
station <device_name> get-networks
station <device_name> connect <SSID>  # Enter password if needed


3. Partitioning (cfdisk):

Bash
cfdisk /dev/sda  # Replace sda with your drive

4. Formatting:

Bash
mkfs.fat -F32 /dev/sda1  # EFI System Partition (ESP)
mkswap /dev/sda2          # Swap
mkfs.ext4 /dev/sda3        # Root (/)

5. Mounting:

Bash
mount /dev/sda3 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
swapon /dev/sda2


6. Installation (Base + Essentials):

Bash
pacstrap /mnt base linux linux-firmware intel-ucode sof-firmware base-devel sudo grub efibootmgr nano networkmanager

7. Generate fstab:

Bash
genfstab -U /mnt >> /mnt/etc/fstab

8. Chroot (Enter the New System):

Bash
arch-chroot /mnt

9. Time Zone & Hardware Clock:

Bash
ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
hwclock --systohc
Use code with caution.

10. Locale:

Bash
nano /etc/locale.gen   # Uncomment en_US.UTF-8 UTF-8 and others as needed
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf  # Or your chosen locale

11. Hostname:

Bash
echo "myhostname" > /etc/hostname

12. Root Password & User Account:

Bash
passwd
useradd -m -G wheel -s /bin/bash <your_username>
passwd <your_username>

13. Enable sudo for User:

Bash
EDITOR=nano visudo  # Uncomment line for '%wheel ALL=(ALL) ALL'

14. NetworkManager:

Bash
systemctl enable NetworkManager

15. Bootloader (Grub):

Bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB # If UEFI
grub-mkconfig -o /boot/grub/grub.cfg

16. Exit & Reboot:

Bash
exit
umount -R /mnt
reboot

##Post-Installation (After Reboot):

##To update the system
sudo pacman -Syu

#Update the database:
sudo pacman -Syy

##Installing
#To install a package (always run pacman -Syu, before installing):
sudo pacman -S package_name

#To install a local package, or from a website:
sudo pacman -U /path/to/the/package

#To re-install all packages (those from the repo’s), in case of emergency:
pacman -Qnq | pacman -S -

##Removing Packages
#If you want to only remove the package, the following command is sufficient:
sudo pacman -R

#To remove the package and those of its dependencies that aren’t needed by any other application, do
sudo pacman -Rs

#Finally, to remove the package, avoid orphaned dependencies and erase its global configuration, type
sudo pacman -Rns package_name

#which in most cases is the proper command to remove software.

##Searches/Queries
#Info about an installed package:
pacman -Qi package_name

#Queries the repo about a package:
pacman -Ss package_name

#Queries the repo about a packages, and all that depend on it:
pacman -Sii package_name

##Howto
#List Installed Packages that are not in the Official Repositories:

#If you want a list of the packages you built and installed locally or packages that are no longer in the official repositories:

pacman -Qm

#Make sure to check this regularly, preferably monthly but at least every three months. KaOS repositories are always moving so you don’t want to keep
unmaintained and possibly conflicting packages in your install.

##Pacman is completely broken! How do I reinstall it?
#In the case that pacman is broken beyond repair, manually download the necessary packages (openssl, libarchive, and pacman) and extract them. The pacman binary will be restored along with its default configuration file. Afterwards, reinstall these packages with pacman to maintain package database integrity. You can use this command to extract them.

sudo tar -xwvpf <i>package_name-version.tar.xz</i> -C / --exclude .PKGINFO --exclude .INSTALL

##Connect to Wi-Fi using NetworkManager.
#Install desired desktop environment (e.g., sudo pacman -S plasma-desktop) and a display manager if not already chosen during base install.
#Key Improvements from Previous Attempts:

#Network Focus: Wi-Fi connection is established early in the process.
#Concise Commands: Streamlined for readability and ease of use.
#Essential Packages Only: Focus on core components to avoid conflicts.
#Post-Install Reminder: Guides to complete the setup after reboot.

##references:
https://youtu.be/68z11VAYMS8?si=RdaTKJVFR-wjqjPA
https://wiki.archlinux.org/title/Installation_guide
https://wiki.archlinux.org/title/iwd#iwctl
https://youtu.be/3zqITuprlL8?si=vCnjLmzGcFbhV9-B
https://kaosx.us/docs/pacman/
