# Python wrapper for Lourakis' sparse bundle adjustment C library 
### Dennis Evangelista 

Enjoy! The most recent version can be obtained from [bitbucket](https://bitbucket.org/devangel77b/python-sba) via https or ssh:
```
git clone https://devangel77b@bitbucket.org/devangel77b/python-sba.git
git clone git@bitbucket.org:devangel77b/python-sba.git
```

As **prerequisites**, you will also need to install the sba library as a shared object (libsba.so) (Makefile with shared object target included here) and the sba projections library (libsbaprojs.so):

```
http://www.ics.forth.gr/~lourakis/sba
https://bitbucket.org/devangel77b/libsbaprojs
```

See HOWTO.md for details.

### New in version 1.6.8.2
I think this now works for Python 3. Corrected the cloning recipe now that bitbucket allows git, The bitbucket repo is no longer reliably maintained; for the latest and greatest see  [https://argus.web.unc.edu](https://argus.web.unc.edu)

I am no longer in academia because Jenelle Piepmeier and the Department of Weapons, Robotics, and Control Engineering at the US Naval Academy are religiously bigoted creationist nutjobs. 

### New in 1.6.8

Dylan Ray updated some things to get it to play nicer with Python 3.

### Typical usage

The main way to use this is as follows

```python
import sba

cameras = sba.Cameras.fromTxt('cams.txt')
points = sba.Points.fromTxt('pts.txt',cameras.ncameras)
newcams, newpts, info = sba.SparseBundleAdjust(cameras,points)
```

If you wish to alter the default and autodetected options, you can
create an Options object and change it, and then pass it to sba:
```python
options = sba.Options.fromInputs(points,cameras)
# can also update options.XXX to appropriate values
newcams,newpts,info = sba.SparseBundleAdjust(cameras,points,options)
```

Hopefully this is cleaner than the original way to call it in C. 

### Helpful Hints

Some users have used this package in a workflow combined with the CalTech
Borguet camera calibrations and with data exported from Hedrick's Matlab 
dltDV5 / easyWand5 tools.  If decomposing someone else's P matrix for use
as a cams.txt, it may help to remember that rotations and translations here 
are in the world frame; Rc=R', Tc-R*t (using notation of Hartley and 
Zisserman).

When building for a Mac, one user (amin_abouee) noted that the extensions 
should be .dylib rather that .so; this can be fixed by altering the lines in both the Makefile and the code, e.g.:

```python
libsba = ctypes.CDLL("libsba.dylib")
```
On rotations: AndyLJones noted the following "SBA works with two sets of rotations. There's the initial rotations, which are fed in as part of the globs argument of `sba_motstr_levmar_x` and family in libsba. These are fixed throughout the optimization. There're also the "local rotations", as Lourakis's readme calls them, which are fed in with the rest of the camera parameters via the p argument to the same function. These are updated at each step. Internally, libsba uses local_rotation * initial_rotation when figuring out the projections." This becomes important if you are working on a known-rotation problem and wish to specify initial rotations. See Issue #16 for more detail. 

    
### Contributors

The original sba C library was written by Manolis Lourakis and is 
described in Lourakis, Manolis I A and Antonis A Argyros (2004), "The design 
and implementation of a generic sparse bundle adjustment software package 
based on the Levenberg-Marquardt algorithm", FOURTH_ICS TR-340.

If using this package in research work, we would appreciate you citing it: D Theriault, N Fuller, B Jackson, E Bluhm, D Evangelista, Z Wu, M Betke, and T Hedrick (2014). A protocol and calibration method for accurate multi-camera field videography. *J exp Biol* **217**:1843-1848. The BibTeX entry is:

```bibtex
@article{Theriault:2014,
author = {Theriault, D and Fuller, N and Jackson, B and Bluhm, E and Evangelista, D and Wu, Z and Betke, M and Hedrick, T},
title = {A protocol and calibration method for accurate multi-camera field videography},
journal = {J exp Biol},
doi={10.1242/jeb.100529},
year = {2014},
volume = {217},
pages = {1843--1848}}
```


### Thanks also to

Manolis Lourakis and Antonis Argyros, Ty Hedrick, Evan Bluhm, my mom and the academy. Version 1.6.5 has bug fixes from Isaac Yeaton and Nick Gravish, and notes on Mac usage from Amin Abouee. Andy Jones helped provide clarification on rotations in known-rotation problems.

