# System Reinstall instructions

## Fire snapd into the heart of the sun
This is because I hate the interface to snap and I'd rather sandbox my own stuff when required
```
$ sudo apt autoremove --purge snapd
```

Create a separate location for games, and use the games group for my games (setgid: not for everyone I realise, but it helps permissions) also add "myuser" to the input group (for access to certain devices, like dance mats)
```
$ sudo usermod -a -G games myuser
$ sudo mkdir /games
$ sudo chown myuser:games /games
$ sudo chmod g+s /games
$ sudo usermod -a -G input myuser 
```
## Add 32bit support 
This is for WINE and other apps that need it
```
$ sudo dpkg --add-architecture i386
```
..restart to get permisisons, and get rid of snap! :)

## Drivers and WINE
NVIDIA: Install latest NVIDIA drivers from their website
*-or-*
AMD: Stick with distro Mesa drivers (thankfully up to date now)

Mesa list for ubuntu/mint and vulkan
```
$ sudo apt install libegl-mesa0 libegl-mesa0:i386 libegl1-mesa-dev libgl1-mesa-dri libgl1-mesa-dri:i386 libglapi-mesa libglapi-mesa:i386 libglu1-mesa libglu1-mesa:i386 libglu1-mesa-dev libglx-mesa0 libglx-mesa0:i386 libosmesa6 libosmesa6:i386 mesa-utils mesa-utils-bin mesa-va-drivers mesa-va-drivers:i386 mesa-vdpau-drivers mesa-vdpau-drivers:i386 mesa-vulkan-drivers mesa-vulkan-drivers:i386 libvulkan1 libvulkan1:i386
```

## Add WINE staging from WineHQ

Install instructions for WINE for Ubuntu 24 (noble) and Mint 22
```
$ sudo mkdir -pm755 /etc/apt/keyrings
$ sudo wget -O /etc/apt/keyrings/winehq-archive.key https://dl.winehq.org/wine-builds/winehq.key
$ sudo wget -NP /etc/apt/sources.list.d/ https://dl.winehq.org/wine-builds/ubuntu/dists/noble/winehq-noble.sources
$ sudo apt install --install-recommends winehq-staging -y
```

# Gaming setup

Install Steam, basic development stuff, create a separate SteamLibrary location for steam to store its games
```
$ sudo apt install steam-installer cmake git build-essential scdoc -y
$ sudo mkdir /opt/games/SteamLibrary && sudo chown myuser:games /opt/games/SteamLibrary && chmod g+s /games/SteamLibrary
```

Run Steam once so it sets up and patches, and then login. Add library /opt/games/SteamLibrary
As alternatives to Steam Proton, grab the latest GE proton (https://github.com/GloriousEggroll/proton-ge-custom), untar to ~/.steam/root/compatibilitytools.d/ (needs to be created)

## Lutris Pre-Reqs
```
$ apt install python3-pil python3-numpy python3-build python3-hatchling python3-installer python3-yaml python3-venv cabextract
```

## Git Lutris and Git umu-launcher
Now handled by the handy gittris script in this very repo!
```
$ mkdir ~/source && cd ~/source && git clone https://github.com/lutris/lutris.git
```
Link and run Lutris once, let it install stuff and update
```
$ sudo ln -s ~/source/lutris/bin/lutris /usr/local/bin/lutris && lutris -d
```

Install umu-launcher so Lutris can access and use Steam protons
```
$ cd ~/source && git clone https://github.com/Open-Wine-Components/umu-launcher.git
$ cd ~/source/umu-launcher && ./configure.sh --user-install && make 2>&1 >>/dev/null && make install
```

# Ready player tux
System is pretty much ready to install and game!
