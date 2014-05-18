---
layout: post
title: "Building OpenFOAM 2.3.0 on OS X"
category: posts
---

Recently [OpenFOAM Foundation](http://www.openfoam.org/) announced release of
[OpenFOAM 2.3.0](http://www.openfoam.org/version2.3.0/). So
I've decided to make patches to build this version with Clang.

Notes:

1. Build process was tested on OS X 10.9 with Clang version: Apple LLVM version
   5.0 (clang-500.2.79) (based on LLVM 3.3svn)
2. In this version CGAL library appeared which depends on
   [boost](http://www.boost.org/), [gmp](https://gmplib.org/),
   [mpfr](http://www.mpfr.org/), and
   [cmake](http://cmake.org/). I use [Homebrew](http://brew.sh/) as a package manager so patches assume that boost,
   gmp, and mpfr are installed with brew. Basically I use this package manager
   to get installation prefixes for the libraries.
3. I have multi-threaded build of boost intalled (--without-single option of
   the package) so build was tested with this version (i.e. libraries have -mt
   suffix). If you have single threaded libraries installed makeCGAL script
   will try to adjust build options, though this wasn't tested.
4. Paraview is downloaded from [paraview.org](http://paraview.org/) and
   installed in /Applications

Instruction is rather similar to the [previous one](/posts/2013/12/22/building-openfoam-222-on-os-x-with-clang/):

1. Download OpenFOAM sources from [openfoam.org](http://openfoam.org/):
   [OpenFOAM-2.3.0.tgz](http://downloads.sourceforge.net/foam/OpenFOAM-2.3.0.tgz)
   and
   [ThirdParty-2.3.0.tgz](http://downloads.sourceforge.net/foam/ThirdParty-2.3.0.tgz).
2. Download patches:
   [OpenFOAM-2.3.0-2.patch](https://dl.dropboxusercontent.com/u/2169907/OpenFOAM-2.3.0-2.patch)
   and
   [ThirdParty-2.3.0-1.patch](https://dl.dropboxusercontent.com/u/2169907/ThirdParty-2.3.0-1.patch)
3. Create disk image with CASE SENSITIVE file system. One can find instructions
   with pictures in [OpenFOAM wiki](http://openfoamwiki.net/index.php/Installation/Mac_OS/OpenFOAM_2.2.2).
4. Mount created disk image to `$HOME/OpenFOAM` (for example, it can be done with
   command `hdiutil attach -quiet -mountpoint $HOME/OpenFOAM <your disk image
   file>`).
5. Unpack source archives to `$HOME/OpenFOAM` (if you downloaded files elsewhere, correct paths to archives)

         $ cd $HOME/OpenFOAM
         $ tar xzf ~/Downloads/OpenFOAM-2.3.0.tgz
         $ tar xzf ~/Downloads/ThirdParty-2.3.0.tgz

6. Copy patches into correspondent directories:

         $ cp ~/Downloads/OpenFOAM-2.3.0-2.patch OpenFOAM-2.3.0/
         $ cp ~/Downloads/ThirdParty-2.3.0-1.patch ThirdParty-2.3.0/

7. Apply patches with `git apply` command. I prefer using `git` (instead of `patch`)
   for this because git will get permissions on created files (for example
   `addr2line4Mac.py` will be created as an executable).

          $ cd OpenFOAM-2.3.0
          $ git apply OpenFOAM-2.3.0-2.patch
          $ cd ../ThirdParty-2.3.0
          $ git apply ThirdParty-2.3.0-1.patch
          $ cd ../OpenFOAM-2.3.0

8. Edit `etc/bashrc` to correspond to your system and desires. Major changes that
   can be done are:
   - Change `export WM_MPLIB=SYSTEMOPENMPI` to `export WM_MPLIB=OPENMPI` if you'd
     like to use OpenMPI from ThirdParty source distribution (version 1.6.5).
     I use Homebrew installed OpenMPI 1.7.4.
9. Source `etc/bachrc`
10. Execute `./Allwmake > log.Allwmake 2>&1`
11. Wait.

Build process will take some time, after you can test installation with
traditional:

```sh
$ mkdir -p $FOAM_RUN 
$ run
$ cp -r $FOAM_TUTORIALS/incompressible/icoFoam/cavity .
$ cd cavity
$ blockMesh
$ icoFoam 
```

To mount disk image automatically upon every launch of terminal you can add
something like:

```sh
# OpenFOAM
if [ -f $HOME/.OpenFOAM-release ]; then
OF_VER=$(cat $HOME/.OpenFOAM-release)
if [ ! -f $HOME/OpenFOAM/OpenFOAM-$OF_VER/etc/bashrc ]; then
hdiutil attach -quiet -mountpoint $HOME/OpenFOAM OpenFOAM-$OF_VER.sparsebundle &&
. $HOME/OpenFOAM/OpenFOAM-$OF_VER/etc/bashrc
else
. $HOME/OpenFOAM/OpenFOAM-$OF_VER/etc/bashrc
fi
fi
```

to `.profile` file and create `.OpenFOAM-release` file with

```sh
$ echo '2.3.0' > $HOME/.OpenFOAM-release
```

This snippet assumes that you've names your disk image
OpenFOAM-2.3.0.sparsebundle.

That's it.
