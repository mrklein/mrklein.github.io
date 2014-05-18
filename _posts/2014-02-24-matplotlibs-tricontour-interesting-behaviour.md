---
layout: post
title: "Matplotlib's tricontour interesting behaviour"
category: posts
---

Matplotlib was happy to plot contours of triangular meshes in the past. Though
they were not actually triangular, they were triangulated (by OpenFOAM's sample
utility) rectangular meshes. Recently I've encountered rather strange thing,
all my attempts to plot isoline of a value on a triangular mesh which initially
wasn't rectangular

```python
data = N.loadtxt(filename)
x = 100*data[:, xidx]
y = 100*data[:, yidx]
 
fl = data[:, flidx]
asp = 4.5/(xlim[1] - xlim[0])
P.figure(figsize=(8, 8*asp))
P.clf()
P.tricontour(x, y, fl, [0.5], colors='black')
```

ended up with

```pytb
Traceback (most recent call last):
  File "./plot.py", line 116, in <module>
    plot_planes(s)
  File "./plot.py", line 75, in plot_planes
    P.tricontour(x, y, fl, [0.5], colors='black')
  File "/usr/lib/pymodules/python2.7/matplotlib/pyplot.py", line 3090, in tricontour
    ret = ax.tricontour(*args, **kwargs)
  File "/usr/lib/pymodules/python2.7/matplotlib/axes.py", line 8922, in tricontour
    return mtri.tricontour(self, *args, **kwargs)
  File "/usr/lib/pymodules/python2.7/matplotlib/tri/tricontour.py", line 280, in tricontour
    return TriContourSet(ax, *args, **kwargs)
  File "/usr/lib/pymodules/python2.7/matplotlib/tri/tricontour.py", line 36, in __init__
    ContourSet.__init__(self, ax, *args, **kwargs)
  File "/usr/lib/pymodules/python2.7/matplotlib/contour.py", line 780, in __init__
    self._process_args(*args, **kwargs)
  File "/usr/lib/pymodules/python2.7/matplotlib/tri/tricontour.py", line 47, in _process_args
    tri, z = self._contour_args(args, kwargs)
  File "/usr/lib/pymodules/python2.7/matplotlib/tri/tricontour.py", line 82, in _contour_args
    Triangulation.get_from_args_and_kwargs(*args, **kwargs)
  File "/usr/lib/pymodules/python2.7/matplotlib/tri/triangulation.py", line 172, in get_from_args_and_kwargs
    triangulation = Triangulation(x, y, triangles, mask)
  File "/usr/lib/pymodules/python2.7/matplotlib/tri/triangulation.py", line 72, in __init__
    dt = delaunay.Triangulation(self.x, self.y)
  File "/usr/lib/pymodules/python2.7/matplotlib/delaunay/triangulate.py", line 123, in __init__
    self.hull = self._compute_convex_hull()
  File "/usr/lib/pymodules/python2.7/matplotlib/delaunay/triangulate.py", line 158, in _compute_convex_hull
    hull.append(edges.pop(hull[-1]))
KeyError: 5685
```

while if I try to use scipy triangulation

```python
from scipy.spatial import Delaunay
from matplotlib.tri import Triangulation
 
data = N.loadtxt(filename)
x = 100*data[:, xidx]
y = 100*data[:, yidx]
pts = N.column_stack((x, y))
tri = Delaunay(pts)
mtri = Triangulation(x, y, tri.simplices.copy())
 
fl = data[:, flidx]
asp = 4.5/(xlim[1] - xlim[0])
P.figure(figsize=(8, 8*asp))
P.clf()
P.tricontour(mtri, fl, [0.5], colors='black')
```

everything goes more or less OK. 

Thought matplotlib and scipy use the same algorithm for triangulation appears I was wrong.
