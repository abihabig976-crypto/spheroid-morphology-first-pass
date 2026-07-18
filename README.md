# Spheroid morphology — a first pass at automated image analysis

**Abiha Baig, July 2026**

I am a wet-lab cancer molecular biologist (MSc Cancer Cell and Molecular Biology,
University of Leicester). Before this notebook I had never done automated image
analysis and had not written Python. This repository is my first attempt, and I am
publishing it as-is rather than polishing it, because its point is to be a starting
line rather than a result.

## What it does

Takes a synthetic field of spheroids and extracts the four morphology features
named in the UvA / Amsterdam UMC AI-driven spheroid project:

- spheroid size
- cell cluster count
- area distribution
- inter-cluster distance

## Why I started with thresholding rather than a deep-learning model

I wanted to understand what segmentation *is* before using a tool that does it for
me. So the pipeline is classical: Gaussian blur → Otsu threshold → connected-
component labelling → `regionprops`.

I built the test image myself, so I know the ground truth: **6 spheroids, two of
them deliberately touching.**

Thresholding finds **5**. The two touching spheroids are fused into one object,
and no parameter tuning fixes it, because the method only knows pixel brightness —
it has no notion that a spheroid is a roughly round thing, so it cannot know that a
peanut is really two circles.

The merged object betrays itself in its shape statistics. Every genuine spheroid has
eccentricity ≈ 0.1 and solidity ≈ 0.98. The merged one: **eccentricity 0.91,
solidity 0.83.** So a crude automatic quality flag falls out for free.

Watershed recovers the correct count of 6 — but only for the particular smoothing
parameter I hand-picked, and changing it breaks the count again.

That brittleness is what I actually took away from this. Learned models like
Cellpose and StarDist carry a trained prior over object shape, which is precisely
the thing thresholding lacks. I now understand what "AI-driven image segmentation"
buys you, rather than only that people use it.

## Next

1. Real images (Broad Bioimage Benchmark Collection) in place of the synthetic field
2. Cellpose on the same images; compare counts against threshold and watershed
3. 3D — these are single planes; real spheroids are volumes
4. These features are the state variables a cellular Potts model needs to be
   calibrated against. Extracting them reliably is the first link in that chain.

## Run it

Open `spheroid_morphology_first_pass.ipynb` in Google Colab → Runtime → Run all.
Nothing to install.

## Honesty

Written with AI assistance as a tutor, working from documentation. I can explain
every line and every choice in it; that was the condition I set myself for putting
it up. The scientific content is not novel and is not meant to be.
