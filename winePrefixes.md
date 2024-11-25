# WINE prefixes
## *What is a WINE prefix?*
In short? A directory with Windows files in it.
- **dosdevices** - symlinks mimicing drives available to the prefix
- **drive_c** - Obviously containing the C: drive contents
- **various reg files** - mimicing the registry

## *How do I create a WINE prefix?*
The most basic way is to set the variable WINEPREFIX to a new directory, then execute any wine command.
For example, this will create a new prefix
```mkdir ~/mynewprefix```
```export WINEPREFIX=~/mynewprefix```
```wine wineboot```

## *How do I interact with an existing WINE prefix?*
Again, set WINEPREIX so any WINE-related commands after that will be executed in that prefix.
So if you did
```export WINEPREFIX=/home/user/myprefix```
```wine winecfg```
...it would run the wine configuration panel in that prefix.

## *What are the WINE variables?*
WINE recognises a few variables - see 'man wine' for a full list, but most commonly: -
- WINELOADER - if you have multiple versions of WINE, set this to a specific "wine" executable as needed
- WINEPREFIX - set this to the directory of the prefix (i.e. the directory that contains dosdevices, drive_c etc.)
- WINEARCH - set to win32 or win64 as you need
- WINESERVER - set this to a specific "wineserver" executable if needed, or it will look in the PATH

## *What are the limitations on the architectures?*
The best thing is to focus on is compatibility.
A 64 bit prefix will only run 64 and 32 bit code. A 32 bit prefix will run 32 bit and 16 bit code.

For example, you might know of some 32 bit software, and decide to put it in a 64 bit prefix. However, the software comes with a launcher or service that is 16 bit, so a 32 bit prefix is the only option.
Another example is  supporting software might only come as a 16 bit installer. So, choose as you see fit, but try and select the highest possible.


## *How do I install common Windows apps into a WINE prefix?*
To be honest, the best and quickest way is to use [winetricks](https://github.com/Winetricks/winetricks)
- Winetricks allows you to easily and quickly install DLLs, common software (like .NET, or Visual C Runtime, the all-important DXVK) fonts and settings (like windows version) into any prefix.
- Winetricks is a shell script, but can run in the user context.
- Winetricks caches downloads for reuse (~/.cache/winetricks) so disk use can grow a bit without you realising!

### Example 1 - create new wineprefix then run and installer you downloaded
```mkdir -p /opt/wine/newsoftware```
```export WINEPREFIX=/opt/wine/newsoftware```
```wine ~/Downloads/software_installer.exe```

### Example 2 - create new wineprefix then install .NET 4.8 using winetricks and windows core fonts
```mkdir -p /opt/wine/appneedingdotnet48```
```export WINEPREFIX=/opt/wine/appneedingdotnet48```
```winetricks -q dotnet48 corefonts```
