---
layout: post
title:  "LO-TO splitting"
date:   2023-09-20 13:48:01 +0800
categories: physics
usemathjax: true
---

# Introduction and context
Recently I am trying out some phonon calculations for a production system and unfortunately
found negative phonon frequencies with `conv_thr = 1e-10` and `tr2_ph = 1e-14`. I believe 
that my structure is stable, so to understand whether it is convergence issue I decided 
to learn some practical things from a smaller test system. 

Yesterday I tried to benchmark phonon calculations using Quantum ESPRESSO (`ph.x`)
for bulk hexagonal boron nitride (hBN) at $$ \mathbf{q} = 0 $$ to understand how much I need to 
converge the calculation. I found that using typical scf values of `conv_thr = 1e-6` and
`tr2_ph = 1e-12` yields negative frequencies on the order of 100 cm-1. Then, using
`conv_thr = 1e-10` and `tr2_ph = 1e-18`, the negative frequencies vanish after applying
acoustic sum rule (ASR). But I found a problem. There are four phonon modes at ~1300 cm-1
that are supposed to correspond to optical modes, but in the literature I find that these
phonon modes are split up into ~1300 cm-1 and ~1600 cm-1! Why? I found that LO-TO splitting
was not included in the calculations. So I need to understand what is LO-TO splitting.

I will come back to this at a later moment when I have the time

`conv_thr = 1e-6` and `tr2_ph = 1e-18`: negative freq

# Acoustic sum rule
Due to numerical inaccuracies, the frequencies of the acoustic modes at $$\Gamma$$ are often
not equals to zero. Why must they go to zero? This is because for long-wavelength
acoustic modes, all the nuclei are rigidly displaced such that the forces on all atoms
should be zero. The acoustic sum rule (ASR) is thus the following, for atom $$I$$ sum up 
all force constants:

$$ \sum_{\alpha\beta J}C_{\alpha I, \beta J} = 0$$

# LO-TO splitting
Then I want to understand what is LO-TO splitting. My understanding is that LO-TO splitting can
be obtained from the LST relation, where the ratio $$\frac{\omega_{LO}^2}{\omega_{TO}^2} = \frac{\epsilon_0}{\epsilon_\infty} $$. 
But I just read on [wikipedia][LST-wiki] that LST relation only applies to systems
with one optical branch.

So I did some searching and found that in Quantum ESPRESSO (see Page 14 of [slides][QE-phonon] here),
there is a non-analytic term to the dynamical matrix at $$\mathbf{q} = 0 $$. This is discussed
further in [Phys. Rev. B 1, 910 (1970)][PRB1970], see Eq. (4.7) and Eq. (5.18). Context of 
this paper is to use the microscopic dielectric function to derive formulas for the dynamical
matrix.

# Practicalities
To get LO-TO splitting in a phonon calculation, we need to calculate the non-analytical
term at $$\mathbf{q} = 0$$ as described above. Example input snippet:

```
&inputph
  prefix = 'slab'
  outdir = './'
  tr2_ph = 1e-18
  nmix_ph = 20
  epsil = .true.
  max_seconds = 80000
/
0 0 0
```

```
&input
asr='crystal'
q(1)=1,q(2)=0,q(3)=0
/
```

[LST-wiki]:  https://en.wikipedia.org/wiki/Lyddane%E2%80%93Sachs%E2%80%93Teller_relation
[QE-phonon]: https://www.tcm.phy.cam.ac.uk/~jry20/gipaw/tutorial_vib.pdf
[PRB-1970]:  https://journals.aps.org/prb/abstract/10.1103/PhysRevB.1.910
