# WINE background

## *What is WINE?*
WINE is a set of compatibility tools that allows Windows commands and operating system calls to work on Linux, mainly by mapping them to their equivalent in Linux.

## *Where to get WINE and what to install?*
The main Linux distributions provide their own WINE in their repositories, but I personally avoid using those.

My choice for pre-packaged WINE for me is also the source - (winehq.org)[https://www.winehq.org], but as with many Linux projects, you can download and compile the source yourself.

I enable 32-bit support, and use wine-staging which is in development, but not bleeding edge. Using the recommended *sudo dpkg --add-architecture i386 && sudo apt install --install-recommends winehq-staging -y* is usually enough to support everything I need.

## *What does Proton have to do with WINE?*
Proton is a version of WINE packaged and built to provide more focussed support for Windows games. A bunch of developers got together (mainly Valve, but also many notable others like "GloriousEggroll") produce and maintain it. Things like the Steam deck rely on it to run Windows titles.

## *Why do you capitalise 'WINE'?*
Well, it is an acronym for "wine is not emulation" or "wine is not an emulator" (which would be WINAE and just ...doesn't work)

It is "not an emulator" because it maps functions, rather than providing code (emulation) to do it, but there is always a line for emulation that gets broken somewhere :)

# WINE prefixes and use

## *What is a WINE prefix?*
In short? A directory with supporting files in it, so it looks like a Windows OS.
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
**Note:** I use "export WINEPREFIX" so every wine command I issue in that terminal window after that uses the same prefix.
You can also prefix the prefix to every wine command... *WINEPREFIX=~/mynewprefix wine wineboot* ...which is better for automated builds and scripts.

## *How do I interact with an existing WINE prefix?*
Again, set WINEPREIX so any WINE-related commands after that will be executed in that prefix.
So if you did
```
export WINEPREFIX=/home/user/myprefix
wine winecfg
```
...it would run the wine configuration panel in that prefix using the system WINE.

## *What are the most common WINE variables, and what is multislot-wine?*
These days, WINE is typically built as "multislot" - meaning the wine executable knows where to find its files, based on its local path. So if you invoke a specific wine command, it will pick up the relevant libaries etc.

WINE uses few variables - see 'man wine' for a full list and the specific version info below, but most commonly thanks to multislot WINE, you'll only need two: -
- WINEPREFIX - where to store windows files - set this to the directory of the prefix (i.e. the directory that contains dosdevices, drive_c etc.)
- WINEARCH - architecture - set to win32 or win64 as you need

## *How do I use a specific version of WINE with a WINE prefix, and what about non-multislot?*
If you have multiple versions of WINE, and every time you interact with a prefix, the version of WINE you run will reconfigure it to its "expected" settings. 

If you have all multislot WINE installs, this isn't much of an issue - just invoke the wine you want against the prefix.
```
WINEPREFIX=~/myprefix /opt/wineversion/wine8/bin/wine explorer
```

However, you may also have a WINE version that **isn't multislot** and doesn't play well, so will need to set *all* (or most) of the variables before running WINE commands.
```
export WINEVERPATH="/opt/my-custom-wine"
export PATH="$WINEVERPATH/bin:$PATH"
export WINESERVER="$WINEVERPATH/bin/wineserver"
export WINELOADER="$WINEVERPATH/bin/wine"
export WINEDLLPATH="$WINEVERPATH/lib/wine/fakedlls"
export LD_LIBRARY_PATH="$WINEVERPATH/lib64:$WINEVERPATH/lib:$LD_LIBRARY_PATH"
export WINEARCH=win32
export WINEPREFIX=/home/user/mycustomprefix
$WINELOADER $WINEPREFIX/company/application/software.exe
```

## *What are the limitations on architecture?*
The best thing is to focus on is compatibility, but try and select the highest possible.
A 64 bit prefix will only run 64 and 32 bit code. A 32 bit prefix will run 32 bit and 16 bit code. See WINEARCH variables above!

For example, you might know of some 32 bit software, and decide to put it in a 64 bit prefix. However, the software comes with a launcher or service that is 16 bit, so a 32 bit prefix is the only option. Another example is  supporting software might only come as a 16 bit installer.


## *How do I install common Windows apps into a WINE prefix?*
To be honest, the best and quickest way is to use [winetricks](https://github.com/Winetricks/winetricks)
- Winetricks allows you to easily and quickly install DLLs, common software (like .NET, or Visual C Runtime, the all-important DXVK) fonts and settings (like windows version) into any prefix.
- Winetricks is a shell script, but can run in the user context.
- Winetricks caches downloads for reuse (~/.cache/winetricks) so disk use can grow a bit without you realising!

## *What is a DLL override and why do I need it?*
WINE provides replacement functions for a LOT of Windows DLLs, but some software needs the original file. The use of these is determined by the DLL overrides.
If you run the configuration tool (wine winecfg) you can see the overrides under the "Libraries" tab. 

Each entry corresponds to a DLL, with various options of native/builtin. This choice just means "use the DLL on the disk"/"use the wine DLL" when the DLL is used.
These can be set on the command line, or by winetricks

## *I created a prefix, but I am getting a prompt for something called mono/gecko?*
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
mkdir ~/winelanguages
export WINEPREFIX=~/winelangages
LANG=en_US.UTF-8 wine wineboot
LANG=en_US.UTF-8 wine regedit
```

**Note:** Language settings will get reset every time you run a WINE command in this prefix without the same LANG set.

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
If you have the mono-runtime installed, this will automatically execute remove_mono so there is no conflict.

### Example 3 - create new 32 bit wineprefix
```
mkdir -p /opt/wine/wine32bit
export WINEPREFIX=/opt/wine/wine32bit
export WINEARCH=win32
wine wineboot
```
When interacting with this prefix in future, WINE will know it is 32 bit and behave accordingly.

### Example 4 - I'm running 4k screen and the Windows application fonts are TINY
```
export WINEPREFIX=/opt/wine/myfontsaretiny
wine winecfg
```
Yep, Windows dynamic DPI isn't here :) Go into the "graphics" tab and move the DPI slider - I find 144 or 168 acceptable.
