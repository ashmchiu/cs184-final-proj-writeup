---
layout: page
title: Final Report
has_right_toc: true
usemathjax: true
---

<p class="warning-message">
This assignment has not been completed yet.
</p>

<h2><strong>Honey, I Upped the Viscosity! 🍯</strong></h2>
Team Members: Ashley Chiu, Emmanuel Duarte, Dana Feng, Raymond Tan

## Abstract

## Technical Approach (challenges and lessons learned are incorporated here)

### Starting Point and Set Up
We built off of CS184's Project 4; we converted the PointMass class to a Particle class, with attributes such as velocity, radius, mass, and momentum, as well as viscosity. We then created a new scene with 3 planes, and one sphere. In terms of the particles, we put all of their positions into a ball first of 0.08 radius, and used used openGL's `GL_POINTS` and drew them into an array to render them as points.

### Particle Simulation Physics
We essentially implemented three different particle simulations: one Lagrangian based particle simulation without volume preservation, one Lagrangian based particle with volume preservation, and an SPH version. 

#### Lagrangian Particle Based Model
[INSERT PARTICLE SIM VIDEO WITHOUT VOLUME PRESERVATION]
We modeled our simulation off of the paper "Modeling and Rendering Viscous Liquids". We created a similar cell structure that houses a vector of particles for better spatial lookup, and for each particle we hashed the particle to a cell, using the hash key provided in the paper: `541i + 79j + 31k mod table_size`; we did however, scale the `i`, `j`, and `k` by 10 so as to spread the particles more evenly in buckets, and also chose the table size to be 100. In addition, we also integrated OpenMP to speed up all of the for loops interating over each particle, which was not mentioned in the paper but did help tremendously in terms of scaling up the number of particles run for the simulation. At a high level, the paper describes adding forces and gravity to each particle at each timestep $F(p) = \sum_{i} f(p_i) + g$, with adhesion forces, viscosity forces, and friction/interpenetration forces. In terms of adhesion forces, we use the Leonard-Jones potential mentioned in the paper, and follow the sample piecewise graph given for adhesion forces between particles of distance `d`, although we again scale the values to get the desired honey fluid effect: 

{% highlight js %}
  if (distance < 1) {
    return 1000;
  }
  if (distance < 2) {
    return -1000*distance+2000;
  }
  if (distance < 3) {
    return -100*distance+200;
  }
  if (distance < 4) {
    return 100*distance-400;
  }
{% endhighlight %}

Instead of using adhesion forces between the honey and the sphere/bread as described in the paper, however, we use friction forces instead, and calculate a correction vector to ensure that there is adhesion to the bottom of the sphere only if the liquid particle is within a certain proximity to the sphere. We also apply the collision calculations between the particles and sphere so as to prevent the particles from sinking into the sphere. In terms of viscosity forces, we use the paper's momentum equations to calculate the momentum exchange: 

$$<

P_{exch} &= \frac{v_k(d_{pn})(P_p - P_{pn})\delta t}{2 \sum_{i=0}^{n} k(d_i)} \\
P_p &= P_p + P_{exch} \\
P_{pn} &= P_{pn} - P_{exch}

>$$ 

We use our own piecewise gaussian function, tuning the function so that it only returns a gaussian value if the distance between two particles is less than `0.4`, rather than the paper's specified `4-6`. Then, we explicitly convert the particle momentum to a force by dividing it by the change in time. We also add damping constants to all the forces, which was not mentioned in the paper, but we found that without these constants, the particles would explode and basically repel each other violently. The constants we chose were 0.1 and 0.01 for adhesion and viscosity respectively. Finally, we implemented interpenetration prevention, which was not at all described in detail in the paper, so we ended up just adding a correction vector to move two particles apart if they were overlapping one another. It was interesting as we had to calculate distance between two particles while taking into account their radii rather than just the difference between their center positions. 


[INSERT PARTICLE SIM VIDEO WITH VOLUME PRESERVATION]
While this initial implementation without volume preservation worked well, we wanted to see if we could get more of a mushroom and pooling effect by implementing volume preservation. Thus, we also implemented volume preservation, as detailed in the paper. First, we calculated the desired density of each particle by this equation: $$\rho_0 &= \sum_{i=1}^{26} \omega(d_i) \\$$, where $$\omega$$ refers to the density smoothing kernel, which we defined as 
{% highlight js %}

   double epsilon = 1e-6;

    if (distance <= LOWERBOUND) {
        return 1.0; // Maximum support within lower bound
    } else if (distance >= UPPERBOUND) {
        return 0.0; // No support beyond upper bound
    } else if (std::abs(distance - LOWERBOUND) < epsilon) {
        return 0.0; // No support exactly at lower bound
    } else if (std::abs(distance - UPPERBOUND) < epsilon) {
        return 0.0; // No support exactly at upper bound
    } else {
        // Linear ramp function between lower and upper bounds
        return 1.0 - ((distance - LOWERBOUND) / (UPPERBOUND- LOWERBOUND));
    }

{% endhighlight %}, following the paper's lower and upperbound of `2rsqrt(3) to 4r`. We then refine the density calculation by either scaling by `26/n` if the particle had less than 26 neighbors (n neighbors instead). The paper also mentions the derivative of the density, but the paper does not use the derivative in the calculations anywhere, so we did not either. We then calculated the correction vector to be applied to each particle's position: $$\Delta v &= \frac{(c(\rho - \rho_0) \mathbf{pn} - p)}{\|\mathbf{pn} - p\|}$$, where we chose c to be 0.001, rather than 0.1-0.5 as described in the paper.


We also applied 0.1 as the damping factor when adding each of the correction vectors, as we found that volume preservation without damping resulted in particles being blown away from each other. We also only do one iteration of this correction, as we found that the computation was too intensive/slow for more than 1 iteration.


Overall, implementing the particle physics allowed us to understand the amount of tuning and scaling of different hyperparameters/values that go into a graphics simulation. It also made us appreciate papers that are more detailed in their technical implementation; while the paper we followed was pretty detailed, some sections such as interpenetration was not explained as in depth, and they also did not specify how many particles they used or what constants they chose.


#### Smoothed-Particle Hydrodynamics
[INSERT VIDEO WITH THEIR SPH]


### Meshing and Rendering

We explored 3 separate meshing/rendering techniques, and found that metaballs in Blender was the most suitable for our simulation. We tried Marching Cubes on all three of our particle simulations, tried OpenVDB on our SPH simulation, and applied Metaballs to our two Lagrangian particle simulations, as our SPH simulation required 10k particles, which was too much for metaballs.

#### Marching Cubes

We initially tried marching cubes, using the github repo: https://github.com/nihaljn/marching-cubes. We had to debug and change some parts of the code to allow for parsing of our particle position files (changes are here: https://github.com/dana-feng/marching-cubes), and we played around with various isolvalues such as particle density or volume, as well as various isovalue thresholds (from 0 to 1e-6). However, we ended up getting individual cubes for each particle or inverted meshes that did not look at all like fluids. We then tried to both increase the number of particles, as well as average/interpolate to produce more densely populated particle position files, but we still got the same results.

<div align="center">
    <div style="display: inline-block; width: 45%;">
        <img src="../assets/index/8000 vs 1000.png" width="100%" />
        <p><em>Lagrangian Particle Simulation (without Volume Preservation), 8000 vs 1000 particles</em></p>
    </div>
    <div style="display: inline-block; width: 45%;">
        <img src="../assets/index/interplation.png" width="100%" />
        <p><em>Lagrangian Particle Simulation (withVolume Preservation), Averaging/Interpolation</em></p>
    </div>
</div>

<div align="center">
    <div style="display: inline-block; width: 45%;">
        <img src="../assets/index/sph.png" width="100%" />
        <p><em>SPH Particle Simulation with Poly6 kernel isovalues (Ball of honey) </em></p>
    </div>
    <div style="display: inline-block; width: 45%;">
        <img src="../assets/index/sph coils.png" width="100%" />
        <p><em>SPH Particle Simulation with Poly6 kernel isovalues (Honey coils) </em></p>
    </div>
</div>


#### OpenVDB
[TALK ABOUT INSTALLATION PROBLEMS, LIMITED DOCUMENTATION, CONVERTING FROM POINTS TO VOLUME TO MESH]

#### Metaballs and Modeling in Blender

In our existing simulation, we wrote a script to record particle positions every 16-32 timesteps, and then used the Blender Python API to write another script to parse the particles and render them one by one individually. We found that the most amount of particles we could render was around 3k, hence we were not able to render the SPH simulation.

We had to tune the size of the metaballs, as well as make sure we have at least 1k particles, or else we ended up with boba like honey that had individual orbs. If there were 2k or 3k particles, metaball sizes of 0.04 to 0.07 were enough, while with only 1k particles, we had to use 0.08 to 0.1.
[INSERT BOBA VIDEO]
[INSERT OFF CENTER SPHERE VIDEO]

Overall, we learned the importance of exploring and adopting various meshing/rendering techniques, as well as the importance of using suitable and well documented methods. We also got a taste of just how tedious rendering can be - both Ashley and Dana had to run Blender/render all the frames over the course of 2 full days to get only a 2-3 second video!

[TALK ABOUT BLENDER SHADING, CAUSTICS, BREAD, etc..]
## Results

## References
[ADD ANY BLENDER VIDEOS, SPH VIDEO, SPH FILE, THE MAIN HONEY PAPER]

## Contributions

Navigate to previous checkpoints in our journey to render honey -> [milestone](/milestone.md) \| [proposal](/proposal.md)