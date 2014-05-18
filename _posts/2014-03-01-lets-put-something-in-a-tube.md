---
layout: post
title: "Let's put something in a tube"
category: posts
---

## Introduction

After I've done simulations of [vortex street in 2D](/posts/2012/10/21/fun-with-openfoam-and-turbulence-models/), I kept thinking that it
would be nice to do more or less the same thing in 3D. And after watching the
movie about vortex flow meters by [Endress+Hauser](http://www.youtube.com/watch?v=GmTmDM7jHzA), I've decided it would be
really funny to put something in a tube and watch vortexes going by.

First of all we need to decide on operational parameters of the system. Let's
assume that we have 10 m long tube with diameter of 1 m and let's install
something at 2 m from inlet plane. Further calculations are based upon the
article from Wikipedia [about Kármán vortex street](http://en.wikipedia.org/wiki/Kármán_vortex_street).

Re = uD/ν

where u is flow velocity, D is characteristic length, and ν is fluid viscosity.
We'll get vortex street for Re values larger than 90. If we take, for example,
water with nu around 1000000 it's not hard to get large enough Reynolds number.
But then a question of the distance between vortexes arises. We also want our
vortexes inside the tube. Vortex shedding frequency is 

f = 0.198*u/d*(1 - 19.7/Re)

where d is cylinder diameter (correct for 250 < Re < 10^5). After playing with
tube and cylinder sizes, fluid viscosity and inflow velocities (here is
a [script](https://bitbucket.org/mrklein/tube-von-karman/raw/496fcf7eeeef4ac7b19e349c6761c0c9cc4e90c3/flowmeter.sce)) I've decided to go with the following:

```
D = 1 m
d = 0.2 m
u = 1 m/s
ν = 0.0001 m^2/s
```

## Meshing

For meshing I've used [Gmsh](http://gmsh.info/) and
[snappyHexMesh](http://www.openfoam.org/docs/user/snappyHexMesh.php). I.e. I've
created hexagonal mesh
of the tube, then I've created triangular surface mesh for the cylinder and
sphere, a nd finally I've used snappyHexMesh to cut small cylinder (or sphere)
tube.

[Mesh for tube](https://bitbucket.org/mrklein/tube-von-karman/raw/496fcf7eeeef4ac7b19e349c6761c0c9cc4e90c3/Gmsh/tube.geo)
is based upon my [previous post](/posts/2013/12/25/building-hexagonal-meshes-with-gmsh/). The addition is a parametrization
of the GEO file, i.e. one need to set tube leng th and its diameter; gmsh will
recalculate positions of the points in the mesh. GEO files for cutting shapes
([cylinder](https://bitbu%0Acket.org/mrklein/tube-von-karman/raw/496fcf7eeeef4ac7b19e349c6761c0c9cc4e90c3/Gmsh/cylinder.geo), [sphere](https://bitbucket.org/mrklein/tube-von-karman/raw/496fcf7eeeef4ac7b19e349c6761c0c9cc4e90c3/Gmsh/sphere.geo)) are even simpler cause we need only triangular surface mesh
so there's no need to split a shape into volumes with 6 sides (this operation
necessary for transfinite algorithm to work).

One of the problems I've encountered during "cutting" procedure is the need to
use more or less cubical mesh for sHM to work. In case of sphere it wan't
actually a problem, cause it lies inside "inner" part of the mesh which is
rather regular. So one can have graded mesh near the walls of the tube. That
was not so in case on cylinder. After several unsuccessful attempts to cut
a cylinder hole in graded mesh (though sHM tried to do it but wasn't able to
generate acceptable quality mesh, usually non-orthogonality on the new mesh was
too high), I've removed grading.

[snappyHexMeshDict](https://bitbucket.org/mrklein/tube-von-karman/raw/496fcf7eeeef4ac7b19e349c6761c0c9cc4e90c3/Cases/Sphere/system/snappyHexMeshDict) is quite simple, first I define the surface:

```
geometry
{
    sphere.stl
    {
        type triSurfaceMesh;
        name sphere;
    }
};
```

then I refine mesh around the surface

```
refinementRegions
{
    sphere
    {
        mode distance;
        levels ((0.05 3) (0.1 2) (0.2 1));
    }
}
```

refinement is based upon distance from the mesh. Other parameters in the
dictionary is just a copy from tutorial.

So after running mesh conversion (from gmsh to OpenFOAM) and then running
snappyHexMesh, I've got the following meshes for the simulation:

Initial tube mesh

![Whole tube mesh](https://dl.dropboxusercontent.com/u/2169907/Meshes/tube-mesh-whole.png)

with grading towards the walls

![Tube mesh zoom](https://dl.dropboxusercontent.com/u/2169907/Meshes/tube-mesh-zoom-grading.png)

without grading

![Tube mesh zoom](https://dl.dropboxusercontent.com/u/2169907/Meshes/tube-mesh-zoom.png)

Mesh with internal cylinder

![Tube with cylinder](https://dl.dropboxusercontent.com/u/2169907/Meshes/Cylinder/mesh-all.png)

![Tube with cylinder](https://dl.dropboxusercontent.com/u/2169907/Meshes/Cylinder/mesh-cut.png)

![Tube with cylinder](https://dl.dropboxusercontent.com/u/2169907/Meshes/Cylinder/mesh-cut-zoom.png)

![Tube with cylinder](https://dl.dropboxusercontent.com/u/2169907/Meshes/Cylinder/mesh-zoom-1.png)

![Tube with cylinder](https://dl.dropboxusercontent.com/u/2169907/Meshes/Cylinder/mesh-zoom-2.png)

Mesh with internal sphere

![Tube with sphere](https://dl.dropboxusercontent.com/u/2169907/Meshes/Sphere/mesh-all.png)

![Tube with sphere](https://dl.dropboxusercontent.com/u/2169907/Meshes/Sphere/mesh-cut.png)

![Tube with sphere](https://dl.dropboxusercontent.com/u/2169907/Meshes/Sphere/mesh-cut-zoom.png)

![Tube with sphere](https://dl.dropboxusercontent.com/u/2169907/Meshes/Sphere/mesh-wall-and-sphere.png)

Unlike previous time I've decided not to compare turbulence models and just selected [realizable k-epsilon](http://www.cfd-online.com/Wiki/Realisable_k-epsilon_model).

## Flow movies

After running the simulations I've got the following flow movies:

The cylinder in the tube, vorticity

<iframe width="640" height="480" src="//www.youtube.com/embed/ruQ57WkbrME?rel=0" frameborder="0" allowfullscreen></iframe>

The cylinder in the tube, velocity

<iframe width="640" height="480" src="//www.youtube.com/embed/BKZvH4e291o?rel=0" frameborder="0" allowfullscreen></iframe>

The sphere in the tube, vorticity

<iframe width="640" height="480" src="//www.youtube.com/embed/RsS4C1pdpR4?rel=0" frameborder="0" allowfullscreen></iframe>

The sphere in the tube, velocity

<iframe width="640" height="480" src="//www.youtube.com/embed/a3zclp4J_ow?rel=0" frameborder="0" allowfullscreen></iframe>

As expected after initial flow development I've got quite nice vortices going along the tube.

## Probes

Since the movies are not quite enough I've added probes along the axis of the
tube to record values of velocity, pressure, and turbulent viscosity during the
simulations (this is a fragment of controlDict):

```
functions
{
    probes
    {
        type            probes;
        functionObjectLibs ("libsampling.so");
        outputControl   timeStep;
        outputInterval  1;
        probeLocations
        (
            (2.47 0 0)
            (2.90 0 0)
            (3.5 0 0)
            (5 0 0)
            (6 0 0)
        );
        fields
        (
            U
            p
            nut
        );
    }
}
```

Below is a graphs of the pressure evolution at the location of the first probe:

For internal cylinder

![Pressure](https://dl.dropboxusercontent.com/u/2169907/p-1.png)

For internal sphere

![Pressure](https://dl.dropboxusercontent.com/u/2169907/p-2.png)

Though evolution has periodic structure but the frequency differs from estimations.

## How to run the cases on your own

I've put all necessary files into [repository](https://bitbucket.org/mrklein/tube-von-karman) on BitBucket. So you can clone the
repo or just download [archive of the default branch](https://bitbucket.org/mrklein/tube-von-karman/get/default.tar.gz). There is Cases folder
where case files reside. Every case directory has Allprepare script which will
do all the necessary utility invocations:

```sh
#!/bin/sh
cd ${0%/*} || exit 1

. $WM_PROJECT_DIR/bin/tools/RunFunctions

gmsh - tube.geo
gmsh - cylinder.geo
sed -i -e "s/Created by Gmsh/cylinder/" cylinder.stl
mv cylinder.stl constant/triSurface
gmshToFoam tube.msh
cp -r 0.org 0
changeDictionary
surfaceFeatureExtract
snappyHexMesh -overwrite
```

The script assumes that you have gmsh in your `$PATH` and first it creates mesh
for tube and cylinder, then corrects STL file created by Gmsh, then places STL
files to the directory where sHM will look for it, converts tube mesh to
OpenFOAM format, corrects boundary type for walls, and finally runs sHM for
final mesh creation. sHM procedure is rather lengthy and needs RAM as the limit
on the number of cells is `1500000`.
