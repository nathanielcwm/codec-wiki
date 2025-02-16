---
label: AV1 Encoding for Dummies
sidebar_position: 1
---

# AV1 Encoding for Dummies

# AV1 Encoding Guide for Dummies
-> This guide will show you how to encode in AV1 *the right way*. Yes, you using standalone ``libaom``, ``libsvtav1``, and ``librav1e`` from FFmpeg or even piping ``yuv4mpeg`` into **mainline** Aomenc are all wrong. <-

![Compare](https://autumn.revolt.chat/attachments/Xe9o7rg3DayS0OZ4Xi9R-CO_e2gidBsI3MNOUhoy3y/compare-guide.webp)

# Getting Started

### Microsoft Windows

**The Easy Way:**
1. Install [Nmkoder](https://github.com/n00mkrad/nmkoder) which is a GUI front-end to av1an with all dependencies installed
2. You're done, you can skip to the next part

:::warning Outdated Software
Since Nmkoder already ships everything by default and its last release was 29th March 2022. You need to manually update EVERYTHING (av1an.exe, aomenc.exe, etc) to get performance optimizations, better speeds, and more settings. Missing out on updates will result in your encodes being sub-optimal. You can use the instructions below to help you update.
:::

**The (not very) Hard Way (Recommended):**
1. Install **Python 3.10.x, this will change so consult from the** [Vapoursynth website](http://www.vapoursynth.com/doc/installation.html) **if you're reading this from the future** from [here](https://www.python.org/downloads/windows/) and select "Windows Installer 64-bit". Upon installation check the tick for adding Python to PATH like so
![Python](https://miro.medium.com/max/1344/0*7nOyowsPsGI19pZT.png)

2. Download and install Vapoursynth from [here](https://github.com/vapoursynth/vapoursynth/releases) and select "VapourSynth64-RXX.exe"
3. Open Command Prompt/Terminal/whatever and do ``vsrepo.py install lsmas ffms2`` to install some plugins for Av1an to work
4. Download MKVToolNix from [here](https://mkvtoolnix.download/downloads.html#windows), select "mkvtoolnix-64bit-XX.X.X-setup.exe", and install
5. Download Av1an from [here](https://github.com/master-of-zen/Av1an/releases) (SELECT LATEST AND CLICK THE "ASSETS" DROPDOWN)
6. Download **shared libraries** FFmpeg from [gyan.dev](https://www.gyan.dev/ffmpeg/builds)
7. Download a pre-built fork of Aomenc ([aom-av1-lavish](https://github.com/Clybius/aom-av1-lavish/tree/Endless_Merging)) which has neat stuff such as sane defaults, new tunes, optimizations, etc. Which can be downloaded [here](https://cdn.discordapp.com/attachments/1042536514783023124/1121801826921103420/aom-lavish-opmox-patch.7z)
:::caution Updating and compiling
Join the [AV1 Discord server](https://discord.gg/vpREHAvYvh) and head to #community-builds for updated versions, you can opt to compile it yourself with those custom (Butteraugli, VMAF) tunes but its a huge PITA on Windows.
:::

8. Move Av1an, FFmpeg **(Including the DLLs)**, and Aomenc to somewhere preferable eg ``C:\Encoding``
9. Add the folder **AND MKVTOOLNIX INSTALLATION FOLDER** to the [Windows PATH environment](https://www.maketecheasier.com/what-is-the-windows-path/)


### macOS
*written by gb82*

macOS is very similar to Linux, although there aren't any GUI tools for AV1 encoding that I can comfortably recommend.

**Homebrew + Macports for Av1an + rav1e:**
*Note that some commands may have to be run with `sudo`, which I won't explicitly include for security reasons.*

Installing the Homebrew package manager is a well documented process at this point:
```zsh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

As is installing MacPorts. Install the relevent `.pkg` for your macOS version from the MacPorts Project website:
[www.macports.org/install.php](https://www.macports.org/install.php)

Now, you can run the following commands:
```zsh
brew update && brew upgrade
brew install rav1e aom mkvtoolnix ffmpeg
# Usually you must run MacPorts commands for package installations as root
port upgrade outdated
port install av1an
```

This is the easiest way to get everything set up & working to produce AV1 video with `rav1e` or mainline `aomenc` & Av1an. You can check that things are installed by running the following commands & parsing their output:
```zsh
% av1an --version
av1an 0.4.1-unstable (rev e10880d) (Release)

* Compiler
  rustc 1.70.0 (LLVM 16.0)

* Target Triple
  aarch64-apple-darwin

* Date Info
  Commit Date:  2023-06-25

* VapourSynth Plugins
  systems.innocent.lsmas : Not found
  com.vapoursynth.ffms2  : Not found
```
```zsh
% rav1e --version | grep "release" -C 1      
rav1e 0.6.6 () (release)
rustc 1.69.0 (84c898d65 2023-04-16) (built from a source tarball) aarch64-apple-darwin
```
```zsh
% aomenc --help | grep "AOMedia" -C 3

Included encoders:

    av1    - AOMedia Project AV1 Encoder 3.6.1 (default)

        Use --codec to switch to a non-default encoder.
```

Notice `systems.innocent.lsmas : Not found` in the Av1an output. This means you won't be able to use the lsmash chunking method through vapoursynth & may instead have to rely on hybrid chunking, through `-m hybrid`. This is slower & takes up disk space while encoding, but still works. A sample Av1an command with this basic installation may look like this:

```zsh
av1an -i "input" -y --resume --verbose --split-method av-scenechange -m hybrid -c mkvmerge -e rav1e --force -v " --tiles 8 -s 4 --quantizer 80 --no-scene-detection" --photon-noise 7 --chroma-noise --pix-format yuv420p10le -w 8 -o "output.mkv"
```

**Building From Source**

If you want lsmash support, aom-av1-lavish instead of mainline, or anything else that isn't covered by the more basic installation, you'll have to compile from source. Things are very similar to Linux, with a few oddities:

- macOS sometimes doesn't have a `/usr/local/bin` by default. You can fix this by doing `mkdir /usr/local/bin`.
- Homebrew installs *everything* in its own directory structure. If you're building things from source that rely on libraries from vapoursynth, zimg, lsmash, etc, make sure to copy them from `/opt/homebrew/lib` to `/usr/local/lib`. Finding them is a matter of `ls | grep "keyword"` & copying what looks reasonable to be associated with the tool you're using.
- Building most things from source will have instructions for \*nix which work for both macOS & Linux. Even if it says Linux, there's a good chance it'll work on macOS as well, & it is always worth trying Linux build instructions on Mac. I won't be going through building every encoding tool & dependency from source, as it is generally much more intuitive than Windows, but building Av1an is worth detailing here just as an example.
```zsh
brew install git rust nasm
git clone https://github.com/master-of-zen/Av1an
cd Av1an
RUSTFLAGS="-C target-cpu=native" cargo build --release
cd .. && cd target/release
cp av1an /usr/local/bin
```

**More Difficult: Building aom-av1-lavish from Source**

If you want to make the most out of your hardware & eke out every last drop of quality, it may be worth building aom-av1-lavish from source. The first step is to clone it from the Endless Merging branch:
```zsh
git clone https://github.com/Clybius/aom-av1-lavish -b Endless_Merging
cd aom-av1-lavish
```
Now, you need to make some manual changes to the source code until Clybius merges [this commit](https://github.com/Clybius/aom-av1-lavish/pull/1/files).
- Add the line `#include "aq_variance.h"` at line 19 in `av1/encoder/encodeframe_utils.c`
- Comment out line 2546 in `av1/encoder/speed_features.c`. This line is `const int qindex_thresh_cdef_sf_s1_s3_l2[2] = { 92, 48 };` & becomes `// const int qindex_thresh_cdef_sf_s1_s3_l2[2] = { 92, 48 };`.

Now you can continue to build according to the Linux instructions below. Obviously you'll need cmake, which you can install with homebrew along with any other tools you may need. While still in the `aom-av1-lavish` directory:
```zsh
mkdir -p aom_build && cd aom_build
cmake .. -DBUILD_SHARED_LIBS=0 -DENABLE_DOCS=0 -DCONFIG_TUNE_BUTTERAUGLI=0 -DCONFIG_TUNE_VMAF=0 -DCONFIG_AV1_DECODER=0 -DENABLE_TESTS=0 -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="-flto -O3 -march=native" -DCMAKE_C_FLAGS="-flto -O3 -march=native -pipe -fno-plt" -DCMAKE_LD_FLAGS="-flto -O3 -march=native"
make -j$(nproc)
# This may need to be run as root:
make install
```

Now you can run `aomenc --help | grep "AOMedia" -C 3` to see if lavish installed. If you're getting the same output as above, you may need to copy the `aomenc` executable to `/opt/local/bin`, `/usr/local/bin`, & `/opt/homebrew/bin` if you already installed mainline aomenc. Running the version info command again, the correct output should look something like this:
```zsh
% aomenc --help | grep AOMedia -C 3

Included encoders:

    av1    - AOMedia Project AV1 Encoder Psy v3.6.0 (default)

        Use --codec to switch to a non-default encoder.
```

Notice how it says `AOMedia Project AV1 Encoder Psy` instead of `AOMedia Project AV1 Encoder`. You should be all set after this to start using aom-av1-lavish & following the current parameter meta as outlined below.

### Linux

**The Easy Way:** 
Install [Aviator](https://github.com/gianni-rosato/aviator), it is currently only available as a [Flatpak](https://beta.flathub.org/apps/net.natesales.Aviator)
or
Install [rAV1ator](https://giannirosato.com/blog/post/aviator-1/), basically same thing but av1an + rav1e
**Keep in mind Aviator ships with SVT-AV1 and rAV1ator with rav1e instead of Aomenc/AOM-AV1, which I will not be covering here**

**The compiling route:**
You know what to do, so I'll just list the things you'll need: ``Vapoursynth, Av1an, FFmpeg, mkvtoolnix-cli, aom-av1-lavish, Git, Perl, CMake, Ninja, Meson, Nasm, Rust, Highway``

**Compiling aom-av1-lavish (Basic, recommended for beginners)**
``` bash
git clone https://github.com/Clybius/aom-av1-lavish -b Endless_Merging
cd aom-av1-lavish && mkdir -p aom_build && cd aom_build
cmake .. -DBUILD_SHARED_LIBS=0 -DENABLE_DOCS=0 -DCONFIG_TUNE_BUTTERAUGLI=0 -DCONFIG_TUNE_VMAF=0 -DCONFIG_AV1_DECODER=0 -DENABLE_TESTS=0 -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="-flto -O3 -march=native" -DCMAKE_C_FLAGS="-flto -O3 -march=native -pipe -fno-plt" -DCMAKE_LD_FLAGS="-flto -O3 -march=native"
make -j$(nproc)
sudo make install
```

:::note Branches
``Endless_Butter`` used to be bleeding edge but now it's been taken over by ``Endless_Merging``, be sure to check the Github page to check which is more updated!
:::

# Encoding

The moment you've all been waiting for, let's just get into it. Here's an example *recommended* parameter as of now (04/02/23):
```bash
av1an -x 300 -i input.mkv -w 4 -e aom -c mkvmerge --resume -m lsmash --photon-noise=10 --set-thread-affinity=2 --verbose -a " -an " -f " -an " -v " --bit-depth=10 --cpu-used=4 --end-usage=q --cq-level=24 --threads=2 --tile-columns=0 --tile-rows=0 --lag-in-frames=64 --tune-content=psy --tune=ssim --enable-keyframe-filtering=1 --disable-kf --kf-max-dist=9999 --enable-qm=1 --deltaq-mode=0 --aq-mode=0 --quant-b-adapt=1 --enable-fwd-kf=0 --arnr-strength=1 --sb-size=dynamic --enable-dnl-denoising=0 " -o "output.mkv"
```

:::info Parameter Meta
Again, it is strongly recommended to join the [AV1 Discord server](https://discord.gg/vpREHAvYvh) to get the latest updates on what to use and which to set, as it's the only easily reachable place for everything AV1 & encoding tips in general.
:::
Now let's dissect it one-by-one

**Av1an parameters:**

- ``-i`` Input.

- ``-x 300`` Sets scene split length to 300 frames, you can increase it for more quality at the tradeoff of video seekability.

- ``-w 4`` Specifies the amount of "workers" or amount of encoders working on the video.

- ``--verbose`` Sets logging to verbose.

- ``--resume`` Resumes the encode even when you haven't encoded yet. I strongly recommend leaving this if you resume a lot since you can accidentally delete your whole progress (There's no delete confirmation feature.. yet) if you "resumed" without the parameter in place.

- ``-e aom`` Specifies we're using aomenc encoder which should be the default option.

- ``-c mkvmerge`` Specifies we're using mkvmerge (MKVToolNix) to concatenate the parts when done, you can specify with ffmpeg if you want to but this is the best method.

- ``-m lsmash`` Specifies we're using l-smash (Vapoursynth plugin) to split the videos, this is also the best method because ffms2 causes video lag (Tested a year ago, might change now) and other methods just suck (Slow and not worth it, learned the hard way). You can attempt to use ffms2 when inputting VC-1 videos as it is not possible with l-smash (Or convert it to lossless with x264 qp 0).

- ``-f " -an "`` ``-f`` Stands for ffmpeg parameters, ``-an`` is to remove all audio since its better to encode and merge it separately. To crop use ``-f " -an -vf crop=1920:800 "`` for example to crop the video to 1920x800.

- ``-v " "`` Is where you put the encoder's parameters in.

- ``-a " -an "`` Audio options, we're removing it cause its gonna cause issues.

- ``--photon-noise=10`` AV1 grain synthesis, which is a technique where the encoder puts fake grain in so it looks more natural and potentially hiding video artifacts (cause grain is hard to encode and explodes bitrate usage because of their randomness), 5-8 for almost none to little grain, 10-14 for medium, 15+ heavy, 20+ extremely heavy, 30+ for extremely grainy 90s live action films.

- ``--set-thread-affinity=2`` Pins the thread to the encoder, aligns with ``--threads=2`` in the encoder parameter so set them accordingly.


**Aomenc parameters:**
- ``--bit-depth=10`` We're using 10bit because it makes the video smaller and reduces [banding](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Video_codecs#contouring).

- ``--cpu-used=4`` This is the preset which ranges from 0-9, you can go to 3 if you want more efficiency, 2 if you have a lot of time, 4 is the sweet spot, and 6 if you want speed. Don't go above 6 (Worst efficiency) or even 0 (It would take WEEKS to finish).

- ``--end-usage=q --cq-level=24`` This specifies that we are going to use a knockoff version of CRF level similar to x264/x265 encoders, in this case CRF 24.

- ``--tile-columns=0 --tile-rows=0`` This is the tiles options, where the encoder splits the videos into tiles to encode faster, see the image below (Yellow lines):
![Tiling](https://cdn.discordapp.com/attachments/974279294740213762/1058568425313673236/latest.png)
:::note Tile usage
Do NOT use tiles for 1080p and below, use 1 ``tile-columns`` at 1440p (2K), 2 ``tile-columns`` and 1 ``tile-rows`` for 2160p (4K)
:::

- ``--lag-in-frames=64`` Knockoff of x264/x265 [Group of Pictures](https://en.wikipedia.org/wiki/Group_of_pictures) (GOP), makes the encoder look into future framesfor better compression decision making, do not go over 64 as it is pretty much useless.
 
- ``--aq-mode`` adaptive quantization mode, 0 is better most of the time

- ``--tune-content=psy --tune=ssim`` As the name suggests they are tunes that affect the video output, for the better, and for the worst

:::info Tunes to use
Set ``tune-content`` to ``animation`` if you're encoding above ``cq-level=30`` A.K.A lower quality, despite it's name
Set ``tune-content`` to ``psy`` for everything else, **do not use if you encode above ``cq-level=30``**
For ``tune``, this is a bit tricky. For now, the meta seems to be ``ssim``, but back then it was ``lavish`` which is considered THE best tune because it's based on [butteraugli](https://github.com/google/butteraugli). Now it's fallen behind because its more blurry than ``ssim``, and before that it was ``butteraugli``, and then ``ipq_vmaf_psy``, and finally just ``ipq``. 
If you use any of the VMAF tunes, **you need to specify ``--vmaf-model-path=`` to where you put it**.
:::

- **BONUS:** Use ``--butteraugli-resize-factor=2`` if you use any of the butteraugli-based tunes to speed it up without much losses (lavish, butteraugli) and ``--butteraugli-intensity-target=250`` to match the content light level.

- ``--enable-keyframe-filtering=1`` We're setting it to 1 because of compatibility reasons, 2 is more efficient but there are seeking issues and FFmpeg for some reason can't input it.

- ``--sb-size=dynamic`` Allows the encoder to use 128x128 block partitioning besides 64x64 which gives an efficiency boost, ignore it.

- ``--deltaq-mode`` set to 0 bc its better

- **BONUS:** Use ``--arnr-maxframes`` to set max reference frames that will be used to filter the encode, higher values would make the video blurrier at high fidelity but look better at lower bitrates.

- ``--arnr-strength=1`` Controls how strong the filtering will be, 1 is good for 3D Pixar CGI-like and 2D anjmation, use 4 if you're doing live action content. Using maximum at higher bitrates would just result in a blurry mess.

- ``--disable-kf --enable-fwd-kf=0`` We're disabling keyframes cause **Av1an already did scene detection, so we wont have to.**. And it speeds things up.

- ``--kf-max-dist=9999`` Maximum keyframe interval, we're setting it at the highest possible value since av1an's scene detection keyframe interval is already 240 by default

- ``--enable-chroma-deltaq=1 --enable-qm=1 --quant-b-adapt=1`` Parameters that give you free efficiency boost, ignore it.

- ``--enable-dnl-denoising=0`` Disables the encoder's built-in denoising technique when grain synthesis is enabled, you can optionally set it to 1 when you have a pretty noisy video since it works quite well

- **OPTIONAL:** ``--denoise-noise-level=10`` Alternative to ``photon-noise``, slower than photon-noise and is the OG grain synthesis method, performs okay and just serves as an alternative. Don't attempt to use it at high values (>12) since it creates noticeable grain patterns.


:::info Concatenation Error on Linux
Run ``ulimit -n 200000``, resume, and it should concatenate just fine. If it still errors, head to the encode directory > encode, and run ``mkvmerge @../options.json``
:::


**Merging Everything**

Once you're done just encode your audio using ffmpeg (or just passthrough it), subtitles should be carried along with your video output, and merge them in MKVToolNix! Don't want Matroska files? That's fine, you can use FFmpeg or MP4Box to output into .mp4, just keep in mind that PGS/SUP/VOBSUB subtitles are not supported and Opus audio support is highly experimental.


# Final Thoughts

-> Encoding has always been about experimentation for the best, there is really no "One size fits all" for encoding content, as they differ from scene complexity, how it's captured (2D/Real life), film grain, dark scenes, etc. So experiment away for your specific type of content! <-

> ***Guide created by Simulping || simulping. on Discord and u/SimultaneousPing on Reddit.***