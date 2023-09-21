---
layout: post
title:  "LO-TO splitting"
date:   2023-09-20 13:48:01 +0800
categories: physics
---

# Introduction and context
Recently I am trying out some phonon calculations for a production system and unfortunately
found negative phonon frequencies with `conv_thr = 1e-10` and `tr2_ph = 1e-14`. I believe 
that my structure is stable, so to understand whether it is convergence issue I decided 
to learn some practical things from a smaller test system. 

Yesterday I tried to benchmark phonon calculations using Quantum ESPRESSO (`ph.x`)
for bulk hexagonal boron nitride (hBN) at $ \mathbf(q) = 0 $ to understand how much I need to 
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
Firstly I want to understand what is the acoustic sum rule. 

# LO-TO splitting
Then I want to understand what is LO-TO splitting.

# Practicalities
To get LO-TO splitting in a phonon calculation, we need to calculate the non-analytical
term at $\mathbf(q) = 0$, which in turn requires computing xxx according to Eq. xx. Example
input snippet:

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

Then can get LO-TO splitting.
