# Mint 22 - System install instructions for gaming

## Add 32bit support 
This is for WINE and other apps that need it
```
sudo dpkg --add-architecture i386
```
## GPU Drivers
### NVIDIA
Install latest [NVIDIA drivers from their website](https://www.nvidia.com/en-us/drivers/) - This should also pull in vulkan support
Note: If you have a very new GPU, Nvidia's 560 driver is broken, so use 565 onwards

### AMD
Stick with repo Mesa drivers (thankfully up to date now) or you can always go to [Kisak's PPA](https://launchpad.net/~kisak/+archive/ubuntu/kisak-mesa) which is reliable

Mesa and vulkan install command for ubuntu24/mint22
```
sudo apt install libegl-mesa0 libegl-mesa0:i386 libegl1-mesa-dev libgl1-mesa-dri libgl1-mesa-dri:i386 libglapi-mesa libglapi-mesa:i386 libglu1-mesa libglu1-mesa:i386 libglu1-mesa-dev libglx-mesa0 libglx-mesa0:i386 libosmesa6 libosmesa6:i386 mesa-utils mesa-utils-bin mesa-va-drivers mesa-va-drivers:i386 mesa-vdpau-drivers mesa-vdpau-drivers:i386 mesa-vulkan-drivers mesa-vulkan-drivers:i386 libvulkan1 libvulkan1:i386
```

*Restarting the system at this point is a good idea!*


## WINE staging from WineHQ

Install instructions for WINE for Ubuntu 24 (noble) and Mint 22 see [WineHQ.org](https://www.winehq.org) for others
```
sudo mkdir -pm755 /etc/apt/keyrings
sudo wget -O /etc/apt/keyrings/winehq-archive.key https://dl.winehq.org/wine-builds/winehq.key
sudo wget -NP /etc/apt/sources.list.d/ https://dl.winehq.org/wine-builds/ubuntu/dists/noble/winehq-noble.sources
sudo apt install --install-recommends winehq-staging -y
```

## Gaming setup
Install Steam, basic tools
```
sudo apt install steam-installer curl cmake git build-essential scdoc -y
```

Run Steam once so it sets up and patches, and then login.

As an alternative to Steam Proton, grab the latest GE proton (https://github.com/GloriousEggroll/proton-ge-custom), untar to ~/.steam/root/compatibilitytools.d/ (needs to be created)

## Lutris Pre-Reqs
```
sudo apt install python3-pil python3-numpy python3-build python3-hatchling python3-installer python3-yaml python3-venv cabextract
```
## Other pre-reqs
Lutris also needs vulkan-tools and fluidsynth, but the source commands may depend on your distribution. For Ubuntu 24/Mint22 however
```
sudo apt install vulkan-tools fluidsynth
```

## Git Lutris and Git umu-launcher
Now handled by the handy gittris script in this very repo!

### Automagic, assuming you trust my script
```
mkdir ~/source && cd ~/source && git clone https://github.com/jmczab/git-tris
~/source/git-tris/gittris
```

### Manual, if you don't trust my script :)
```
mkdir ~/source && cd ~/source && git clone https://github.com/lutris/lutris.git
```
Link and run Lutris once, let it install stuff and update
```
sudo ln -s ~/source/lutris/bin/lutris /usr/local/bin/lutris
lutris -d
```

Install Git umu-launcher so Lutris can access and use Steam protons - preferred over the static version, as updating git keeps them in step
```
cd ~/source && git clone https://github.com/Open-Wine-Components/umu-launcher.git
cd ~/source/umu-launcher && ./configure.sh --user-install && make 2>&1 >>/dev/null && make install
```

# Ready player tux
System is pretty much ready to install games, and game!


## Optional stuff
Fire snapd into the heart of the sun - because I hate the interface to snap and I'd rather sandbox my own stuff when required, and FlatPak is much more robust
```
sudo apt autoremove --purge snapd
```

Create a separate location for games, and use the games group for my games (setgid: not for everyone I realise, but it helps permissions) also add "myuser" to the input group (for access to certain devices, like dance mats. Yes, I still enjoy (Stepmania)[https://github.com/stepmania/stepmania])
```
sudo usermod -a -G games myuser
sudo mkdir /opt/games
sudo chown myuser:games /opt/games
sudo chmod g+s /opt/games
sudo usermod -a -G input myuser 
```

Create a separate SteamLibrary location for steam to store its games, and one for Lutris
```
mkdir /opt/games/SteamLibrary
mkdir /opt/games/lutris
```

Add /opt/games/SteamLibrary to Steam's default library location, and update Lutris preferences to use /opt/games/lutris
