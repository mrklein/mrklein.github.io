---
layout: post
title: "Building OpenFOAM 2.2.2 on OS X with clang"
category: posts
---

There are several instructions for building OpenFOAM on OS X ([1](http://www.cfd-online.com/Forums/openfoam-installation-windows-mac/114265-patches-compile-openfoam-2-2-mac-os-x.html), [2](http://openfoamwiki.net/index.php/Installation/Mac_OS/OpenFOAM_2.2.2)). They
assume that you are using [Macports](http://www.macports.org/) which was not a case with me. So I've
decided to build OpenFOAM with [clang](http://clang.llvm.org/) as it comes with developer tools. My
patches are based upon ones by [Bernhard Gschaider](http://www.cfd-online.com/Forums/members/gschaider.html), basically I've applied his
patches, removed Macports stuff and added clang stuff. Patches and build
process was tested on OS X 10.9 with clang --version:

```
Apple LLVM version 5.0 (clang-500.2.79) (based on LLVM 3.3svn)
Target: x86_64-apple-darwin13.0.0
Thread model: posix
```

So, to build OpenFOAM you need:

1. Download OpenFOAM and Third-Party source packs [here](http://openfoam.org/download/source.php).
2. Create a disk image with Disk Utility. One needs this image to create case sensitive file system (see [1](http://www.cfd-online.com/Forums/openfoam-installation-windows-mac/114265-patches-compile-openfoam-2-2-mac-os-x.html) or [2](http://openfoamwiki.net/index.php/Installation/Mac_OS/OpenFOAM_2.2.2) for instructions with pictures).
3. Download patches for [OpenFOAM](https://dl.dropboxusercontent.com/u/2169907/OpenFOAM-2.2.2-1387556919.patch) and [Third-Party](https://dl.dropboxusercontent.com/u/2169907/ThirdParty-2.2.2-1387556964.patch) source packs. They assume you are building 64-bit version of the code as there're no wmake rules for 32-bit compilation.
4. Mount created disk image to `$HOME/OpenFOAM`
5. Unpack source packs to `$HOME/OpenFOAM`
6. Copy patch files to source folders (OpenFOAM-2.2.2-1387556919.patch to OpenFOAM-2.2.2, ThirdParty-2.2.2-1387556964.patch to ThirdParty-2.2.2).
7. Apply patches with git apply [patch file name] command. In addition to changing contents of the files git sets modes so you won't need to do `chmod 0755 [file]` on executable files created by the patches.
8. Edit `$HOME/OpenFOAM/OpenFOAM-2.2.2/ets/bashrc` file to correspond to your system. I've tested build with OPENMPI (1.6.3, in ThirdParty source pack), and with SYSTEMOPENMPI (1.7.3, installed with [Homebrew](http://brew.sh/), I guess if you're using [Macports](http://www.macports.org/) you'll need to modify wmake rules).
9. Source bashrc with `source etc/bashrc`
10. Now you can execute Allwmake script to build OpenFOAM.

To mount disk image and source OpenFOAM's `bashrc` file automatically each time
you launch Terminal one can add following lines to `$HOME/.profile` file:

```sh
# OpenFOAM
if [ -f $HOME/.OpenFOAM ]; then
    OF_VER=$(cat $HOME/.OpenFOAM)
    if [ ! -f $HOME/OpenFOAM/OpenFOAM-$OF_VER/etc/bashrc ]; then
        hdiutil attach -quiet -mountpoint $HOME/OpenFOAM OpenFOAM-$OF_VER.dmg &&
            . $HOME/OpenFOAM/OpenFOAM-$OF_VER/etc/bashrc
    else
        . $HOME/OpenFOAM/OpenFOAM-$OF_VER/etc/bashrc
    fi
fi
```

And create `$HOME/.OpenFOAM` file with OpenFOAM version in it with:

```sh
echo '2.2.2' > $HOME/.OpenFOAM
```

Now you can test installation with standard icoFoam run:

```sh
$ mkdir -p $FOAM_RUN
$ run
$ cp -r $FOAM_TUTOTALS/incompressible/icoFoam/cavity .
$ cd cavity
$ blockMesh
$ icoFoam
```

Also maybe it's worth to move `$FOAM_RUN` folder somewhere outside of disk
image and create symlink.

That's it.
