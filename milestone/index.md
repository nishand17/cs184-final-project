# cs184-final-project

Bilal Syed | Nishan D’Souza

Scott Sunarto | Hans Zhang

Procedurally Generated Terrain

# Summary

Our group is working on a procedurally generated 2D terrain renders in Python. At the end of the project, our goal is to have a fully functioning terrain procedural generation system with high degree of customizability. These terrain renders would be composed of things such as: mountains, rivers, stars, sun/moon, etc. We aim to make this terrain renders to be aesthetically pleasing with smooth and realistic looking curvatures generated from different noise functions that the user can choose (i.e. perlin, simplex, or brownian noise). On top of that, we are planning to experiment with different method of applying texture/gradient color to each of the terrain components. For starters, we plan to use barycentric coordinates to apply a smooth gradient over the rolling hills based on the x-axis and y-axis offset, etc.

As a stretch goal, we also plan to experiment with more advanced concepts such as particle effects to augment our terrain. For instance, we can use a fire + smoke particle simulation for a fire pit surrounded by tents.

**Slides:** [Link](https://docs.google.com/presentation/d/1uvBOAOIlAqOsHXEM_jdHr-3s5UQRWiH77Y261Pejehk/edit?usp=sharing)

<div style="position: relative; padding-bottom: 56.25%; height: 0;"><iframe src="https://www.loom.com/embed/99af2838af56489cbfc266e8e66cba8a" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"></iframe></div>

# Progress

We have had a steady progress so far.

Our key goals in this milestone is divided into:

1. Research on terrain procedural generation
2. Get ourselves comfortable with the Python's pygame library
3. Scaffold the codebase to demonstrate a minimal proof-of-concept

## Research on terrain procedural generation

Our research on terrain procedural generation starts with the question: how do we generate natural looking hills/mountains?

The answer to our question is to use a suitable noise function. In this case, we found 3 potential candidates: Perlin noise, simplex noise, and random (white) noise.

**Perlin Noise**

<img src="https://upload.wikimedia.org/wikipedia/commons/d/da/Perlin_noise.jpg" />

Perlin noise is a commonly used gradient noise that is often uses in computer graphics to create procedural texture. Games such as Minecraft have been noise to use perlin noise to generate their terrain landscape. (Note: this is what we are trying to replicate, albeit in a 2 dimension instead of Minecraft's 3 dimension).

**Simplex Noise**

Simplex noise is an improvement over perlin noise that is invented by the same person (Ken Perlin) as perlin noise. The advantage of the simplex noise is that it has lower computational complexity that scales better with higher dimensions and it also doesn't have noticeable directional artifact.

**Random (White) Noise**

To provide some contrast with the more well thought out noise function (perlin and simplex noise), we introduce a random noise function which is practically "random walk" and does not carry any ideal properties for procedural generation.

**Stretch Goal: Fractional Brownian Motion**

If we have extra time, we also plan to experiment with fractional brownian motion that layers turbulence on top of a perlin noise that some reference material suggests can increase the "quality" / sophistication of the terrain.

## Get ourselves comfortable with the Python's pygame library

Our team is fairly familiar with Python (thanks CS 61A!), however we needed some time to understand the specific intricacies of the Pygame library. We spent our time gathering resources and watching YouTube tutorials on how to structure the pygame codebase with extensibility in mind.

## Scaffold the codebase to demonstrate a minimal proof-of-concept

**Progress picture**

<img src="http://nishand.com/cs184-final-project/milestone/poc.png" />

As seen above, we have been able to create a minimal proof-of-concept of our procedural generation system. This is an _extremely_ rudimentary form of what we are trying to build. We draw the hills on the background with a simple bezier curve; this will be extended to support our more complex hills supplied by the noise function. In the background, we have randomly positioned stars that we plan to animate to look like shooting stars with keyframe easing to make it more dynamic.

# Work plan

For the remaining time of the final project here are our goals:

- Implement the different noise functions (perlin, simplex, and white noise) and integrate it with the terrain components (i.e. hills, mountains, etc).

- Implement terrain texturing

- Implement more terrain components (i.e. sun/moon, etc)

- Implementing shooting star animation with easing

- Add a textbox to input a randomness seed to the procedural generation

- Add input form that allow users to customize the procedural generation (enable/disable components, choose noise function, etc)

- Stretch goal: Implement fire + smoke animation with a nice fire camp render

- Stretch goal: Implement fractional brownian motion
