# Perilous endeavours
Getting Git Lutris and games running quickly on EndeavourOS didn't prove to be too challenging for someone with a Fedora/Debian Linux background. That said, there are quirks.

## Choice of deskop system
I picked Cinnamon. I like cinnamon. It's pretty tidy, works with Wayland, and works with Lutris.

## Driver installation
After much browsing of OS notes and self determination - Install the nvidia install tool, then run it
```
pacman -S nvidia-inst
nvidia-inst (to install nvidia drivers over nouveau)
```

## Why must you always WINE, Louis?
Staging is always a better bet IMO. A bit behind winehq builds, but take we can get.
```
pacman -S wine-staging
```

## Lutris support packages
Basic stuff really - stuff Lutris (and winetricks) needs
```
pacman -S git 7zip vulkan-tools cabextract
```

## Python packages
After some poking - Arch didn't go for python/python3 foolery, so its just a case of determining the equivalent package names
```
pacman -S python-numpy python-build python-hatchling python-installer python-yaml python-virtualenv python-pip python-uv python-lxml python-certifi
```

## Lutris install
Just cloned it from git, as per my usual M.O.

## Lutris integration
Lutris normally fixes up the clickable Firefox link integration for lutris: links, but it was broken. Had to manually put it in my profile's handlers.json

## Game installation failures!
1. vulkan failure using wine (causes error 256)
I tried the following: -
- amdvlk lib32-amdvlk - nope
- vkd3d lib32-vkd3d - yes!

2. Error 256 after game install
This was due to winetricks requiring cabextract. Normally I install it, but forgot. It's now in the pre-reqs above.

3. Game failed to launch with a dxvk error. Had to work out the packages required...oh look, we're missing 32 bit.
```
pacman -S lib32-nvidia-utils
```
and remove these just to be sure
```
pacman -R amdvlk lib32-amdvlk
```

# Game on
That was it really. A couple of hours and I now know EndeavourOS (yes, I know it's got a "u" in it) is a reasonable platform to do Lutris in a hurry.
