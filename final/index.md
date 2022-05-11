# Abstract

In this project, we created a Python program that procedurally generate and render world landscapes inspired from the games of our childhood such as Minecraft and Terraria. The program uses a 2-dimensional OpenSimplex noise function to procedurally generate the world surface, employs color theory to select aesthetically pleasing color palette, and use kinematics-based animation techniques to simulate a real world physics environment.

# Technical Approach

## Noise Function

To procedurally generate the world surface a smooth and realistic terrain, we need a suitable noise function that we can feed into the renderer to create surface textures that emulate hills and mountains. 

Using a white noise (random noise) function will not suffice due to the erratic non-pattern look it generates. Instead, we direct our attention to gradient noise functions, specifically the Perlin noise function and its successor, that provides a smooth transition between its adjacent values.

![alt_text](images/image1.png "image_tooltip")


_Visualization of Random Noise vs. Perlin Noise in 1-dimension_

_https://www.youtube.com/watch?v=MJ3bvCkHJtE_

On a high-level, Perlin noise are able to achieve this smooth curve by calculating the dot products between a randomly generated gradient vector and the offset vector (the distance between a point and the gradient vector) and linearly interpolating the dot products. For example, to generate a 2-dimension noise such as the one that we are going to be using, a bi-linear interpolation is used to linearly interpolate 4 dot products. With this method, the algorithmic complexity of our noise function would be O(2^n) where n is the number of dimension.

For this project, we instead opt to use another gradient noise function called the [Open]Simplex noise, that is often considered as the successor of the Perlin noise function. The [Open]Simplex noise is able to produce a similar smooth transition between the values, but with better efficiency, O(n^2), in higher dimension and also less directional artifact in the final result. 

We can then interact with the noise function by providing it a 2D cartesian coordinate, and it will return the corresponding noise value at that point with range: [0, 1]

We are then able to generate a formula to generate a set of points for the hill and mountain procedurally:


```
// Pseudocode

points = []

For i in range(SCREEN_WIDTH):

	points.append([i, &lt;obj y-offset> + &lt;amplitude> * noise(&lt;x-step> * i, &lt;y-step>])

&lt;obj y-offset> 	: the location where the points will be rendered

&lt;amplitude>	: the amplitude, in pixels, for the hills/mountains

&lt;x-step>	: a discrete step in the noise x-axis that determines the noise value

&lt;y-step>	: a discrete step in the noise y-axis that determines the noise value, used to generate subtle differences in the mountain and hills layering
```

## Color Theory

Now that we have our world terrain, we need to bring them to life with colors! The simplest approach would be to randomly generate colors, however that would not provide us with the aesthethically pleasing result that we are looking for. Therefore, we need to employ color theory to the many facets of our terrain renders.

On a high-level, the color scheme that we want to be working in is the HSLA (Hue, Saturation, Lightness, Alpha) instead of RGBA. HSLA color scheme provides us with a convenient “abstraction” to programmatically use color theory in our program.



1. Hills and mountains layering


![alt_text](images/image2.png "image_tooltip")



In the world terrain, you might notice that the hills and mountains have “layers” to it, this was done as an artistic choice to create an emulation of depth in a 2-dimensional space. If you pay attention closely, we also subtly adjust the &lt;y-step> to make it more realistic. 


For mountains and hills, we start with a randomly selected hue value for all of the layers, then we slightly adjust the saturation and lightness for each layer to achieve the result that we are looking for. This gives us the “monochromatic” color palette that we are looking for.

2. Night sky


![alt_text](images/image3.png "image_tooltip")



If you look closely, you might notice that the night sky also has a randomly selected tint to it. We are able to efficiently generate this programmatically by using the HSLA color scheme. Simply, we select a low lightness and saturation value and randomly selects a hue that corresponds to the color tint.



3. Mountain “view distance”

Another subtle detail on the night sky is in the way the mountain range is drawn. You might notice that the mountain seemingly fades into the night sky as if there are layers of fogs. We achieve this by setting the alpha of the mountain range to a lower value and allowing it to blend to the night sky background.


This is inspired by Minecraft’s “view distance” setting that fades away the terrain that are a certain distance away from the player (Trivia: this is done to reduce the number of chunks that needs to be loaded at any given time, therefore reducing memory usage, the “fade” prevents player from seeing the unrendered chunks).


## Kinematics-based Animation


![alt_text](images/image4.png "image_tooltip")


The crowning jewel of the terrain render is the star field that graces the night sky. The animation for the star-field is procedurally generated using kinematics and it performs a simple simulation of “gravity” and “air resistance” to create the natural looking shooting star effect.

To do this, we employ an animation method that is inspired by kinematics where the object movement is simply dictated by its velocity and acceleration, instead of the individual forces. 

The way this works on a high-level is as the following:



1. When instantiated, each particle is assigned a position, horizontal velocity, and vertical velocity. 
2. At every discrete timestep, we apply different accelerations on the particle to change its horizontal and vertical velocity. On the y-axis, this would be the “gravity” of the planet. On the x-axis, this would be the “air resistance” of the planet.
3. Last but not least, we then calculate the new position based on the velocity of the particle. 

By combining these different accelerations, with constant iteration on the range of possible values, we are able to create the arc-ing trajectory of the shooting star. To make this more visible, and also for dramatic effect, we render the trail of each star particle by storing its previous positions.

 \
Last but not least, we added subtle details on the animation such as the fade in/fade out to create a more realistic feel on how the star appears and disappears.

# Problems That We Faced and Lessons Learned**

**The main problems/challenges that we faced can be boiled down into two key themes:** \
 \
1**) The time consuming trial-and-error process required to come up with a satisfying render**

A lot of the time that we spent in this project involves fine tuning animations, constants, and components to achieve the result that we want. Sometime, this involves refactoring a huge chunk of the codebase as we learned that the way that we architected it does not allow us to cleanly do certain things. 

The fire particle animation is the best example of this. We spent a significant amount of time trying to come up with a way to simulate how a fire would look like with basic graphic primitives (rendering circles) and fine tuning the animation to achieve a realistic feel. The same goes for the star field animation.

**2) Grappling with Pygame’s render system**

Another problem stems from having to grok and understand the way the pygame’s render system works. This especially becomes a problem when we start using a lot of animations in the project.

The naive approach would be to render the entire screen at every frame, however this would be extremely inefficient since there would be a lot of irrelevant components (such as the mountains and hills polygons) that would be redrawn. This results with a “stuttering” animation due to the significantly drop in frames per second (FPS) of the render.

To work around this, we need to carefully plan how each of the components is layered and when they need to be redrawn in the “game loop”.

**The lessons that we learned from this project includes:**

* When working under time constraints, 80/20 the effort: sometimes getting a component up to “perfection” is not worth the time effort when it could be better used to add more features that provides more value.
* Scaffold the project with a clean codebase architecture: we spent a lot of time nearing the end of the project rewriting the entire codebase to be “modular” as its getting difficult to read the code when every implementation is stored in a single main.py file. In hindsight, we should have done this from the very first place to increase developer productivity.

# Results

Attached are example landscapes of the procedurally generated landscapes. For complete demonstration of the animation, the final video serves as a better reference to the animated component of the result.

# References

Pygame, the lightweight graphics library that we used - [https://www.pygame.org/docs/](https://www.pygame.org/docs/)

“Perlin Noise Explained Tutorial 2”, the tutorial we used to learn about the characteristic of the gradient/perlin noise function - [https://www.youtube.com/watch?v=MJ3bvCkHJtE](https://www.youtube.com/watch?v=MJ3bvCkHJtE)

Simplex Noise characteristics - [https://en.wikipedia.org/wiki/Simplex_noise](https://en.wikipedia.org/wiki/Simplex_noise)

Last but not least, as often mentioned, we draw a lot of inspiration from procedurally generated video games such as Minecraft and No Man’s Sky. Our decision to look into Perlin Noise is largely inspired to its use in Minecraft.

# Team Contributions

Scott: Noise function, procedural generation for hills/mountains, color theory on landscape, shooting stars, project scaffolding

Hans: Creation of smooth foreground hills, campfire animation, demo video

Bilal: Sun/Moon, demo video, proposal writeup, initial shooting star implementation

Nishan: Sun/Moon, initial shooting star implementation, writeups
