---
layout: post
title: "Fun with OpenFOAM and turbulence models"
category: posts
---

Creation of these movies was a part of sudden teaching assistance duties. In
the beginning the idea was to just reproduce [Kármán vortex street](http://en.wikipedia.org/wiki/Kármán_vortex_street). And then it
evolved to the comparison of vortex street simulation results with different
turbulence models. Case files for the LES simulations can be found at
[https://bitbucket.org/mrklein/openfoam-ravings](https://bitbucket.org/mrklein/openfoam-ravings).

## Channels and meshes

We have simple straight channel with two bodies of different shape put inside
the channels. Simple schemes of the channels are shown on the figures below.

![Circle channel scheme](/public/images/2012-10-21/circle-channel-scheme.png "Circle channel scheme")

![Rectangle channel scheme](/public/images/2012-10-21/rectangle-channel-scheme.png "Rectangle channel scheme")

At the inlet fluid velocity changes linearly from 0 to 1 m/s during time
interval 0-5 s and then it stays constant for the rest of the simulation time
(20 s). So the flow inside the channels goes from laminar to turbulent as
Reynolds number grows.

For all cases rather simple uniform mesh was used. Surely it was possible to
use mesh grading to reduce computation time but cases were 2D and I've decided
not to mess with all these gradings. Meshes are shown on figures below.

![Circle channel grid](/public/images/2012-10-21/circle-grid.png "Circle channel grid")

<a href="/public/images/2012-10-21/rectangle-grid.png">
![Rectangle channel grid](/public/images/2012-10-21/rectangle-grid.png "Rectangle channel grid")
</a>

## Models and simulation results

Two different approaches were selected for description of turbulence - RANS and
LES. In case of RANS different variations of k-epsilon model were used: [RNG
k-epsilon](http://www.cfd-online.com/Wiki/RNG_k-epsilon_model) and [realizable k-epsilon](http://www.cfd-online.com/Wiki/Realisable_k-epsilon_model). Kinetic energy at the inlet was estimated
from turbulence intensity using standard 1.5(uI)^2 and dissipation rate was
calculated from kinetic energy value and channel characteristic length (7% of
Dh). For LES smooth delta with [Smagorinsky sub-grid viscosity model](http://www.cfd-online.com/Wiki/Smagorinsky-Lilly_model) were used.

### Flow around rectangle

#### LES velocity

<iframe width="640" height="480" src="//www.youtube.com/embed/4nEivPV5yGw?rel=0" frameborder="0" allowfullscreen></iframe>

#### LES vorticity

<iframe width="640" height="480" src="//www.youtube.com/embed/RPWyJ25ch-E?rel=0" frameborder="0" allowfullscreen></iframe>

#### RANS (realizable k-epsilon) velocity

<iframe width="640" height="480" src="//www.youtube.com/embed/9504BorNVTM?rel=0" frameborder="0" allowfullscreen></iframe>

#### RANS (realizable k-epsilon) vorticity

<iframe width="640" height="480" src="//www.youtube.com/embed/3JXUIyedcqc?rel=0" frameborder="0" allowfullscreen></iframe>

#### RANS (RNG k-epsilon) velocity

<iframe width="640" height="480" src="//www.youtube.com/embed/vIbLRensnAw?rel=0" frameborder="0" allowfullscreen></iframe>

### Flow around circle

#### LES velocity

<iframe width="640" height="480" src="//www.youtube.com/embed/JgoHhKiQFKI?rel=0" frameborder="0" allowfullscreen></iframe>

#### LES vorticity

<iframe width="640" height="480" src="//www.youtube.com/embed/BzG0S9jLE7c?rel=0" frameborder="0" allowfullscreen></iframe>

#### RANS (realizable k-epsilon) velocity

<iframe width="640" height="480" src="//www.youtube.com/embed/Gv-CPKlFJeI?rel=0" frameborder="0" allowfullscreen></iframe>

#### RANS (realizable k-epsilon) vorticity

<iframe width="640" height="480" src="//www.youtube.com/embed/57-URbKeWME?rel=0" frameborder="0" allowfullscreen></iframe>

#### RANS (RNG k-epsilon) velocity movie

<iframe width="640" height="480" src="//www.youtube.com/embed/VDSGc8ZvhU8?rel=0" frameborder="0" allowfullscreen></iframe>

#### RANS (RNG k-epsilon) vorticity movie

<iframe width="640" height="480" src="//www.youtube.com/embed/IpOH-k_5st0?rel=0" frameborder="0" allowfullscreen></iframe>
