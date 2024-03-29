---
layout: page
title: 'Proposal'
has_right_toc: true
usemathjax: true
nav_order: 1
---
<p class="warning-message">
This assignment has not been completed yet.
</p>

<img src="../assets/honey.png" width="30%" align="right" />

## Honey, I Upped the Viscosity
Team Members: Ashley Chiu, Emmanuel Duarte, Dana Feng, Raymond Tan

## Summary
In this project, we aim to simulate honey, as well as the physics of honey falling onto different geometric surfaces and reflecting light through space. We hope to use some of the following surfaces, dripping the honey onto it with different orientations (vertical, horizontal, etc).
- Sphere: Honey should stick and flow down the sphere and pool at the bottom before dripping off.
- Cloth: Honey may stick to the cloth and either spread slowly throughout, or be absorbed by the fabric. A stain will be left behind. 

## Problem Description
We are especially interested in implementing particle systems for highly viscous liquids, particularly for honey, which is a complex liquid that is semi transparent and sticky. We hope to be able to combine the particle system for honey with the point mass system in homework 4, which could then be generalized to other slow and thick liquids that interact with more solid objects.

### Why it is important
This problem is important because we can see how honey interacts with certain surfaces, and we can apply the principles we learn to other forms of liquid. Furthermore, as a viscous liquid, this will provide an understanding of fluid dynamics and the interaction between liquid-solid objects.

### Where it is challenging
This project will be challenging in the following ways:
- Rendering the liquid to appear thick and syrupy, as well as flow smoothly
- Handling collisions between solids and fluids (the main premise of our project), particularly because honey can stick/leave a residue
- Modeling porous materials, since all our work has been on solid objects recently

Our general idea is we’ll build upon the Homework 4 infrastructure, namely instead of seeing how the cloth moves, we’ll be using the cloth as a material for honey to fall on. We were thinking of using Modeling and Rendering Viscous Liquids as a backbone to render honey off of (tuning viscosity and coloring). This simulation of honey will exist within the Homework 4 framework (maybe dripping honey on a plane to start with). We believe this realistic rendering of honey will take up a bulk of our time, but following that, we will drip the honey onto a sphere and cloth, and if time permits, porous materials and other surfaces laid out below. We hope we will be able to accomplish our goals with both the shader knowledge and point mass knowledge from Homework 4, as well as new knowledge of particle systems. 

### Hopeful outputs
Linked below are examples of what we hope to demo:
<ul>
    <li><a href="https://www.youtube.com/watch?v=XDhUXndVLJI">Honey Dripping on Cloth</a><br>
    <p align="center"><iframe width="560" height="315" src="https://www.youtube.com/embed/XDhUXndVLJI" title="Honey Dripping on Cloth" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe></p></li>
    <li><a href="https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=bdbe45284686a54f3284fdf98759f099e3a95e84">Honey Dripping on Sphere</a><br>
    <img src="../assets/proposal/honey_on_sphere.png"></li>
</ul>

## Goals and Deliverables
Our main deliverable is to create images detailing key moments in the simulation, such as when the honey first makes contact with a surface or begins to cascade off it. Our plan includes adding ray tracing/global illumination. Images would be a great way to showcase the shadows and light refractions produced by honey.

Since we're working off the infrastructure for Homework 4, we’ll create one render that has a cloth stretched out in the horizontal plane and some honey dripping onto it. Another render is going to be a render that uses a sphere instead of a cloth. The honey will fall on top of the sphere and show how the honey interacts with the surface over time. These somewhat replicate the experiences shown above in our [Problem Description](#hopeful-outputs).

The questions we hope to plan to answer with our analysis are:
- How can we simulate realistic honey?
- How does honey interact with spheres/cloths when dripped onto these surfaces?

### Quality

Creating quantifiable metrics for this project are difficult because we're mainly aiming to simulate the flow of honey. As such, quality will be deemed by the realism of the simulation, in comparison to honey in real life. We don’t see any performance-based measurements that we can make, particularly because we don’t see us needing to perform any speed ups in our renderings, as we will use a hash table to store the particles in grid cells. We do note that the size of the timesteps taken for simulation will influence the accuracy of the honey, so we will apply techniques such as separating operations for moving vs. stationary particles.

### Expected Goals
As a baseline, we should be able to render honey so that it flows and appears similar to honey in real life. In addition, it should be able to come in contact with other objects and flow similar to how it would in real life.
- **Rendering realistic honey**: Generating the geometry of honey volume, adding material shading, adding ray tracing/global illumination, adding point mass simulation
- **Dripping honey onto cloth**: Collision with cloth material, honey pools onto the cloth
- **Dripping honey onto a sphere**: Collision with sphere

### Stretch Goals
We do have some stretch goals as well, if we have the time!
- **Add residue from honey** to cloth and sphere
- **Drip honey onto additional surfaces**. This will be a little difficult because none of us have used Unity/Blender before, so we'll need to research models for a lot of the materials that we're using to make the simulation look realistic. However, if we have the time, we think this could be fun!
   - Bread: Honey may be partially absorbed by the bread
   - Wood: Honey might seep into the pores of wood surfaces, and leave a light stain
   - Foam: Honey could partially absorb into the foam material, and leave a sticky residue
   - Glass: Honey will stick to glass and if it’s at an angle, honey will slowly fall in the direction of steepest descent. In this scenario, honey will also leave a trail behind. 
   - Ceramic: Honey may adhere to the surface and spread out slightly
   - Fishnet/Wireframe: Honey may drip through the holes of the fishnet, while leaving some stains on the surface.
- **Model honey crystallization** over time, and its interactions with different materials

## Schedule
- **Week 0: (3/31-4/6)**: Get familiar with relevant research papers/videos, as well as computing frameworks involved
- **Week 1 (4/7-4/13)**: Render realistic honey. Start to figure out the consistency, texture, and how honey interacts with light and falls. Start to implement the particle system for the honey, taking into account friction, volume, and viscosity
- **Week 2 (4/14-4/20)**: Finish rendering realistic honey. Render Cloth and sphere surfaces, as well as create the adhesion matrix and model 
- **Week 3 (4/21-4/28)**: Dripping honey onto surfaces, by applying techniques such as tweaking the adhesion matrix and raising the force to be higher than gravitational force.
- **Week 4 (4/28 - 5/4)**: Attempt stretch goals (maybe learn about Unity/Blender), and prepare/practice for the final presentation.

## Resources
We will be using the Homework 4 infrastructure as a starting point to build upon (along with our own personal laptops).

Papers and articles we've referenced are:
- [Modeling and Rendering Viscous Liquids](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=bdbe45284686a54f3284fdf98759f099e3a95e84)
   - Honey specific
       - Create a stringiness effect under ball by raising adhesion force between honey particles and ball’s object particles to be greater than gravitational force
   - Adhesion matrix
       - Used to control behavior of liquids in contact with solid objects
       - When particles come in contact with solid object, adhesion matrix is consulted - type of liquid and solid objects involved in collision are used as indices to loop up corresponding entry in the adhesion matrix and then the value is retrieved to represent strength of adhesion between liquid particle and solid object. The value represents the force applied.
   - Liquid particle system
       - Traditional implicit surface rendering method to generate surface triangles that can be rasterized/raytraced
       - Stored in a 3D spatial partitioning grid of cells
       - Each particle of system represents a globule or finitely small volume of liquid
   - Object system (also particle system)
       - Liquid particles respond to forces originating from collisions with object particles
   - Viscosity Forces
       - As viscosity of liquid increases, momenta of a liquid’s constituent particles tend towards the neighborhood average
   - Friction 
       - Only apply to particles that are in contact with scene objects
   - Volume
       - Iterative approach to maintain correct density at each particle location
   - Rendering with shaders
       - Can use Homework 4’s shaders as a way to raytrace
- [Honey simulation with different viscosity formulations](https://adamalsegard.github.io/lists/TNCG13-essay3.pdf)
   - Two methods for viscosity:
       - [An Implicit Viscosity Formulation for SPH Fluids](https://cg.informatik.uni-freiburg.de/publications/2015_SIGGRAPH_viscousSPH.pdf)
           - Particle based
           - Non physical constant
       - [Variational Stokes: A Unified Pressure-Viscosity Solver for Accurate Viscous Liquids](https://cs.uwaterloo.ca/~c2batty/papers/Larionov2017/Larionov2017.pdf)
           - Grid based
           - Dynamic viscosity coefficient
       - Combines viscosity and pressure forcesBoundary handling
   - Boundary handling
       - Sticky boundaries: honey sticks to solid surfaces, dampening the shear rates between fluid and solid particles
       - Separating boundaries: Fluid flows freely, just not into the boundary
- [A Multi-Scale Model for Simulating Liquid-Fabric Interactions](https://www.cs.columbia.edu/cg/wetcloth/main.pdf)
   - Mixture theory models wet fabric as a continuum mixture of water, air, and fabric material 
   - The saturated continuity equations assume that the fabric is fully saturated with liquid. If we want to simulate partially saturated fabric, we fill parts of the fabric with air particles instead of liquid and the same equations hold