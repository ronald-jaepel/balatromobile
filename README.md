# <img src="misc/icon.png" width="60px"/> Balatro Mobile

Create a mobile version of Balatro from the Windows base version of the game. 

Python rewrite of [balatro-apk-maker](https://github.com/blake502/balatro-apk-maker) by [blake502](https://github.com/blake502) and friends. Compared to the original one, it is *NIX friendly, more modular, has patch versioning and does not try to download and install tools from the internet.

As of today, it only supports Android, but estending it to iOS should be trivial.


## Prerequisites
This script comes pre-bundled with all the tools needed to make it work. The following list of programs must be installed to make the script work:
* [Java-JRE](https://www.java.com/en/download/manual.jsp)
* [Python >= 3.11](https://www.python.org/)

Moreover, you will need a copy of the game (buy it on [Steam](https://store.steampowered.com/app/2379780/Balatro/))

## Installation
Open a terminal and run
```bash
python3 -m pip install balatromobile
```

## Usage
```bash
balatromobile android Balatro.exe
```
This command will output an APK with all needed patches and already signed. Ready to be installed on your Android device.

## Save files
If your device is running Android 12 or prior, you will find your save files in your sdcard, more specifically under:
```
/sdcard/Android/data/dev.bootkit.balatro/
```

If your device is running Android 13 or later, you cannot access directly your files from your computer. Fortunately, you can use applications which supports the Storage Access Framework API (such as [FX File Explorer](https://play.google.com/store/apps/details?id=nextapp.fx) and [Material Files](https://play.google.com/store/apps/details?id=me.zhanghai.android.files)) to access the files from your device:

[![Video tut](misc/video-thumb-1.png)](https://vimeo.com/939997099 "Click to Watch!")



If you disable the `external-storage` patch, you can browse the game files using `run-as` in `adb`, for example:
```bash
adb shell run-as dev.bootkit.balatro ls
```
This is finnicky and error prone and not reccomended.

## Patches
Every patch is versioned, allowing the upkeeping of different patches for different versions of the game.
As of today, the version check is disabled (since only one version of the game is supported anyway).
A refactor is needed to make the version system make sense.

You can list the available patches using the `list-patches` command:
```
$ balatromobile list-patches
Name              Platforms    Description                                                                                      Authors
----------------  -----------  -----------------------------------------------------------------------------------------------  ---------------------------
basic             android      Basic set of patches needed to make the game run on mobile                                       blake502,TheCatRiX,PGgamer2
nunito-font       android,ios  Replace the main font used with nunito, optimized for smaller displays. From PortMaster          nkahoang,rancossack
simple-fx         android,ios  Disable gameplay visible behind menu background, shadows, and bloom effects. From PortMaster     nkahoang,rancossack
landscape-hidpi   ios          Forces the game to always stay in landscape mode and apply hidpi fix for iOS                     blake502
external-storage  android      Save game files under /sdcard/Android [Works well for Android < 13]                              blake502
landscape         android,ios  Forces the game to always stay in landscape mode, ignoring the screeen orentation of the device  blake502
fps               android,ios  Cap the FPS limit to the FPS limit of the screen                                                 PGgamer2
no-background     android,ios  Disable background animations and effects. From PortMaster                                       nkahoang,rancossack
shaders-flames    android,ios  Fix the flames shaders for mobile                                                                PGgamer2
crt               android,ios  Disable CRT effect [Fixes blackscreen bug on Pixels and other devices]                           blake502
square-display    android,ios  Optimize for square and square-like displays. From PortMaster                                    nkahoang,rancossack
```
It is possible to specify the list of patches you want to apply by supplying a comma-separated list of patches, for example:
```bash
balatromobile android Balatro.exe --patches basic,landscape,external-storage
```

## Supported Game Versions
* 1.0.1e-FULL
* 1.0.1c-FULL

## Advanced Usage
```
$ balatromobile android -h
usage: balatromobile android [-h] [--output OUTPUT] [--patches PATCHES] [--skip-sign] [--display-name DISPLAY_NAME] [--package-name PACKAGE_NAME] BALATRO_EXE

positional arguments:
  BALATRO_EXE           Path to Balatro.exe file

options:
  -h, --help            show this help message and exit
  --output OUTPUT, -o OUTPUT
                        Output path for apk (default: balatro-GAME_VERSION.apk)
  --patches PATCHES, -p PATCHES
                        Comma-separated list of patches to apply (default: basic,landscape,crt,fps,external-storage,shaders-flames)
  --skip-sign, -s       Skip signing the apk file with Uber Apk Signer (default: False)
  --display-name DISPLAY_NAME
                        Change application display name (default: Balatro Mobile (unofficial))
  --package-name PACKAGE_NAME
                        Change application package name (default: dev.bootkit.balatro)
```

## Extended considerations on external game files for Android >= 13

One way to do it, would be to ask permission for a directory using `Intent.ACTION_OPEN_DOCUMENT_TREE`. The problem with this approach is that the current implementation uses [PhysicsFS](https://icculus.org/physfs/) and [SDL](https://www.libsdl.org/). While it could be possible at the SDL level to se the _Android API_ to open and read files using Documents, that is not the way it is used today. old-school syscalls (as far as I understand) are used by _PhysicsFS_.

I worked on a [patch of GameActivity.java](https://gist.github.com/antipatico/73f718d5b37b507b6b6dbf9bf92052e0), which correctly asks for permissions and store those permissions. I was also able to retrieve the preference (in a dirty and hacky way) in _SDL_ and convert back to a 'standard filesystem path'. The problem is, that is a really dirty implementation and it is very prone to errors.

Do you have any suggestion? Open an issue / PR!

## Credits
This software is a rewrite of [balatro-apk-maker](https://github.com/blake502/balatro-apk-maker). It uses [APKEditor](https://github.com/REAndroid/APKEditor), [Uber Apk Signer](https://github.com/patrickfav/uber-apk-signer), [Love Android](https://github.com/love2d/love-android) and [Nunito Font](https://fonts.google.com/specimen/Nunito). Moreover, some patches were ported from [nkaHong's fork of PortMaster](https://github.com/nkahoang/PortMaster-nkaHoang).

Thanks for everybody contributing to this project.