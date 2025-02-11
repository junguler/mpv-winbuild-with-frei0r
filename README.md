# mpv-winbuild

[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/qwerty12/mpv-winbuild/MPV)](https://github.com/qwerty12/mpv-winbuild/actions)
[![releases](https://img.shields.io/github/v/release/qwerty12/mpv-winbuild)](https://github.com/qwerty12/mpv-winbuild/releases/latest)
[![downloads](https://img.shields.io/github/downloads/qwerty12/mpv-winbuild/total)](https://github.com/qwerty12/mpv-winbuild/releases)

Uses Github Actions to build mpv for Windows daily.

mpv is built from [my *fork* of shinchiro's mpv-winbuild-cmake](https://github.com/qwerty12/mpv-winbuild-cmake/). My [Scoop](https://scoop.sh/) bucket, [scoop-alts](https://github.com/qwerty12/scoop-alts), installs this version of mpv.

The notable changes are:

* LuaJIT has its JIT compiler enabled

    * I assume it's intentionally disabled by default because some scripts may not be compatible with it. [But not the ones I use. 😎](https://github.com/qwerty12/mpv-config) If you start seeing problems with scripts that you weren't seeing before, try adding [`jit.off()`](https://luajit.org/ext_jit.html) to the beginning of them.

* LuaSocket is built and included, so [simple-mpv-webui](https://github.com/open-dynaMIX/simple-mpv-webui) can be used out of the box

* frei0r plugins are built. See the section below for more information.

* The `mpv` here is built against the [Oniguruma](https://github.com/kkos/oniguruma) regex library to enable the use of the `sub-filter-regex` option unofficially on Windows

    * Yes, I know `sub-filter-jsre` is an option, but I would rather deal with an actual regex library than a JS engine

    * I picked Oniguruma because it's C, reasonably fast and, most importantly, provides an API that emulates the POSIX `regex.h`. PCRE2 can also do this. If there's a tangible advantage to using PCRE2, I will switch to that

    * Do not report any bugs relating to `sub-filter-regex` (maybe even subtitles in general) to the mpv team

Because the mpv builds here have been modified with unofficial patches, please see if you can reproduce any bugs you may experience with [shinchiro's pristine builds](https://github.com/shinchiro/mpv-winbuild-cmake/releases) before making a bugreport to the mpv team. Let them know you're using this dirty build at the very least.

Everything else is pretty much the same as shinchiro's builds. Thanks to https://github.com/zhongfly/mpv-winbuild/

## Auto-Builds

Builds run daily at 12:00 UTC and are automatically released on success.

On the first of every month, the cache will be automatically cleared. This ensures the underlying MinGW installation is fresh.

This repo only provides 64-bit version. If you need a 32-bit version, you can fork this repo and edit mpv.yml.

> `mpv-dev-x86_64-xxxx.7z` is 64-bit libmpv version, including the `mpv-2.dll` file.
> 
> Some media players based on libmpv use `mpv-2.dll`.You can upgrade their libmpv version by overwriting mpv-2.dll. You will need to add lua51.dll to the same folder as well.

### Release Retention Policy

-   Artifacts (from the Actions tab) are cleaned every 30 days
-   Releases that are 30 days old should be removed

## frei0r plugin support

* I do not have a need for these personally. If they cause build failures, the first thing I will try is clamping the version to a known good commit (and check a long time in the future whether the latest version can be built again). Failing that, I will disable their build entirely.

* Plugins that require Cairo, OpenCV or gavl are not built/included

* As they are ~~150 MB uncompressed~~ in the realm of 50 MB uncompressed, they are not bundled with the mpv build here proper.

    * You will need to download them separately from the [releases](https://github.com/qwerty12/mpv-winbuild/releases/latest) and extract them to a path of your choosing

    * If you grabbed an early build of the plugins, please download them again from the latest release. I had not `strip`ped the debugging symbols from them, which made up their large size

     * Most of the time, you will not need to keep downloading them with every mpv release, especially if the plugins are built from the same Git commit. If their API changes (unlikely) and mpv can't load the DLLs, then yes, download them again

* You have three choices for getting mpv to see the frei0r plugins:

    * set the [Windows environment variable](https://www.computerhope.com/issues/ch000549.htm) `FREI0R_PATH` to the full path of the folder where you extracted the plugins

    * extract the DLLs into `C:\usr\local\lib\frei0r-1\`

    * Save [main.lua](https://raw.githubusercontent.com/qwerty12/mpv-winbuild/main/scripts/frei0r_path/main.lua) into a folder called `frei0r_path` in your mpv config `scripts` folder. With this script, you can either have the frei0r-1 folder next to the mpv.exe file or next to the main.lua script itself

## Information about packages

same as [shinchiro](https://github.com/shinchiro/mpv-winbuild-cmake/blob/master/README.md#information-about-packages) [![](https://flat.badgen.net/github/last-commit/shinchiro/mpv-winbuild-cmake?cache=1800)](https://github.com/shinchiro/mpv-winbuild-cmake)

-   Git/Hg
    -   ANGLE [![ANGLE](https://flat.badgen.net/gitlab/last-commit/shinchiro/angle?scale=0.8&cache=1800)](https://gitlab.com/shinchiro/angle)
    -   FFmpeg [![FFmpeg](https://flat.badgen.net/github/last-commit/FFmpeg/FFmpeg?scale=0.8&cache=1800)](https://github.com/FFmpeg/FFmpeg)
    -   xz [![xz](https://flat.badgen.net/gitlab/last-commit/shinchiro/xz?scale=0.8&cache=1800)](https://gitlab.com/shinchiro/xz)
    -   x264 [![x264](https://flat.badgen.net/https/gitlab-latest-commit-rphv1x3zj2pi.runkit.sh/code.videolan.org/videolan/x264?scale=0.8&cache=1800)](https://code.videolan.org/videolan/x264)
    -   x265 (multilib) [![x265](https://flat.badgen.net/https/bitbucket-ft1l4pi7n5hp.runkit.sh/multicoreware/x265_git?scale=0.8&cache=1800)](https://bitbucket.org/multicoreware/x265_git)
    -   uchardet [![uchardet](https://flat.badgen.net/github/last-commit/freedesktop/uchardet?scale=0.8&cache=1800)](https://github.com/freedesktop/uchardet)
    -   rubberband [![rubberband](https://flat.badgen.net/github/last-commit/lachs0r/rubberband?scale=0.8&cache=1800)](https://github.com/lachs0r/rubberband)
    -   opus [![opus](https://flat.badgen.net/github/last-commit/xiph/opus?scale=0.8&cache=1800)](https://github.com/xiph/opus)
    -   openal-soft [![openal-soft](https://flat.badgen.net/github/last-commit/kcat/openal-soft?scale=0.8&cache=1800)](https://github.com/kcat/openal-soft)
    -   mpv [![mpv](https://flat.badgen.net/github/last-commit/mpv-player/mpv?scale=0.8&cache=1800)](https://github.com/mpv-player/mpv)
    -   luajit [![luajit](https://flat.badgen.net/github/last-commit/openresty/luajit2/v2.1-agentzh?scale=0.8&cache=1800)](https://github.com/openresty/luajit2)
    -   luasocket [![luasocket](https://flat.badgen.net/github/last-commit/lunarmodules/luasocket?scale=0.8&cache=1800)](https://github.com/lunarmodules/luasocket)
    -   libvpx [![libvpx](https://flat.badgen.net/github/last-commit/webmproject/libvpx?scale=0.8&cache=1800)](https://chromium.googlesource.com/webm/libvpx)
    -   libwebp [![libwebp](https://flat.badgen.net/github/last-commit/webmproject/libwebp?scale=0.8&cache=1800)](https://chromium.googlesource.com/webm/libwebp)
    -   libpng [![libpng](https://flat.badgen.net/github/last-commit/glennrp/libpng?scale=0.8&cache=1800)](https://github.com/glennrp/libpng)
    -   libsoxr [![libsoxr](https://flat.badgen.net/gitlab/last-commit/shinchiro/soxr?scale=0.8&cache=1800)](https://gitlab.com/shinchiro/soxr)
    -   libzimg [![libzimg](https://flat.badgen.net/github/last-commit/sekrit-twc/zimg?scale=0.8&cache=1800)](https://github.com/sekrit-twc/zimg)
    -   libdvdread [![libdvdread](https://flat.badgen.net/https/gitlab-latest-commit-rphv1x3zj2pi.runkit.sh/code.videolan.org/videolan/libdvdread?scale=0.8&cache=1800)](https://code.videolan.org/videolan/libdvdread)
    -   libdvdnav [![libdvdnav](https://flat.badgen.net/https/gitlab-latest-commit-rphv1x3zj2pi.runkit.sh/code.videolan.org/videolan/libdvdnav?scale=0.8&cache=1800)](https://code.videolan.org/videolan/libdvdnav)
    -   libdvdcss [![libdvdcss](https://flat.badgen.net/https/gitlab-latest-commit-rphv1x3zj2pi.runkit.sh/code.videolan.org/videolan/libdvdcss?scale=0.8&cache=1800)](https://code.videolan.org/videolan/libdvdcss)
    -   libudfread [![libdvdcss](https://flat.badgen.net/https/gitlab-latest-commit-rphv1x3zj2pi.runkit.sh/code.videolan.org/videolan/libudfread?scale=0.8&cache=1800)](https://code.videolan.org/videolan/libudfread)
    -   libbluray [![libbluray](https://flat.badgen.net/https/gitlab-latest-commit-rphv1x3zj2pi.runkit.sh/code.videolan.org/videolan/libbluray?scale=0.8&cache=1800)](https://code.videolan.org/videolan/libbluray)
    -   libass [![libass](https://flat.badgen.net/github/last-commit/libass/libass?scale=0.8&cache=1800)](https://github.com/libass/libass)
    -   libmysofa [![libmysofa](https://flat.badgen.net/github/last-commit/hoene/libmysofa?scale=0.8&cache=1800)](https://github.com/hoene/libmysofa)
    -   lcms2 [![lcms2](https://flat.badgen.net/github/last-commit/mm2/Little-CMS?scale=0.8&cache=1800)](https://github.com/mm2/Little-CMS)
    -   lame [![lame](https://flat.badgen.net/gitlab/last-commit/shinchiro//lame?scale=0.8&cache=1800)](https://gitlab.com/shinchiro/lame)
    -   harfbuzz [![harfbuzz](https://flat.badgen.net/github/last-commit/harfbuzz/harfbuzz/main?scale=0.8&cache=1800)](https://github.com/harfbuzz/harfbuzz)
    -   game-music-emu [![game-music-emu](https://flat.badgen.net/https/bitbucket-ft1l4pi7n5hp.runkit.sh/mpyne/game-music-emu?scale=0.8&cache=1800)](https://bitbucket.org/mpyne/game-music-emu)
    -   freetype2 [![freetype2](https://flat.badgen.net/gitlab/last-commit/shinchiro/freetype2?scale=0.8&cache=1800)](https://gitlab.com/shinchiro/freetype2)
    -   mujs [![mujs](https://flat.badgen.net/github/last-commit/ccxvii/mujs?scale=0.8&cache=1800)](https://github.com/ccxvii/mujs)
    -   libarchive [![libarchive](https://flat.badgen.net/github/last-commit/libarchive/libarchive?scale=0.8&cache=1800)](https://github.com/libarchive/libarchive)
    -   libjpeg [![libjpeg](https://flat.badgen.net/github/last-commit/libjpeg-turbo/libjpeg-turbo/main?scale=0.8&cache=1800)](https://github.com/libjpeg-turbo/libjpeg-turbo)
    -   shaderc (with [spirv-headers](https://github.com/KhronosGroup/SPIRV-Headers), [spirv-tools](https://github.com/KhronosGroup/SPIRV-Tools), [glslang](https://github.com/KhronosGroup/glslang)) [![shaderc](https://flat.badgen.net/github/last-commit/google/shaderc/main?scale=0.8&cache=1800)](https://github.com/google/shaderc)
    -   vulkan-header [![Vulkan-Headers](https://flat.badgen.net/github/last-commit/KhronosGroup/Vulkan-Headers/main?scale=0.8&cache=1800)](https://github.com/KhronosGroup/Vulkan-Headers)
    -   vulkan [![Vulkan](https://flat.badgen.net/github/last-commit/KhronosGroup/Vulkan-Loader?scale=0.8&cache=1800)](https://github.com/KhronosGroup/Vulkan-Loader) 
    -   spirv-cross [![spirv-cross](https://flat.badgen.net/github/last-commit/KhronosGroup/SPIRV-Cross?scale=0.8&cache=1800)](https://github.com/KhronosGroup/SPIRV-Cross)
    -   fribidi [![fribidi](https://flat.badgen.net/github/last-commit/fribidi/fribidi?scale=0.8&cache=1800)](https://github.com/fribidi/fribidi)
    -   frei0r [![frei0r](https://flat.badgen.net/github/last-commit/dyne/frei0r?scale=0.8&cache=1800)](https://github.com/dyne/frei0r)
    -   nettle [![nettle](https://flat.badgen.net/gitlab/last-commit/shinchiro/nettle?scale=0.8&cache=1800)](https://gitlab.com/shinchiro/nettle)
    -   libxml2 [![libxml2](https://flat.badgen.net/https/gitlab-latest-commit-rphv1x3zj2pi.runkit.sh/gitlab.gnome.org/GNOME/libxml2?scale=0.8&cache=1800)](https://gitlab.gnome.org/GNOME/libxml2)
    -   amf-headers [![amf-headers](https://flat.badgen.net/github/last-commit/GPUOpen-LibrariesAndSDKs/AMF?scale=0.8&cache=1800)](https://github.com/GPUOpen-LibrariesAndSDKs/AMF/tree/master/amf/public/include)
    -   avisynth-headers [![avisynth-headers](https://flat.badgen.net/github/last-commit/AviSynth/AviSynthPlus?scale=0.8&cache=1800)](https://github.com/AviSynth/AviSynthPlus)
    -   nvcodec-headers [![nvcodec-headers](https://flat.badgen.net/github/last-commit/FFmpeg/nv-codec-headers?scale=0.8&cache=1800)](https://git.videolan.org/?p=ffmpeg/nv-codec-headers.git)
    -   libmfx [![libmfx](https://flat.badgen.net/github/last-commit/lu-zero/mfx_dispatch?scale=0.8&cache=1800)](https://github.com/lu-zero/mfx_dispatch)
    -   [aom](https://aomedia.googlesource.com/aom/)
    -   dav1d [![dav1d](https://flat.badgen.net/https/gitlab-latest-commit-rphv1x3zj2pi.runkit.sh/code.videolan.org/videolan/dav1d?scale=0.8&cache=1800)](https://code.videolan.org/videolan/dav1d/)
    -   libplacebo (with [libepoxy](https://github.com/anholt/libepoxy)) [![libplacebo](https://flat.badgen.net/github/last-commit/haasn/libplacebo?scale=0.8&cache=1800)](https://github.com/haasn/libplacebo)
    -   fontconfig [![uchardet](https://flat.badgen.net/github/last-commit/freedesktop/fontconfig?scale=0.8&cache=1800)](https://github.com/freedesktop/fontconfig)
    -   libbs2b [![libbs2b](https://flat.badgen.net/github/last-commit/alexmarsev/libbs2b?scale=0.8&cache=1800)](https://github.com/alexmarsev/libbs2b)
    -   [libssh](https://git.libssh.org/projects/libssh.git)
    -   libsrt [![libsrt](https://flat.badgen.net/github/last-commit/Haivision/srt?scale=0.8&cache=1800)](https://github.com/Haivision/srt)
    -   libmodplug [![libmodplug](https://flat.badgen.net/github/last-commit/Konstanty/libmodplug?scale=0.8&cache=1800)](https://github.com/Konstanty/libmodplug)
    -   libjxl (with [brotli](https://github.com/google/brotli), [highway](https://github.com/google/highway)) [![libjxl](https://flat.badgen.net/github/last-commit/libjxl/libjxl/main?scale=0.8&cache=1800)](https://github.com/libjxl/libjxl)

-   Zip
    -   [expat](https://github.com/libexpat/libexpat) (2.4.8)
    -   [bzip](https://sourceware.org/pub/bzip2/) (1.0.8)
    -   [zlib](https://github.com/madler/zlib/) (1.2.12)
    -   [xvidcore](https://labs.xvid.com/source/) (1.3.7)
    -   [oniguruma](https://github.com/kkos/oniguruma) (6.9.8)
    -   [vorbis](https://xiph.org/downloads/) (1.3.7)
    -   [speex](https://ftp.osuosl.org/pub/xiph/releases/speex/) (1.2.0)
    -   [ogg](https://ftp.osuosl.org/pub/xiph/releases/ogg/) (1.3.5)
    -   [lzo](https://fossies.org/linux/misc/) (2.10)
    -   [libopenmpt](https://lib.openmpt.org/libopenmpt/download/) (0.6.2)
    -   [libiconv](https://ftp.gnu.org/pub/gnu/libiconv/) (1.16)
    -   [gmp](https://gmplib.org/download/gmp/) (6.2.1)
    -   [vapoursynth](https://github.com/vapoursynth/vapoursynth) (R58)
    -   [libsdl2](https://www.libsdl.org/release/) (2.0.20)
    -   [mbedtls](https://github.com/Mbed-TLS/mbedtls) (3.1.0)
    -   ~~[libressl](https://cdn.openbsd.org/pub/OpenBSD/LibreSSL/) (3.1.5)~~
