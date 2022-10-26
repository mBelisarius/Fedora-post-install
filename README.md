# Fedora post-install guide


Fedora post-install guide, currently for Fedora 36. 

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

### Update the system

Update the entire system
```
sudo dnf update -y
sudo dnf install -y dnf-plugins-core
sudo dnf install -y kernel-devel kernel-headers
```

Update the firmware
```
sudo fwupdmgr refresh --force
sudo fwupdmgr get-updates
sudo fwupdmgr update -y
```

### Enable RPM Fusion repositories<sup>[[4]]</sup>
    
Enable RPM Fusion to both free and non-free repository: 
```
sudo dnf install -y https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

RPM Fusion AppStream metadata: 
```
sudo dnf groupupdate -y core
```

RPM Fusion Multimedia post-install: 
```
sudo dnf groupupdate -y multimedia --setop="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
sudo dnf groupupdate -y sound-and-video
```

### Install package managers
    
Enable Flathub repository<sup>[[5]]</sup>: 
```
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```
    
Enable snaps from Snap Store<sup>[[6]]</sup>: 
```
sudo dnf install -y snapd
```

(Optional) Install Snap Store GUI: 
```
sudo snap install -y snap-store
```

### GPU drivers for NVIDIA cards

To install NVIDIA drivers, the recommended display server is X11 (X-org), although Wayland (XWayland) is supported starting with Fedora 35<sup>[[7]]</sup>.
```
sudo dnf install -y akmod-nvidia
sudo dnf install -y xorg-x11-drv-nvidia-cuda
sudo dnf install -y xorg-x11-drv-nvidia-cuda-libs
sudo dnf install -y vdpauinfo libva-vdpau-driver libva-utils
sudo dnf install -y vulkan
```


## Optimizations


### BRTFS filesystem partitions

Optimize the BRTFS filesystem partition by adding the following tags in `/etc/fstab`<sup>[[8]]</sup>. A fstab file example is given [here]().
```
compress=zstd:3,discard=async,space_cache,commit=60
```

To update systemd, run
```
sudo systemctl daemon-reload
```


## Installing programs


### Remove unnecessary programs

### Install essential programs

Install your prefered text-editor (if not the default), in this case Sublime-text 3 using dnf<sup>[[9]]</sup>: 
```
sudo rpm -v --import https://download.sublimetext.com/sublimehq-rpm-pub.gpg
sudo dnf config-manager --add-repo https://download.sublimetext.com/rpm/stable/x86_64/sublime-text.repo
sudo dnf install -y sublime-text
```

Install your prefered navigator (if not the default), in this case Brave using dnf<sup>[[10]]</sup>: 
```
sudo dnf config-manager --add-repo https://brave-browser-rpm-release.s3.brave.com/x86_64/
sudo rpm --import https://brave-browser-rpm-release.s3.brave.com/brave-core.asc
sudo dnf install -y brave-browser
```

Install VMWare (or VirtualBox): [VMWare Workstation Player](https://customerconnect.vmware.com/en/downloads/details?downloadGroup=WKST-PLAYER-1624&productId=1039&rPId=91446)

Install Wine to run .exe files<sup>[[11]]</sup>: 
```
sudo dnf config-manager --add-repo https://dl.winehq.org/wine-builds/fedora/36/winehq.repo
sudo dnf install -y wine.x86_64
```

Install GitHub CLI<sup>[[12]]</sup>: 
```
sudo dnf install -y 'dnf-command(config-manager)'
sudo dnf config-manager --add-repo https://cli.github.com/packages/rpm/gh-cli.repo
sudo dnf install -y gh
```

JetBrains ToolBox for JetBrains IDEs: [JetBraibs ToolBox](https://www.jetbrains.com/toolbox-app/)

Install Anaconda/Conda distribution for Python: [Anaconda distribution](https://www.anaconda.com/products/distribution)

Install SDKMan<sup>[[13]]</sup>:
```
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
```

Install Spotify using snap<sup>[[14]]</sup>: 
```
sudo snap install spotify
```

Install Discord: [Discord](https://discord.com/)


## KDE Plasma only


Install ocs-url for further customization<sup>[[15]]</sup>: 
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
[9]: <https://www.sublimetext.com/docs/linux_repositories.html#dnf> "Sublime text install guide"
[10]: <https://brave.com/linux/#release-channel-installation> "Brave install guide"
[11]: <https://wiki.winehq.org/Fedora> "Wine install guide"
[12]: <https://github.com/cli/cli/blob/trunk/docs/install_linux.md> "GitHub CLI install guide"
[13]: <https://sdkman.io/install> "SDKMan install guide"
[14]: <https://www.spotify.com/br/download/linux/> "Spotify download"
[15]: <https://store.kde.org/p/1136805/> "ocs-url download"
