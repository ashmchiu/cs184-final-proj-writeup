---
layout: page
title: 'Milestone'
has_right_toc: true
usemathjax: true
nav_order : 2
---
<p class="warning-message">
This assignment has not been completed yet.
</p>

<h2><strong>Honey, I Upped the Viscosity!</strong></h2>
Team Members: Ashley Chiu, Emmanuel Duarte, Dana Feng, Raymond Tan

## What We've Accomplished

We've built out a particle model based on Homework 4's `PointMass` attribute, constructing a new scene with asphere of `PointMass`es hovering over a `Sphere` `CollisionObject`, and then dropping that sphere of particles over the `Sphere`. 

TODO

## Preliminary Results
TODO

## Reflections
Reflecting on our current simulations, we like TODO 

However, we'd like to fix TODO

## Updated Work Plan
Narrowing the scope of our original [expected goals](/proposal.md#expected-goals), we believe we were too ambitious in our original plans. Rather than focusing on dripping the honey onto different types of surfaces, we will limit our collisions with honey to the sphere as we believe it demonstrates adhesion well. Thus, our remaining visual goals are to ensure that (1) residue from the honey sticks on the sphere and (2) simulate the texture of honey well with a BSDF. Some stretch goals we have are to ray trace shadows and make honey reflect and refract environments in Blender. Thus, our updated schedule is
- **Week 2 (4/14-4/20)**: Continue working on the viscosity model based on [Modeling and Rendering Viscous Liquids](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=bdbe45284686a54f3284fdf98759f099e3a95e84). Tweak the adhesion matrix as needed for realisitic rendering. Complete the marching cubes implementation to capture meshes at different timesteps in our rendering. 
- **Week 3 (4/21-4/28)**: Parse the produced `.obj` files from marching cubes through [Stop-motion-OBJ](https://github.com/neverhood311/Stop-motion-OBJ) to render in Blender. Import mesh sequence into Blender, add BSDF for honey.
- **Week 4 (4/28 - 5/4)**: Refract the environment through honey and ray trace shadows (stretch goals given time). Prepare and practice for the final presentation.

## Deliverables
[Slides](https://docs.google.com/presentation/d/1XChTjyzATtKneU5rBeOULRzXM5DbCcm-AcWQce74FTo/edit#slide=id.p) \| Video (TBD)