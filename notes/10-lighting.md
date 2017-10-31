[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# Lighting

* A surface can emit light by self-emission, reflect light from other surfaces, or both
* This recursive scattering of light between surfaces accounts for the bleeding of colors between adjacent surfaces

## Light Sources

* **Ilumination Function** — `I(x,y,z,θ,ɸ,λ)`
* For most applications, we model the light source using three intensity components: `I_r, I_g, I_b`
* Different light sources:
	* **Ambient Light** — large light source
		* Characterised by an intensity `I_a`
	* **Point Source** — light equally in all directions from a point p0
		* Characterised by an intensity `I(p0)`
		* Intensity at point `p` equals `1/|p-p0|^2 × I(p0)`
		* If very far, the intensity becomes uniform
	* **Spotlight** — light inside a cone with angle θ,
		* Intensity is a function of the angle ɸ between the direction of the source and a vector on the point
	* **Distant Light Source** — calculations similar to parallel projections

## Phong Reflection Model

* Provides a close enough approximation to physical reality
* Involves following rays of light from a **Light Source** and modelling what happens when they interact with reflecting surfaces
* To do this:
	1. Model the light sources in the scene
	2. Build a reflection model to deal with the interactions between different materials and light
* There are three different types of surfaces:
	* **Specular** — Shiny; most of the light is reflected or scattered in a narrow range of angles
	* **Diffuse** — reflected light scattered in all directions
	* **Translucent** — light penetrates the surface to emerge from another location
* Uses four vectors:
	* `n` is the normal at point `p`
	* `v` is the direction from `p` to viewer
	* `r` is the direction of perfect reflection
	* `l` is the direction of a line from `p` to an arbitrary point on the light source
* Assume that each source can have a separate ambient, diffuse and specular components for each of the three primary colours, as such, we need none coefficients to characterize these terms at any point `p` on the surface:

```
L_i = |L_ira L_iga L_iba|
      |L_ird L_igd L_ibd|
      |L_irs L_igs L_ibs|
```

* Construct the model assuming that we can compute how much of the incident lights is reflected at the point of interest
* The value of `R` depends on:
	* Material properties
	* Orientation of the surface
	* Direction of the light source
	* Distance between light source and viewer

```
R_i = |R_ira R_iga R_iba|
      |R_ird R_igd R_ibd|
      |R_irs R_igs R_ibs|
```

* 	`I_ir = I_ira + I_ird + I_irs`
*  `= R_ira × L_ira + R_ird × L_ird + R_irs × L_irs`
*  `I_r = Sum(I_ra + I_rd + I_rs) + I_ar`

### Phong Reflection Model Components

* Ambient Light: scattered so much by the environment and has no specific direction
* Diffuse Component: comes from one direction — brighter if straight on
* Specular Component: comes from a specific direction and bounces in a preferred direction (shininess)

## Reflection Model

### Ambient Reflection

* Intensity of ambient light `I_a` is the same at every point on the surface
* The amount reflected is given by the ambient reflection coefficient:
	* `R_a = k_a`, `0 ≤ k_a ≤ 1`, so `I_a = k_a × L_a`
* `L_a` can be any of the individual light sources, or it can be a global ambient term

### Diffuse Reflection

* A perfectly diffuse reflector scatters the light that it reflects equally in all directions — hence, such a surface appears the same to all viewers
* The amount of light reflected depends on both the material and on the position of the light source relative to the surface
* `R_d ∝ cosθ`
* `cosθ = I × n` if `I` and `n` are unit vectors
* `k_d` representing the fraction of incoming diffuse light that is reflected
	* `I_d = (k_d)/(a + bd + cd^2) × (I × n) × L_d`
* In practice, we use:
	* `max( (I×n)×L_d, 0 )`

### Specular Reflection

* `I_s = k_s × L_s × cos(^a)(ɸ)`, `0 ≤ k_s ≤ 1`
* The exponent `a` is a _shininess_ coefficient
	* As `a` approaches infinity, we get a mirror
* `I_s = k_s × L_s × max( (r × v)^a, 0 )`

### The Phong Model

Given the above reflections, we get the following:

```
I = 1/[a+bd+cd^2] × (k_d × L_d × MAX(I×n, 0) + k_s × L_s × MAX((r×v)^a, 0) + k_a × L_a
```

## Lighting in OpenGL

1. Define normal vectors for every vertex
2. Create, select and position one or more light sources
3. Create and select a lighting model, which defines the level of global ambient light and effective location of the viewpoint (for lighting calculations)
4. Define material properties

### Normals

* Requires user calculation, then specified using `gl.glNormal3f(nx, ny, nz)`
* _Unit normals_ need to be supplied
	* Normal length can be affected by transformations
	* Scaling does not preserve normal length
	* `gl.glEnable(GL2.GL_NORMALIZE)` allows for autonormalisation at a performance penalty

### Enabling Lighting

* Lighting can be turned on and off, as can individual lights:
	* `gl.glEnable(GL2.GL_DEPTH_TEST)`
	* `gl.glEnable(GL2.GL_LIGHTING)`
	* `gl.glEnable(GL2.GL_LIGHTX)` where `X` can range from 0 to 7
* Smooth shading (_Gouraud Interpolation_) is enabled with:
	* `gl.glShadeModel(GL2.GL_SMOOTH)` or `GL2.GL_FLAT` to turn it off
* A light position can be specified with a 4-dimensional array:
	* `float lightPos[] = {x, y, z, w}`
* Given above, are two types of light:
	* **Infinite** — `w = 0`
	* **Local** — `w ≠ 0`
* To change the properties of a light:
	* `gl.glLightfv(light, property, value)`
	* e.g. changing its position `gl.glLightfv(GL2.GL_LIGHT0, GL2.GL_POSITION, lightPos)`

#### Lighting Example

```java
// Position
GLfloat light0_pos[] = {1.0, 2.0, 3.0, 1.0};

// Reflection colors: {r, g, b, alpha}
GLfloat ambient0[] = {1.0, 0.0, 0.0, 1.0};
GLfloat diffuse0[] = {1.0, 0.0, 0.0, 1.0};
GLfloat specular0[] = {1.0, 1.0, 1.0, 1.0};

// Entire scene's light color
GLfloat lmodel_ambient[] = {0.1, 0.1, 0.1, 1.0};

// Enable lights
gl.glEnable(GL2.GL_LIGHT0);
gl.glLightfv(GL2.GL_LIGHT0, GL2.GL_POSITION, light0_pos);
gl.glLightfv(GL2.GL_LIGHT0, GL2.GL_AMBIENT, ambient0);
gl.glLightfv(GL2.GL_LIGHT0, GL2.GL_DIFFUSE, diffuse0);
gl.glLightfv(GL2.GL_LIGHT0, GL2.GL_SPECULAR, specular0);

// Enable scene light
gl.glLightModelfv(GL2.GL_LIGHT_MODEL_AMBIENT, lmodel_ambient);
``` 

### Spotlights

```java
// Set spotlight direction
GLfloat light0_spotDir[3] = {0.0F, 0.0F, -1.0F};

gl.glLightf(GL2.GL_LIGHT0, GL_SPOT_EXPONENT, 0.0F);
gl.glLightf(GL2.GL_LIGHT0, GL_SPOT_CUTOFF, 180.0F);
gl.glLightfv(GL2.GL_LIGHT0, GL_SPOT_DIRECTION, light0_spotDir);
```

### Two-Sided Lighting

```java
gl.glLightModeli(GL2.GL_LIGHT_MODEL_TWO_SIDED, GL2.GL_TRUE);

// Counter-clockwise faces as front:
gl.glFrontFace(GL2.GL_CCW);

// Or clockwise:
gl.glFrontFace(GL2.GL_CW);
```

### Positioning Light Sources

* Light sources are geometric objects whose positions or directions _are affected by the model-view matrix_
* Users positions can be written to be moved etc. by manipulating the vertex floats

### Specifying Materials

```java
GLfloat ambient[] = {1.0, 0.0, 0.0, 1.0};
GLfloat diffuse[] = {1.0, 0.0, 0.0, 1.0};
GLfloat specular[] = {1.0, 1.0, 1.0, 1.0};

gl.glMaterialfv(GL2.GL_FRONT, GL2.GL_AMBIENT, ambient);
gl.glMaterialfv(GL2.GL_FRONT, GL2.GL_DIFFUSE, diffuse);
gl.glMaterialfv(GL2.GL_FRONT, GL2.GL_SPECULAR, specular);
gl.glMaterialf(GL2.GL_FRONT, GL2.GL_SHININESS, 100.0);

// Simulate a physical light source by giving an emissive component
GLfloat emission[] = {0.0, 0.3, 0.3, 1.0};
gl.glMaterialf(GL2.GL_FRONT, GL2.GL_EMISSION, emission);
```

### RGB Values for Lights and Materials

* For light, RGB values represent the color of the light
* For materials, RGB values represent the reflected color
* So if an OpenGL
	* light has components `(LR, LG, LB)`
	* material has components `(MR, MG, MB)`
	* the light arrives at the eye given by `(LR×MR, LG×MG, LB×MB)`



