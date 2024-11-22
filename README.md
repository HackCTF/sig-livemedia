# SIG - AlmaLinux Live Media

This git repository contains Kickstarts and other scripts needed to produce the AlmaLinux Live DVDs. Two ways to create/build this project. Using `docker` containers or `AlmaLinux` system.

## Using Live media

Live media ISO files are available at https://repo.almalinux.org/almalinux/8/live/x86_64/ and https://repo.almalinux.org/almalinux/9/live/x86_64/, or use mirrors https://mirrors.almalinux.org find a close one. Refer to project wiki https://wiki.almalinux.org/LiveMedia.html#about-live-media for detailed installation steps.

## Build using AlmaLinux System

`AlmaLinux` system installed on a physical or vitual system is required use these steps to live-media ISO files. This proces takes `20-50 minutes` depends on number of CPU cores and internet speed. Minimum `15GB` work space for temporary files. Resulting ISO size ranges from `1.4GB` to `2.4GB` depends on build type. Execute following commands from root folder of sources.


### Build Environments

This project contains number of `KickStart` files to build live media for AlmaLiux. It uses `anaconda` and `livecd-tools` or `lorax` packages for ISO file build process. Use following command to install necessary softwares to build this project. Make sure to reboot the system prior to run the build commands.

```sh
cd ~
git clone https://github.com/HackCTF/sig-livemedia.git
cd sig-livemedia
sudo dnf -y install epel-release
sudo dnf -y --enablerepo="epel" install anaconda-tui \
                livecd-tools \
                qemu-kvm \
                lorax \
                subscription-manager \
                pykickstart \
                efibootmgr \
                efi-filesystem \
                efi-srpm-macros \
                efivar-libs \
                grub2-efi-*64 \
                grub2-efi-*64-cdboot \
                grub2-tools-efi \
                shim-*64
```

### Rolling the first release
First of all we need a boot iso we can base our custom image off:

```sh
wget https://mirror.grid.uchicago.edu/pub/linux/alma/9.4/isos/x86_64/AlmaLinux-9.4-x86_64-boot.iso
```

### Build using `lorax`

Run following commands to build hackctflinux live media.

```sh

sudo livemedia-creator \
       --make-iso \
       --iso AlmaLinux-9.4-x86_64-boot\   
       --ks kickstarts/hackctflinux-9.ks\   
       --nomacboot\   
       --resultdir ./iso-Hack0S\    
       --project "Hack0S"\   
       --releasever 0.1\   
       --iso-only\
       --iso-name Hack0S-0.1-x86_64-Live.iso

```

Since we want the build pipeline to fail-fast, we can start by calling ksvalidator to validate the kickstart file before starting the build process.

```sh
ksvalidator kickstarts/hackctflinux-9.ks && echo "OK" || echo "ERROR"
```

### Additional notes

* Current build scripts uses the AlmaLinux mirror closer to `US/East` zone. Use https://mirrors.almalinux.org to find and change different mirror.
* Use following commnd to generate package list to install `rpm -qa --qf "%{n}\n" | grep -v pubkey | sort > packages-name.txt`
* Make sure to use `--cache` for build process, it will help for faster build and less network traffic.'
