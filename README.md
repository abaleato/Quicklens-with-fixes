This repository is a copy of D.Hanson's [Quicklens code](https://github.com/dhanson/quicklens) after correcting the two critical bugs listed below:

# Fixed bugs
### 1) Spherical harmonic transforms for fields with non-zero spin

Faulty functions: shts/__init__.map2vlm() and shts/__init__.vlm2map()

What we were seeing was that, while the TT full-sky reconstruction worked fine, things went wrong pretty dramatically as soon as you tried to incorporate polarisation fields. We seem to have tracked this down to a bug in the functions that convert between real space fields and their spin-s spherical harmonic representation. In particular, it appears that the issue had to do with how {s}_Y_{lm} was manipulated when s<0 to avoid feeding the fortran module actually doing the transformation a negative spin. The bug was harmless when s=0, so temperature worked fine. 

### 2) Simulation of TEB fields in the flat-sky limit

Faulty function: sims/sims.tebfft()

The bug would have appeared if you ever used it to simulate flat-sky fields: the function ql.sims.tebfft() can be used to generate realizations of size (nx,ny) of real TEB fields by drawing complex Fourier coefficients with the right power spectrum on a Fourier-space grid of size (nx, ny/2+1). However, the implementation forgets to set the strip (lx=0,ly<0) to the complex conjugate of the (lx=0, ly>0) entries. One manifestation of this bug is that the lensed simulations you generate will lack power, with the shortage being most acute on large scales. 