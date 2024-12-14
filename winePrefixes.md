# WINE prefixes
## *What is a WINE prefix?*
In short? A directory with Windows files in it.
- **dosdevices** - symlinks mimicing drives available to the prefix
- **drive_c** - Obviously containing the C: drive contents
- **various reg files** - mimicing the registry and storing Windows settings

## *How do I create a WINE prefix?*
The most basic way is to set the variable WINEPREFIX to a new directory, then execute any wine command.
For example, this will create a new prefix
```
mkdir ~/mynewprefix
export WINEPREFIX=~/mynewprefix
wine wineboot
```

## *How do I interact with an existing WINE prefix?*
Again, set WINEPREIX so any WINE-related commands after that will be executed in that prefix.
So if you did
```
export WINEPREFIX=/home/user/myprefix
wine winecfg
```
...it would run the wine configuration panel in that prefix using the system WINE.

## *How do I use a specific version of WINE with a WINE prefix*
If you have multiple versions of WINE, then things can be confusing - every time you interact with a prefix, the version of WINE you run will reconfigure it to its "expected" settings. Typically this isn't a problem, but you may also need a prefix using a very old WINE version that doesn't play well when updated by the latest.
```
export WINEPREFIX=/home/user/myprefix
export WINELOADER=/opt/wineversion/wine7/bin/wine
$WINELOADER explorer
```

## *What are the WINE variables?*
WINE recognises a few variables - see 'man wine' for a full list, but most commonly: -
- WINELOADER - if you have multiple versions of WINE, set this to a specific "wine" executable as needed
- WINEPREFIX - set this to the directory of the prefix (i.e. the directory that contains dosdevices, drive_c etc.)
- WINEARCH - set to win32 or win64 as you need
- WINESERVER - set this to a specific "wineserver" executable if needed, or it will look in the PATH

## *What are the limitations on the architectures?*
The best thing is to focus on is compatibility.
A 64 bit prefix will only run 64 and 32 bit code. A 32 bit prefix will run 32 bit and 16 bit code. See WINEARCH variables above!

For example, you might know of some 32 bit software, and decide to put it in a 64 bit prefix. However, the software comes with a launcher or service that is 16 bit, so a 32 bit prefix is the only option.
Another example is  supporting software might only come as a 16 bit installer. So, choose as you see fit, but try and select the highest possible.


## *How do I install common Windows apps into a WINE prefix?*
To be honest, the best and quickest way is to use [winetricks](https://github.com/Winetricks/winetricks)
- Winetricks allows you to easily and quickly install DLLs, common software (like .NET, or Visual C Runtime, the all-important DXVK) fonts and settings (like windows version) into any prefix.
- Winetricks is a shell script, but can run in the user context.
- Winetricks caches downloads for reuse (~/.cache/winetricks) so disk use can grow a bit without you realising!

## What is a DLL override and why do I need it?
WINE provides replacement functions for a LOT of Windows DLLs, but some software needs the original file. The use of these is determined by the DLL overrides.
If you run the configuration tool (wine winecfg) you can see the overrides under the "Libraries" tab. 

Each entry corresponds to a DLL, with various options of native/builtin. This choice just means "use the DLL on the disk"/"use the wine DLL" when the DLL is used.
These can be set on the command line, or by winetricks

## I created a prefix, but I am getting a prompt for something called mono/gecko?
### Mono
When you installed WINE, it will also have pulled a package called "mono-runtime" or similar.
Mono is a replacement for .NET in WINE prefixes and installing it in a new prefix does not cause any issues. The prompt usually a new version and is updating its runtimes.

However, when a game dependant on .NET runs into issues, you can replace mono with a full fat .NET install using winetricks. This will automatically remove mono, if installed.

### Gecko
Gecko is another compatibility layer - Because Windows integrates internet exporer with its OS, WINE uses its own layer. Gecko is an extended compatibility layer, but is rarely needed for games.

## *My Windows application does not support my Linux native language*
Old Windows software typically relies on the system language being some variant of US English. However, if you don't use English and have a non-default keyboard, this can be a problem. Generally, the variables are stored in the registry under HKEY_CURRENT_USER\Control Panel\International

You can set the LANG environment variable before launching WINE, which will cause WINE to rewrite some of the Windows system variables. 
```
export WINEPREFIX=~/winelangages
LANG=en_US.UTF-8 wine regedit
```

**Note: Language settings will get reset every time you run a WINE command in this prefix without the same LANG set**

You can make changes in the registry - like the default date formats, again, using the same LANG setting so it persists, then use regedit to view them, then launch the exe.
```
LANG=en_US.UTF-8 wine reg add "HKCU\Control Panel\International" /v sLongDate /t REG_SZ /d "ddd, MMM d, yyyy" /f
LANG=en_US.UTF-8 wine regedit
LANG=en_US.UTF-8 wine $WINEPREFIX/drive_c/MySoftware/application.exe
```


# Examples

### Example 1 - create new wineprefix, then run and installer you downloaded
```
mkdir -p /opt/wine/newsoftware
export WINEPREFIX=/opt/wine/newsoftware
wine wineboot
wine ~/Downloads/software_installer.exe
```

### Example 2 - create new wineprefix then install .NET 4.8 using winetricks and windows core fonts - quietly and unattended
```
mkdir -p /opt/wine/appneedingdotnet48
export WINEPREFIX=/opt/wine/appneedingdotnet48
winetricks -q dotnet48 corefonts
```

### Example 3 - create new 32 bit wineprefix
```
mkdir -p /opt/wine/wine32bit
export WINEPREFIX=/opt/wine/wine32bit
export WINEARCH=win32
wine wineboot
```

### Example 4 - I'm running 4k screen and the fonts are TINY
```
export WINEPREFIX=/opt/wine/myfontsaretiny
wine winecfg
```
Go into the "graphics" tab and move the slider - I find 144 or 168 acceptable
