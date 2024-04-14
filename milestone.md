---
layout: page
title: 'Milestone'
has_right_toc: true
usemathjax: true
nav_order : 1
---
<h2><strong>Honey, I Upped the Viscosity! 🍯</strong></h2>
Team Members: Ashley Chiu, Emmanuel Duarte, Dana Feng, Raymond Tan

## What We've Accomplished

We've built out a particle model based on Homework 4's `PointMass` class, constructing a scene with a sphere of `PointMass`es hovering over a `Sphere` `CollisionObject`. 

<img src="../assets/milestone/adhesion.png" width="25%" align="right" />

Simulating honey fluid dynamics using [Modeling and Rendering Viscous Liquids](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=bdbe45284686a54f3284fdf98759f099e3a95e84), we implemented liquid-solid interactions so the honey sticks to the sphere, liquid-liquid interactions to build viscosity between the honey particles, and refined interpenetration prevention and volume preservation to prevent a 2D-ification of the honey as it pools at the bottom on the plane. To speed up our simulations, which were effectively unrunnable (buffering at every frame), we utilized spatial hashing for neighbor particle searching (via `Cell`s) and incorporated OpenMP parallelization over all possible `for` loops. Parallelizing neighbor search and particle-sphere collision at every timestep had the most impact.

## Preliminary Results
TODO

## Reflections
Reflecting on our current simulations, we like TODO 

However, we'd like to fix TODO

## Updated Work Plan
Narrowing the scope of our original [expected goals](/proposal.md#expected-goals), we want to focus more time on making our viscosity models more realistic. Thus, rather than dripping the honey onto different types of surfaces, we limit our collisions with honey to the sphere since it demonstrates adhesion well. Our first goal is to tune particle movements to be more realistic. Then, while we've built out particle simulation in Homework 4 infrastructure, to replicate the visual experience of honey, we need to use [marching cubes](https://www.cs.toronto.edu/~jacobson/seminar/lorenson-and-cline-1987.pdf) and port meshes into Blender, and simulate the texture of honey with a BSDF and ray trace shadows. A stretch goal we have is to make honey reflect and refract environments. Thus, our updated schedule is
- **Week 2 (4/14-4/20)**: Tweak adhesion and viscosity values to be more realistic and finish volume preservation. Complete marching cubes and capture meshes at different timesteps. 
- **Week 3 (4/21-4/28)**: Parse produced `.obj` files from marching cubes through [Stop-motion-OBJ](https://github.com/neverhood311/Stop-motion-OBJ). Import mesh sequence into Blender, add BSDF for honey and ray trace shadows.
- **Week 4 (4/28 - 5/4)**: Refract the environment through honey (stretch goal given time). Prepare and practice for the final presentation.

## Deliverables
[Slides](https://docs.google.com/presentation/d/1XChTjyzATtKneU5rBeOULRzXM5DbCcm-AcWQce74FTo/edit#slide=id.p) \| Video (TBD)