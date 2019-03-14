---
layout: page
title: Task 1 - Introduction to Pymol
type: task
---


Providing path to python binaries for whole script to run


```python
import sys
sys.path.append("/home/tema/bin/pymol/lib/python3.4/site-packages/")
```

Importing pymol as python module. This is needed to control pymol from jupyter notebook.



```python
import __main__
__main__.pymol_argv = [ 'pymol', '-x' ]

import pymol
pymol.finish_launching()
from pymol import cmd,stored
```

Initializing the model:
* loading model with PDBID: 1cll
* showing bonds as lines
* zooming on risidues 4 and 5
* making 1000 frames of current state
* save current frame as the key one


```python
cmd.do('''
fetch 1cll, async=0
as lines, n. C+O+N+CA
zoom i. 4+5
mset 1 x1000
mview store''')
```

Creating list to store residues. Then iterate over residues and append them to list. 


```python
stored.r = [] 
cmd.iterate('1cll and n. CA','stored.r.append(resi)')
```




    152



Creating color list - points from 1 to 0.5 with number of steps equal to number of residues.

Setting colors for residues - red chanel number derived form the colors list, blue and green set to 0.5 and 0.75 respectively.


```python
import numpy as np

length = len(stored.r)
colors = np.linspace(1,0.5, length)
for k,i in enumerate(stored.r):
    cmd.set_color('col%d' % k, [colors[k],0.5,0.75])
    print([1,1,colors[k]])
    cmd.set('cartoon_color','col%d' % k ,'resi %s' % i)
cmd.show_as('cartoon','all')
```

Create movie frames. For each residue i from 1 to 100, create keyframe where residues form i to i+7 are zoomed in. 


```python
for i in range(length):
    cmd.frame((10*i)+1)
    cmd.zoom( 'n. CA and i. %d+%d' % (i,i+7))
    cmd.mview('store')
```

Video saved in .mov format using ffmpeg converter. And then converted to HTML5 friendly mp4 H.264 format. Tutorial taken form [here](https://rigor.com/blog/optimizing-animated-gifs-with-html5-video).
The resulting movie:

<video width="400" height="300" controls>
    <source src="{{ site.baseurl }}/assets/videos/residMovie.mp4" type="video/mp4" />
    Your browser does not support the video tag.
</video>