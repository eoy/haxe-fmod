# FMOD for Haxe on Windows and HTML5

**Note: This library will likely undergo many breaking changes early on**

A library to integrate the FMOD audio engine with Haxe 4 games for Windows and HTML5 deployments

Primarily focuses on simplifying the FMOD Studio project workflow through the use of a well-documented [helper library](https://tanneris.me/haxe-fmod-helper-library)

The Windows integration was built on top of Aaron Shea's [C++ integration with FMOD's official API](https://tanneris.me/faxe)

LICENCE: [MIT](https://tanneris.me/mit-license)

Remember to follow the rules of [FMOD's license](https://tanneris.me/FMOD-License) when using this library

[Download the package via Haxelib](https://tanneris.me/haxelib)

## Table of Contents

 - [Features](#features)
 - [How to Use This Library](#how-to-use-this-library)
 - [HTML5 Builds](#html5-builds)
 - [FMOD Studio Project Configuration](#fmod-studio-project-configuration)
 - [Example Project](#example-project)
 - [Local Development](#local-development)
 - [Feature Requests and Contact](#feature-requests-and-contact)


## Features 
- FMOD in Haxe!
  - Haxe version 4.x.x
  - FMOD Studio/API version 2.00.08
- (Windows only) FMOD Studio Live Update for real-time mixing of sounds in your game (make sure to disable auto-reconnect in FMOD Studio)
- `FmodManager` library to simplify FMOD calls
- `FmodUtilities` and `FmodUpdater` libraries with additional convenience methods for `Flixel` projects
- FMOD Studio script to automatically generate a Haxe constants file (`.hx`) that can be used to reference your Music and SFX in code without using strings

## How to Use This Library

After configuring your project to work with this library, playing a song or sound effect is extremely simple:

```haxe
    override public function create():Void {
        // Plays a song in your game
        FmodManager.PlaySong(FmodSongs.MainLevel);

        // Plays a sound in your game
        FmodManager.PlaySound(FmodSFX.PlayerJump);
    }
    
    override public function update(elapsed:Float):Void {
        // Update call required by FMOD to process commands
        FmodManager.Update();
    }
```
**Important note:** HTML5 builds require a "startup scene" to load FMOD before the game starts. See the [HTML5 Builds](#html5-builds) section for more information

**Download FMOD Studio and setup your project:**

This will be the tool you use to manage all audio for your game. Download FMOD Studio version 2.00.08 [here](https://tanneris.me/fmod-downloads). Once installed, follow the [FMOD Studio Project Configuration](#fmod-studio-project-configuration) section before moving on.

**Download the FMOD API:**

To run FMOD in your game, you need get FMOD Studio API version 2.00.08. You will have to download the Windows integration and HTML5 integration separately. The Windows API will be installed and the HTML5 API will come in a zip. They can both be found [here](https://tanneris.me/fmod-downloads) under the "FMOD Studio" dropdown. 

Windows:

Once installed, find the `FMOD Studio API Windows` folder in the FMOD Studio API's installation directory. Take the entire `api` folder and copy it into the `lib/Windows` directory of the `haxefmod` library. If you installed this library using `haxelib`, you can find the location of the library on your computer by running `haxelib config`. Once the `api` folder is copied into the `haxefmod` project, the path to the API folder should look like this: `haxefmod/lib/Windows/api`.

HTML5:

The HTML5 API will come in the form of a zip folder. Unzip it and move the `api` folder into `haxefmod/lib/html5` similarly to the process of installing the Windows API.

**Add the library your Haxe project:**

[Download the package via Haxelib](https://tanneris.me/haxelib)

If required, import the library in your project. On HaxeFlixel projects, add `<haxelib name="haxefmod" />` to the "Libraries" section of your `Project.xml` file

**Use the library in code:**

Create an `imports.hx` file next to your game's `Main.hx` and put the FMOD imports in it to make the helpers globally available.

`imports.hx`:
```haxe
// Fmod helper library
import haxefmod.FmodManager;
// Static class containing all sound effect names
import FmodConstants.FmodSFX;
// Static class containing all song names
import FmodConstants.FmodSongs;
```

The `FmodManager` class is what you should be using most of the time. It abstracts away nearly all of the low-level details of interacting with the FMOD API.

The `FmodSFX` and `FmodSongs` classes are auto-generated classes containing all event names in your FMOD studio bank file (see the [fmod-scripts](https://tanneris.me/haxe-fmod-scripts) folder in this repo)

**important**: The FMOD engine needs a constant stream of update calls to function properly. If this is not present in your game, it will seem like the engine is either buggy or simply not working at all. Remember to call `FmodManager.Update()` at the beginning of **every** update loop of **every** state in your game.

**Global library settings:**

Global settings for `haxefmod` are in a `Settings.hx` file found in the installation location of this library. The relative location of this file from the root of the library is `haxefmod/settings/Settings.hx`. Updating this library to newer versions will likely reset all global settings to their defaults.

Settings available:
```Haxe
DebugMessages //Bool: Enables console output for internal FMOD API calls (can be helpful if things aren't working)
```

## HTML5 Builds

For HTML5 builds to work, a dedicated scene must be run before the game starts to give the FMOD engine a chance to fully load. See the [EZPlatformer example project](./example-project/EZPlatformer/source) for a demonstration of how to handle this. The `Main.hx` file loads the startup scene, waits for FMOD to initialize, then starts the game.

## FMOD Studio Project Configuration

**FMOD Studio project structure**:

- Songs should be placed inside a folder titled "Music" in your FMOD Studio project
- Sound effects should be placed inside a folder titled "SFX" in your FMOD Studio project

**FMOD Studio bank builds**:

This library only supports loading a single master bank for all sounds.

Set your FMOD Studio project to build banks to the correct location:

- Create an `fmod` folder in your `assets` folder (so the path `assets/fmod/` exists in your project) 
- Open up your FMOD Studio project and at the top of the window, click Edit->Preferences, then click the "Build" tab on the window that pops up.
- Under "Built banks output directory (optional)", click browse and navigate to the new `fmod` folder and select it.

From now on, your `Master.bank` and `Master.strings.bank` files should be built in a folder found at `assets/fmod/Desktop` (the Desktop folder is created by FMOD Studio). 

**FMOD Studio Scripts:**

Checkout the [fmod-scripts](https://tanneris.me/haxe-fmod-scripts) folder in this repo to learn how to setup FMOD Studio to generate a Haxe constants file (`.hx`) that can be used to reference your Music and SFX in code without using strings.

**FMOD Studio Live Update:**

When using Live Update in FMOD Studio, turn the auto-reconnect feature off or your game will not start. Hopefully that issue can be resolved fairly easily.

**Note**: Unfortunately, the Live Update feature is officially not available for HTML5 builds. The FMOD team said this has to do with the game running inside the browser.

## Example Project

Inside the `example-project` folder, you will find a simple game from the [HaxeFlixel flixel-demos repo](https://tanneris.me/haxe-flixel-demos) with this FMOD library added to it. It showcases the following:
- A song with an additional layer that uses an event parameter controlled by how fast the player can collect coins.
- A coin sound with multiple versions that are automatically randomized
- A dynamic high-pass filter that is applied to the song when the game is paused (click off the screen to pause)

The FMOD Studio project for the example game is also included.

Play the game, explore the code, and open up the FMOD Studio project (try Live Update!). This will provide insight into the workflow, library calls, and features of this tool. Open the `EZPlatofmrer` folder directly with vscode to get autocomplete and function lookups as you look around the code.

To play the game, run `lime test windows` or `lime test html5` in the `EZPlatformer` folder

## Local Development

1. Make sure `haxefmod` is not installed on your system by checking the output of `haxelib list`. If it _is_ installed, you can uninstall it using `haxelib remove haxefmod`
2. Clone down this repo
3. Point your `haxelib` at the local repo using `haxelib dev haxefmod <directory_to_the_git_clone>`

This will setup the git repo as an "installed" version of `haxefmod` which can be imported by your projects the same way you import other libraries. You can see the special `dev` status when you find `haxefmod` in the output of `haxelib list` 

## Feature Requests and Contact

If you have any feature requests or are having issues using the library, please [open an Issue](https://tanneris.me/haxe-fmod-issues) here on Github
