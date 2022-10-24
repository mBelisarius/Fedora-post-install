# Fedora post-install

- ## Configure the system
    - Configure the dnf package manager in `/etc/dnf/dnf.conf`<sup>[[1]]</sup>: [dnf.conf.example](https://github.com/mBelisarius/Fedora-post-install/blob/main/dnf.conf.example)
    - Change hostname: `sudo hostnamectl set-hostname [hostname]`
    - Configure zram-generator in `/etc/systemd/zram-generator.conf`<sup>[[2]]</sup>: [zram-generator.conf.example](https://github.com/mBelisarius/Fedora-post-install/blob/main/zram-generator.conf.example)
    - Configure the swapness in `/etc/sysctl.conf`<sup>[[3]]</sup>: `vm.swappiness=99`

- ## Update the system
```
sudo dnf update
sudo dnf install dnf-plugins-core
```

- ## Enable and install specific RPM repositories<sup>[[4]]</sup>
    - Enable RPM Fusion to both free and non-free repository: `sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm`
    - RPM Fusion AppStream metadata: `sudo dnf groupupdate core`
    - RPM Fusion Multimedia post-install: 
    ```sudo dnf groupupdate multimedia --setop="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
    sudo dnf groupupdate sound-and-video
    ```

- ## Install package managers
    - Enable Flathub repository<sup>[[5]]</sup>: `flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo`
    - Enable snaps from Snap Store<sup>[[6]]</sup>: `sudo dnf install snapd`
    - (Optional) Install Snap Store GUI: `sudo snap install snap-store`


## Installing programs

- ## Remove unnecessary programs

- ## Install essential programs
    - Install your prefered text-editor (if not the default), in this case Sublime-text 3 using dnf<sup>[[7]]</sup>: 
    ```
    sudo rpm -v --import https://download.sublimetext.com/sublimehq-rpm-pub.gpg
    sudo dnf config-manager --add-repo https://download.sublimetext.com/rpm/stable/x86_64/sublime-text.repo` then `sudo dnf install sublime-text
    ```
    - Install your prefered navigator (if not the default), in this case Brave using dnf<sup>[[8]]</sup>: 
    ```sudo dnf config-manager --add-repo https://brave-browser-rpm-release.s3.brave.com/x86_64/
    sudo rpm --import https://brave-browser-rpm-release.s3.brave.com/brave-core.asc
    sudo dnf install brave-browser
    ```
    - Install VMWare (or VirtualBox): [VMWare Workstation Player](https://customerconnect.vmware.com/en/downloads/details?downloadGroup=WKST-PLAYER-1624&productId=1039&rPId=91446)
    - Install Wine to run .exe files<sup>[[9]]</sup>: 
    ```
    sudo dnf config-manager --add-repo https://dl.winehq.org/wine-builds/fedora/36/winehq.repo
    sudo dnf install wine.x86_64
    ```
    - Install GitHub CLI<sup>[[10]]</sup>: 
    ```
    sudo dnf install 'dnf-command(config-manager)'
    sudo dnf config-manager --add-repo https://cli.github.com/packages/rpm/gh-cli.repo
    sudo dnf install gh
    ```
    - JetBrains ToolBox for JetBrains IDEs: [JetBraibs ToolBox](https://www.jetbrains.com/toolbox-app/)
    - Install Anaconda/Conda distribution for Python: [Anaconda distribution](https://www.anaconda.com/products/distribution)
    - Install Spotify using snap<sup>[[11]]</sup>: `sudo snap install spotify`
    - Install Discord: [Discord](https://discord.com/)

- ## KDE Plasma only

    - Install ocs-url for further customization<sup>[[12]]</sup>: 
    ```
    sudo dnf install qt5-qtbase qt5-qtbase-gui qt5-qtsvg qt5-qtdeclarative qt5-qtquickcontrols
    sudo rpm -i /path/to/ocs-url*.rpm
    ```
    -  Plasma Integration plugin for Chrome/Brave: [Plasma Integration](https://chrome.google.com/webstore/detail/plasma-integration/cimiefiiaegbelhefglklhhakcgmhkai?)


[1]: <https://dnf.readthedocs.io/en/latest/conf_ref.html> "dnf documentation"
[2]: <https://github.com/systemd/zram-generator/blob/main/man/zram-generator.conf.md> "zram documentation"
[3]: <https://www.howtogeek.com/449691/what-is-swapiness-on-linux-and-how-to-change-it/> "Swapness article"
[4]: <https://rpmfusion.org/Configuration> "RPM Fusion documentation"
[5]: <https://flatpak.org/setup/Fedora> "Flatpak setup"
[6]: <https://snapcraft.io/install/snap-store/fedora)> "Snap install guide"
[7]: <https://www.sublimetext.com/docs/linux_repositories.html#dnf> "Sublime text install guide"
[8]: <https://brave.com/linux/#release-channel-installation> "Brave install guide"
[9]: <https://wiki.winehq.org/Fedora> "Wine install guide"
[10]: <https://github.com/cli/cli/blob/trunk/docs/install_linux.md> "GitHub CLI install guide"
[11]: <https://www.spotify.com/br/download/linux/> "Spotify download"
[12]: <https://store.kde.org/p/1136805/> "ocs-url download"
