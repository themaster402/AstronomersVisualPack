# LoadingScreenManager
## A loading screen / slideshow mod for Kerbal Space Program 1.2.2
### By @paulprogart

LoadingScreenManager (LSM) is a simple plugin for KSP 1.2.2 that will
show a slideshow of custom images while KSP loads, and also allows customizing
the "witty loading tips" shown during loading.

For discussion/information/support go to the
[KSP Forum Thread](http://forum.kerbalspaceprogram.com/index.php?/topic/156064-122-loadingscreenmanager-v102-show-your-own-images-while-ksp-loads/).


## Features (v1.02)

* Show your own images while KSP loads (PNG or JPG formats, any size)
* Show multiple random images during loading as a slideshow
* Configure multiple folders within the KSP install directory (defaults to Screenshots folder)
* Use custom file masks (e.g. *.jpg, *.png) and optionally include files in subdirectories
* Customize the time each image shows for as well as the transition fade times
* Optionally include the default loading images in the slideshow
* Eliminate possible repeats
* Customize the length each "witty loading tip" shows for
* Add your own loading tips (with option to keep/discard defaults)
* BONUS:  Will dump all the tips to the log if you really want to see them all


## Change Notes

__From v1.01__

* Fixed accidentally skipping existing loaders in some cases (with predictably disastrous results).

__From v1.00__

* Removed conflicts with certain mods that install their own loaders (e.g. ModuleManager)
* Minor optimizations and typo fixes.


## Known issues:

* When KSP changes from asset to part load, the progress bar will stall and
  then jump - just a cosmetic issue; loading is not affected.
* Also when load changes, the tip will always change regardless of timing
* Tips may be repeated as KSP does not track between multiple screens which
  ones have been shown - since there are typically lots of them this will not
  normally be too noticeable
* ``fadeInTime`` is used for both fade in & fade-out, and ``fadeOutTime``
  is ignored - KSP issue, see below in configuration section


## Licence

Licence is **CC BY-NC-SA** - see here for more:  https://creativecommons.org/licenses/by-nc-sa/4.0/


## Installation

To use the mod in KSP, download the appropriate zip file from the [Releases area](https://github.com/paulprogart/KSPLoadingScreenManager/releases):

* [32-bit Windows (x86)](https://github.com/paulprogart/KSPLoadingScreenManager/releases/download/v1.02/KSP-LSM-1-02-x86.zip)
* [64-bit Windows (x64)](https://github.com/paulprogart/KSPLoadingScreenManager/releases/download/v1.02/KSP-LSM-1-02-x64.zip)
  * Use 64-bit version only if running 64-bit KSP (`KSP_x64.exe`).
  * If there are issues, try the x86 version.
* **NOTE**: Non-Windows users will have to build from source (see [below](#source)).

Unzip this to `<KSP Install Folder>/GameData`.  KSP should pick it up automatically the next time it's run.

**NOTE**: **Only KSP 1.2.2 is officially supported.**  I have no idea if the appropriate API calls are
available in earlier versions.  You can try, but if it doesn't work you're out of luck.


## Configuration

When first run, LSM will create a `LoadingScreenManager.cfg` with default settings within the
folder where it is installed.  The location may vary by OS but it should be `PlugInData\LoadingScreenManager\LoadingScreenManager.cfg`.

LoadingScreenManager uses standard KSP configuration node syntax, as is used for part .cfg files and game save files.  If you
are not familiar with this syntax, view the file generated after the first run and it should be pretty easy to infer when combined with
the info below.

The base configuration settings are as follows:

* **`debugLogging`** - If `True`, will include extra diagnostic info in the log.
    * **If you are submitting a log file with regard to a problem, please turn this on first.**
  * **NOTE**: KSP output logging must be turned on.
* **`dumpScreens`** - If `True`, dumps a list of installed screens to the log.  Mainly for development use.
  * **NOTE**: `debugLogging` must be `True` for this to work!
* **`dumpTips`** - If `True`, dumps a list of the "loading tips" for each screen to the log.
  * This is a borderline game **_SPOILER_** so it is not enabled by default.
  * **NOTE**: `debugLogging` must be `True` for this to work!
* **`totalSlides`** - Maximum number of slides to show during load (_not_ including the logo).  Unless you
    have long load times or are really pressed for memory, you can leave this at the default.
  * This value is the *maximum* number of slides KSP will show.
    If the specified folder(s) (see below) do not contain enough files, then only the
    number of files available will be used.  (Plus the original screens, if included.)
  * It is also the maximum number of images that will be loaded.  If you have a fairly short load time and want to save a bit of memory you can lower this.
  * To show only 1 slide (like current KSP), set this to 1, and set the `displayTime` to be quite large.
  * Actual # of slides shown depends on loading time and timing settings.  Typically this will be a lot less than the value here.
  * The approximate maximum slideshow length (in seconds) will be:
    * `slidesToAdd * (fadeInTime + displayTime + fadeInTime)`
  * If this is too low for your load time, the image will be blank for the last portions of loading.
* **`includeOriginalScreens`** - If `True`, includes the provided KSP screens in the slideshow.
  * Whether they are actually shown or not is random.
* **`forceSlideshowWithNoImageFiles`** - Normally LSM won't try to setup a slideshow if there are no images in the
  any of the folders, e.g. new KSP players or empty installations.  Set to
  `True` to override this behaviour.
  * **NOTE**:  This is mainly to allow showing a blank screen during loading.  Set this to `True`, use a `FOLDER` with no files (see below),
    and turn `includeOriginalScreens` off, but note _this will cause an error (non-fatal) in KSP_!
* **`displayTime`** - Time in seconds each slide is to be shown.
  * If you only want one image (like KSP shows normally), set this to a really high value.
* **`fadeInTime`** - Time in seconds for the fade-in & fade-out transitions to a new slide.
  * __NOTE__: Despite the name, KSP seems to have a bug where it uses this for both fades and ignores ``fadeOutTime`` completely.  The issue was discovered too late to rename the setting.
* **`fadeOutTime`** - Should be the time in seconds for the fade-out transition after a slide, but isn't - see above.
* **`tipTime`** - Time in seconds each "witty loading tip" is to remain showing.
  * Lower this time to see more tips, raise it to see fewer.
* **`tipsFile`** - If provided, indicates a text file to read custom tips for display during loading.  Path is relative to the KSP install folder.  Can be left blank (and is by default) to disable this feature.
  * File format is one tip per line.
  * Lines starting with `#` are treated as comments and will be ignored, as will blank lines.
  * Long tips seem to get wrapped onto multiple lines, but this probably should be avoided.  Maximum line length is probably dictated by your screen resolution.
* **`includeOriginalTips`** - If `True`, includes the provided KSP tips during loading.
  * If `tipsFile` is also provided, the original tips will be added to those from the file.  Whether they are actually shown or not is random.
  * Unlike with screens, tips are always adjusted, so if no `tipsFile` is given and this is `False` then the screen will just say "Loading..." for the entire time.

**`FOLDER` nodes** - Additionally, one or more `FOLDER` nodes should be specified with the
following settings inside.  If no `FOLDER` nodes are present, or on
first run, a default node will be added that points to the `Screenshots/` folder.
If multiples are provided, they will be pooled together.  (All files have an equal
chance of being selected for the slideshow.)

* **`path`** - The folder to pull images from, relative to the KSP install folder.  Include the trailing slash.
  * If OS and user permissions allow (note LSM only reads image files), it is
    possible to use folders outside of the KSP install folder using `../`,
    but **_this is not officially supported_**.  (If you don't know what `../` means, you aren't qualified to try this.)
* **`fileMasks`** - File mask(s) (e.g. *.jpg).  Multiples are allowed and are separated by ; (semicolon).
  * Default is `*.jpg;*.png`
  * Can be used to only include certain files, e.g. `screenshot*.png`
  *  `*` and `?` wildcards are supported and work as on Windows.
  * **NOTE**: File masks should not include anything other than JPG or PNG images!
    Otherwise you will see a bunch of errors and may see blank slides.
* **`ignoreSubfolders`** - By default, all subfolders within `path` will be
  included in the search.  Set this `True` to search _only_ `path`, ignoring
  any subfolders inside it.

**_IMPORTANT_: It is _not_ possible via configuration to alter or adjust the
developer logo loading screen that is shown initially.  Such behaviour is not
permitted by KSP forum rules.  _Don't request this ability, and don't submit
code changes that do it.  Full stop._**


## Source

If you want to build from source, be aware of the following:

* Project & solution files are provided for Visual Studio 2015 only.
  * Before building for the first time, go into the project properties
  `Build Events` tab and update the folder in the Post-build event to your own
  KSP GameData folder, otherwise you will get an error after a successful build.
  * Also you will need to fix the KSP and Unity DLL references.
  * Build configurations are provided for both x86 and x64.
* As there's only one code file, it's easy enough to create a new project (or
equivalent in other dev environments) if needed, assuming you are familiar
with building KSP Add-Ons already.
* **NOTE**: LSM is written using C# 6.0, which is not supported by older
  versions of Visual Studio or by other older editors.
* **NOTE**: LSM contains ReSharper annotation attributes, which on Windows are
  provided (accidentally IMO) by UnityEngine, so I haven't included them.  Not
  sure if they are present on other platforms, but if not you can just remove
  them to get LSM to build.  Or maybe get the JetBrains.Annotations NuGet
  package, though I haven't tried that.
