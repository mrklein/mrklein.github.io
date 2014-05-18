---
layout: post
title: "Building OpenFOAM 2.3.0 on OS X. Tk. 2"
category: posts
---

After I've made patches for OpenFOAM 2.3.0 to build it with clang I came upon
[post by Bernhard Gshaider](http://www.cfd-online.com/Forums/openfoam-installation-windows-mac/130113-compile-2-3-mac-os-x-patch.html#post475782)
where he decided to drop ThirdParty source packs as
all necessary software can be installed with Macports. Since all software from
ThirdParty package can be installed with Homebrew (except you don't need to
install gcc with all the stuff Macports pull with it), I've made this patch.

Installation is more or less straight-forward:

1. Download [OpenFOAM-2.3.0.tgz](http://downloads.sourceforge.net/foam/OpenFOAM-2.3.0.tgz) source pack
2. Download [patch](https://bitbucket.org/mrklein/openfoam-os-x/raw/3bcf4f140189cc3e6cd5990462556165233e661c/OpenFOAM-2.3.0-3.patch).
3. Create disk image with CASE SENSITIVE file system. Guide with pictures is on
   [OpenFOAM wiki](http://openfoamwiki.net/index.php/Installation/Mac_OS/OpenFOAM_2.2.2). Usually guides suggest sparseimage format though sparsebundle
   format is more convenient for the systems with active backup software.
4. Mount created image into `$HOME/OpenFOAM` (`hdiutil attach -quiet -mountpoint $HOME/OpenFOAM <your disk image file>`)
5. Unpack source archive into `$HOME/OpenFOAM`. Assuming you've downloaded it in `$HOME/Downloads` you can do the following:

         $ cd $HOME/OpenFOAM
         $ tar xzf ~/Downloads/OpenFOAM-2.3.0.tgz

6. Copy patch into `$HOME/OpenFOAM/OpenFOAM-2.3.0` folder

         $ cd $HOME/OpenFOAM/OpenFOAM-2.3.0
         $ cp ~/Downloads/OpenFOAM-2.3.0-3.patch .

7. Apply patch with `git apply OpenFOAM-2.3.0-3.patch`
8. Install third party software. I assume you're using
   [Homebrew](http://brew.sh) as a package manager, so you need to do:

         $ brew install open-mpi gmp mpfr boost gcal

9. Now you can initialize environment variables for OpenFOAM with `$ source etc/bashrc`
(or you can first edit this file if you're installing OpenFOAM somewhere but
`$HOME/OpenFOAM/OpenFOAM-2.3.0`)
10. Now you are ready to execute `./Allwmake > log.Allwmake 2>&1`

And that's it.
