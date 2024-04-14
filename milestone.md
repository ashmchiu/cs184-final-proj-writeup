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

Simulating honey fluid dynamics using [Modeling and Rendering Viscous Liquids](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=bdbe45284686a54f3284fdf98759f099e3a95e84), we implemented liquid-solid interactions so the honey sticks to the sphere, liquid-liquid interactions to build viscosity between the honey particles, and refined interpenetration prevention and volume preservation to prevent a 2D-ification of the honey as it pools at the bottom on the plane. To speed up our simulations, which were effectively unrunnable (buffering at every time step), we utilized spatial hashing for neighbor particle searching (via `Cell`s) and incorporated OpenMP parallelization over all possible `for` loops. Parallelizing neighbor search and particle-sphere collision at every time step had the most impact.

## Preliminary Results
TODO

## Reflections
Reflecting on our current simulations, we like TODO 

However, we'd like to fix TODO

## Updated Work Plan
Narrowing the scope of our original [expected goals](/proposal.md#expected-goals), we believe we were too ambitious in our original plans. Rather than focusing on dripping the honey onto different types of surfaces, we will limit our collisions with honey to the sphere since it demonstrates adhesion well. Thus, our remaining visual goals are to ensure that (1) residue from the honey sticks on the sphere and (2) simulate the texture of honey with a BSDF and ray trace shadows. A stretch goal we have is to make honey reflect and refract environments in Blender. Thus, our updated schedule is
- **Week 2 (4/14-4/20)**: Continue working on the viscosity model based on [Modeling and Rendering Viscous Liquids](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=bdbe45284686a54f3284fdf98759f099e3a95e84). Tweak the adhesion matrix as needed for realisitic rendering and finish volume preservation. Complete marching cubes implementation to capture meshes at different timesteps in our rendering. 
- **Week 3 (4/21-4/28)**: Parse the produced `.obj` files from marching cubes through [Stop-motion-OBJ](https://github.com/neverhood311/Stop-motion-OBJ) to render in Blender. Import mesh sequence into Blender, add BSDF for honey and ray trace shadows.
- **Week 4 (4/28 - 5/4)**: Refract the environment through honey (stretch goal given time). Prepare and practice for the final presentation.

## Deliverables
[Slides](https://docs.google.com/presentation/d/1XChTjyzATtKneU5rBeOULRzXM5DbCcm-AcWQce74FTo/edit#slide=id.p) \| Video (TBD)