# Fedora Workstation post-install guide


Fedora Workstation Edition post-install guide, currently for Fedora 37. 

Remember to reboot the system to apply fundamental changes such as kernel updates.


## System


### Configure the system

Configure the dnf package manager in `/etc/dnf/dnf.conf`<sup>[[1]]</sup>: [dnf.conf.example](https://github.com/mBelisarius/Fedora-post-install/blob/main/dnf.conf.example)

Change hostname: 
```
sudo hostnamectl set-hostname [hostname]
```

Configure zram-generator in `/etc/systemd/zram-generator.conf`<sup>[[2]]</sup>: [zram-generator.conf.example](https://github.com/mBelisarius/Fedora-post-install/blob/main/zram-generator.conf.example)

Configure the swapness in `/etc/sysctl.conf`<sup>[[3]]</sup>: 
```
vm.swappiness=99
```

Disable geoclue services (a reboot is necessary toapply the changes):
```
sudo systemctl disable geoclue.service
sudo systemctl mask geoclue.service
```

### Update the system

Update the entire system
```
sudo dnf update -y
sudo dnf install dnf-plugins-core -y
sudo dnf install kernel-devel kernel-headers -y
```

Update the firmware and UEFI (supported by some manufacturers)
```
sudo fwupdmgr get-devices
sudo fwupdmgr refresh --force
sudo fwupdmgr get-updates
sudo fwupdmgr update -y
```

### Enable RPM Fusion repositories<sup>[[4]]</sup>

Add extra Fedora repositories:
```
sudo dnf install fedora-workstation-repositories -y
```
    
Enable RPM Fusion to both free and non-free repository: 
```
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm -y
```

RPM Fusion AppStream metadata: 
```
sudo dnf groupupdate core -y
```

RPM Fusion Multimedia post-install: 
```
sudo dnf groupupdate multimedia --setop="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin -y
sudo dnf groupupdate sound-and-video -y
```

### Install package managers
    
Enable Flathub repository<sup>[[5]]</sup>: 
```
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```
    
Enable snaps from Snap Store<sup>[[6]]</sup>: 
```
sudo dnf install snapd -y
```

### GPU drivers for NVIDIA cards

To install NVIDIA drivers, the recommended display server is X11 (X-org), although Wayland (XWayland) is supported starting with Fedora 35<sup>[[7]]</sup>.
```
sudo dnf install akmod-nvidia -y
sudo dnf install xorg-x11-drv-nvidia-cuda -y
sudo dnf install xorg-x11-drv-nvidia-cuda-libs -y
sudo dnf install vdpauinfo libva-vdpau-driver libva-utils -y
sudo dnf install vulkan -y
```

### AMD drivers

Fedora ships up to date `mesa` which is all you will ever need.

### Tweaks

Better font rendering using free substitutions for popular proprietary fonts from Microsoft and Apple operating systems. Also enables subpixel (rgb) antialiasing.
```
sudo dnf copr enable dawid/better_fonts -y
sudo dnf install fontconfig-font-replacements -y
sudo dnf install fontconfig-enhanced-defaults -y
```


## Optimizations


### BRTFS filesystem partitions

Optimize the BRTFS filesystem partition by adding the following tags in `/etc/fstab`<sup>[[8]]</sup>. A fstab file example is given [here](https://github.com/mBelisarius/Fedora-post-install/blob/main/fstab.example).
```
compress=zstd:3,discard=async,commit=60,usebackuproot
```

To update systemd, run
```
sudo systemctl daemon-reload
```


## Installing applications


### Remove unnecessary programs

### Development tools

Install GitHub CLI<sup>[[9]]</sup>: 
```
sudo dnf install 'dnf-command(config-manager)' -y
sudo dnf config-manager --add-repo https://cli.github.com/packages/rpm/gh-cli.repo
sudo dnf install gh -y
```

Install your prefered text-editor.

Sublime-text 3 using dnf<sup>[[10]]</sup>: 
```
sudo rpm -v --import https://download.sublimetext.com/sublimehq-rpm-pub.gpg
sudo dnf config-manager --add-repo https://download.sublimetext.com/rpm/stable/x86_64/sublime-text.repo
sudo dnf install sublime-text -y
```

VSCode using dnf<sup>[[11]]</sup>:
```
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'
dnf check-update
sudo dnf install code -y
```

Install JetBrains ToolBox for JetBrains IDEs: [JetBraibs ToolBox](https://www.jetbrains.com/toolbox-app/)

Install Anaconda/Conda distribution for Python environments: [Anaconda distribution](https://www.anaconda.com/products/distribution)

Install SDKMan to manage parallel versions of multiple Java SDKs<sup>[[12]]</sup>:
```
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
```

Install .NET SDK and runtime<sup>[[13]]</sup><sup>[[14]]</sup>:
```
sudo dnf install dotnet -y
```

Install QEMU for virtual machines<sup>[[15]]</sup>:
```
sudo dnf install @virtualization -y
```

### Utilities

Install Flatseal to review and modify permissions from Flatpak applications using flatpak<sup>[[16]]</sup>:
```
flatpak install flatseal -y
```

Install Wine to run .exe files using dnf<sup>[[17]]</sup>: 
```
sudo dnf config-manager --add-repo https://dl.winehq.org/wine-builds/fedora/36/winehq.repo
sudo dnf install wine.x86_64 -y
```

### Image and video editing

Install Gimp for image editing using flatpak<sup>[[18]]</sup>:
```
flatpak install https://flathub.org/repo/appstream/org.gimp.GIMP.flatpakref -y
```

Install ShotCut for video editing using flatpak<sup>[[19]]</sup>:
```
flatpak install flathub org.shotcut.Shotcut -y
```

### Internet and multimidia

Install your prefered internet browser (if not the default FireFox).

Google Chrome:
```
sudo dnf config-manager --set-enabled google-chrome
sudo dnf install google-chrome-stable -y
```
Brave using dnf<sup>[[20]]</sup>:
```
sudo dnf config-manager --add-repo https://brave-browser-rpm-release.s3.brave.com/x86_64/
sudo rpm --import https://brave-browser-rpm-release.s3.brave.com/brave-core.asc
sudo dnf install brave-browser -y
```

Install Discord: [Discord](https://discord.com/)

Install Spotify using snap<sup>[[21]]</sup>: 
```
sudo snap install spotify
```

Install OBS Studio<sup>[[22]]</sup>:
```
flatpak install flathub com.obsproject.Studio
```


## KDE Plasma only


Install ocs-url for further desktop customization<sup>[[23]]</sup>: 
```
sudo dnf install -y qt5-qtbase qt5-qtbase-gui qt5-qtsvg qt5-qtdeclarative qt5-qtquickcontrols
sudo rpm -i /path/to/ocs-url*.rpm
```

Plasma Integration plugin for Chrome/Brave: [Plasma Integration](https://chrome.google.com/webstore/detail/plasma-integration/cimiefiiaegbelhefglklhhakcgmhkai?)


[1]: <https://dnf.readthedocs.io/en/latest/conf_ref.html> "dnf documentation"
[2]: <https://github.com/systemd/zram-generator/blob/main/man/zram-generator.conf.md> "zram documentation"
[3]: <https://www.howtogeek.com/449691/what-is-swapiness-on-linux-and-how-to-change-it/> "Swapness article"
[4]: <https://rpmfusion.org/Configuration> "RPM Fusion documentation"
[5]: <https://flatpak.org/setup/Fedora> "Flatpak setup"
[6]: <https://snapcraft.io/install/snap-store/fedora)> "Snap install guide"
[7]: <https://rpmfusion.org/Howto/NVIDIA> "NVIDIA drivers install guide for Fedora"
[8]: <https://btrfs.readthedocs.io/en/latest/Administration.html#mount-options> "BRTFS documentation"
[9]: <https://github.com/cli/cli/blob/trunk/docs/install_linux.md> "GitHub CLI install guide"
[10]: <https://www.sublimetext.com/docs/linux_repositories.html#dnf> "Sublime text install guide"
[11]: https://code.visualstudio.com/docs/setup/linux> "Visual Studio Code on Linux"
[12]: <https://sdkman.io/install> "SDKMan install guide"
[13]: <https://learn.microsoft.com/en-us/dotnet/core/install/linux-fedora> "Microsoft Install .NET 6 Fedora"
[14]: <https://packages.fedoraproject.org/pkgs/dotnet6.0/> "Fedora Packages - Source Package dotnet6.0"
[15]: <https://www.qemu.org/> "QEMU"
[16]: <https://flathub.org/apps/details/com.github.tchx84.Flatseal> "Flatseal"
[17]: <https://wiki.winehq.org/Fedora> "Wine install guide"
[18]: <https://www.gimp.org/> "GIMP"
[19]: <https://www.shotcut.org/> "ShotCut"
[20]: <https://brave.com/linux/#release-channel-installation> "Brave install guide"
[21]: <https://www.spotify.com/br/download/linux/> "Spotify download"
[22]: <https://flathub.org/apps/details/com.obsproject.Studio> "OBS Studio"
[23]: <https://store.kde.org/p/1136805/> "ocs-url download"
