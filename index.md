---
layout: page
title: Final Report
has_right_toc: true
usemathjax: true
---
<h2><strong>Honey, I Upped the Viscosity! 🍯</strong></h2>
Team Members: Ashley Chiu, Emmanuel Duarte, Dana Feng, Raymond Tan

## Abstract

## Technical Approach

### Set Up
Building off Project 4 `Clothsim`, we converted the `PointMass`es of a `Cloth` into `Particle`s, with attributes such as velocity, radius, mass, momentum, and viscosity. We then collided these `Particle`s with a `Sphere` and a `Plane`, creating different scenes.

### Particle Simulation Physics
We effectively implemented two different particle simulations Lagrangian based particle simulation (with and without volume preservation) and one based on Smoothed Particle Hydrodynamics. 

#### Lagrangian Particle Model
[INSERT PARTICLE SIM VIDEO WITHOUT VOLUME PRESERVATION]
We modeled our simulation off of [Modeling and Rendering Viscous Liquids](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=bdbe45284686a54f3284fdf98759f099e3a95e84). We created a similar cell structure that houses a vector of particles for better spatial lookup, and for each particle we hashed the particle to a cell, using the hash key provided in the paper: `541i + 79j + 31k mod table_size`; we did however, scale the `i`, `j`, and `k` by 10 so as to spread the particles more evenly in buckets, and also chose the table size to be 100. In addition, we also integrated OpenMP to speed up all of the for loops interating over each particle, which was not mentioned in the paper but did help tremendously in terms of scaling up the number of particles run for the simulation. At a high level, the paper describes adding forces and gravity to each particle at each timestep $F(p) = \sum_{i} f(p_i) + g$, with adhesion forces, viscosity forces, and friction/interpenetration forces. For adhesion forces, the Lennard-Jones potential was used, following a sample piecewise graph given between particles of distance `d`. 

Instead of using adhesion forces between the honey and the sphere/bread as described in the paper, however, we use friction forces instead, and calculate a correction vector to ensure that there is adhesion to the bottom of the sphere only if the liquid particle is within a certain proximity to the sphere. We also apply the collision calculations between the particles and sphere so as to prevent the particles from sinking into the sphere. In terms of viscosity forces, we use the paper's momentum equations to calculate the momentum exchange: 

$$
P_{exch} = \frac{v_k(d_{pn})(P_p - P_{p_n})\delta t}{2 \sum_{i=0}^{n} k(d_i)} \\
P_p = P_p + P_{exch} \\
P_{pn} = P_{p_n} - P_{exch}
$$ 

We use our own piecewise gaussian function, tuning the function so that it only returns a gaussian value if the distance between two particles is less than `0.4`, rather than the paper's specified `4-6`. Then, we explicitly convert the particle momentum to a force by dividing it by the change in time. We also add damping constants to all the forces, which was not mentioned in the paper, but we found that without these constants, the particles would explode and basically repel each other violently. The constants we chose were 0.1 and 0.01 for adhesion and viscosity respectively. Finally, we implemented interpenetration prevention, which was not at all described in detail in the paper, so we ended up just adding a correction vector to move two particles apart if they were overlapping one another. It was interesting as we had to calculate distance between two particles while taking into account their radii rather than just the difference between their center positions. 


[INSERT PARTICLE SIM VIDEO WITH VOLUME PRESERVATION]
While this initial implementation without volume preservation worked well, we wanted to see if we could get more of a mushroom and pooling effect by implementing volume preservation. Thus, we also implemented volume preservation, as detailed in the paper. First, we calculated the desired density of each particle by this equation: $$\rho_0 = \sum_{i=1}^{26} \omega(d_i) \\$$, where $$\omega$$ refers to a piecewise density smoothing kernel, following the paper's lower and upperbound of `2rsqrt(3) to 4r`. We then refine the density calculation by either scaling by `26/n` if the particle had less than 26 neighbors (n neighbors instead). The paper also mentions the derivative of the density, but the paper does not use the derivative in the calculations anywhere, so we did not either. We then calculated the correction vector to be applied to each particle's position: $$\Delta v = \frac{(c(\rho - \rho_0) \mathbf{p_n} - p)}{\|\mathbf{p_n} - p\|}$$, where we chose c to be 0.001, rather than 0.1-0.5 as described in the paper.


We also applied 0.1 as the damping factor when adding each of the correction vectors, as we found that volume preservation without damping resulted in particles being blown away from each other. We also only do one iteration of this correction, as we found that the computation was too intensive/slow for more than 1 iteration.


Overall, implementing the particle physics allowed us to understand the amount of tuning and scaling of different hyperparameters/values that go into a graphics simulation. It also made us appreciate papers that are more detailed in their technical implementation; while the paper we followed was pretty detailed, some sections such as interpenetration was not explained as in depth, and they also did not specify how many particles they used or what constants they chose.


#### Smoothed Particle Hydrodynamics
[INSERT VIDEO WITH THEIR SPH]


### Surface Reconstruction

We explored 3 separate meshing/rendering techniques, and found that metaballs in Blender was the most suitable for our simulation. We tried Marching Cubes on all three of our particle simulations, tried OpenVDB on our SPH simulation, and applied Metaballs to our two Lagrangian particle simulations, as our SPH simulation required 10k particles, which was too much for metaballs.

#### Marching Cubes

Finding Marching Cubes to be a common solution, we initially tried building off [this GitHub repository](https://github.com/nihaljn/marching-cubes), debugging and changing codes to parse our particle position files and tuning isovalues. However, our resulting images always ended up with individual cubes per particle or inverted meshes that did not resemble fluids. We tried increasing the number of particles and interpolating to produce more densely populated particles, both to no avail.

<div align="center">
    <div style="display: inline-block; width: 30%;">
        <img src="../assets/index/8000 vs 1000.png" width="75%" />
        <figcaption>Lagrangian Simulation (without volume preservation), 8000 vs 1000 particles</figcaption>
    </div>
    <div style="display: inline-block; width: 30%;">
        <img src="../assets/index/interpolation.png" width="75%" />
        <figcaption>Lagrangian Simulation (with volume preservation), Averaging/Interpolation</figcaption>
    </div>
    <div style="display: inline-block; width: 30%;">
        <img src="../assets/index/sph.png" width="50%" />
        <figcaption>SPH Particle Simulation with Poly6 kernel isovalues (Ball of honey) </figcaption>
    </div>
</div>


#### OpenVDB
[TALK ABOUT INSTALLATION PROBLEMS, LIMITED DOCUMENTATION, CONVERTING FROM POINTS TO VOLUME TO MESH]

#### Metaballs

In our existing simulation, we wrote a script to record particle positions every 16-32 timesteps, and then used the Blender Python API to write another script to parse the particles and render them one by one individually. We found that the most amount of particles we could render was around 3k, hence we were not able to render the SPH simulation.

We had to tune the size of the metaballs, as well as make sure we have at least 1k particles, or else we ended up with boba like honey that had individual orbs. If there were 2k or 3k particles, metaball sizes of 0.04 to 0.07 were enough, while with only 1k particles, we had to use 0.08 to 0.1.
[INSERT BOBA VIDEO]
[INSERT OFF CENTER SPHERE VIDEO]

Overall, we learned the importance of exploring and adopting various meshing/rendering techniques, as well as the importance of using suitable and well documented methods. We also got a taste of just how tedious rendering can be - both Ashley and Dana had to run Blender/render all the frames over the course of 2 full days to get only a 2-3 second video!


### Blender-ing Rendering
[TALK ABOUT BLENDER SHADING, CAUSTICS, BREAD, etc..]

### Speed Ups

### Problems Encountered
Early on, we struggled with density preservation as honey dripped and hit a plane. Honey needed to stay viscous and remain stuck to the sphere while it dripped off at the bottom, which was a difficult task. We implemented two separate viscosity models in order to attempt to fix this from our milestone, using [Modeling and Rendering Viscous Liquids](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=bdbe45284686a54f3284fdf98759f099e3a95e84) and [SPH Fluids in Computer Graphics](https://cg.informatik.uni-freiburg.de/publications/2014_EG_SPH_STAR.pdf). Ultimately, the tuning of [Modeling and Rendering Viscous Liquids](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=bdbe45284686a54f3284fdf98759f099e3a95e84) produced better results, and as such, most of our polished renders occurred under this physics engine. We learned to divide and conquer work here so pairings would work on separate physics engines to improve them.

Originally, we considered doing surface reconstruction via Marching Cubes and OpenVDB, however, after gaining little progress (sparse or inverted meshes), we salvaged via Metaballs in Blender. 

## Results

## References
We built our particle simulation model off `ClothSim`. For particle simulation, we referenced: 
- [Modeling and Rendering Viscous Liquids](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=bdbe45284686a54f3284fdf98759f099e3a95e84)
- [An Implicit Viscosity Formulation for SPH Fluids](https://cg.informatik.uni-freiburg.de/publications/2015_SIGGRAPH_viscousSPH.pdf)
- [Coding Adventures: Simulating Fluids](https://www.youtube.com/watch?v=rSKMYc1CQHE)

To port our particle simulation to Blender and simulate realism, we utilized these resources:
- [Poligonising a scalar field](https://paulbourke.net/geometry/polygonise/)
- [Marching Cubes](https://github.com/nihaljn/marching-cubes)
- [Procedural Bisucit Material (Blender Tutorial)](https://www.youtube.com/watch?v=52dC0yBS35I)

## Contributions

Navigate to previous checkpoints in our journey to render honey -> [milestone](/milestone.md) \| [proposal](/proposal.md)