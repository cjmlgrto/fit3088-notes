[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# The Graphics Process

* The **Graphics System**
	* Input: Vertices
	* Output: Pixels in the _Frame Buffer_
	* Tasks:
		* Transformations
		* Clipping
		* Shading
		* Hidden-Surface Removal
		* Rasterization
	* Regardless of order:
		* Pass _every geometric object_ through the system
		* Assign _a color to every pixel_ in the color buffer that is displayed
	* Two strategies:
		* **Image-Oriented** — for each pixel, assign pixel
		* **Object-Oriented** — for each object, render the object

## Object-Oriented Strategy

* Vertices are defined and flow through a sequence of modules that transforms, colors, clips and rasterizes them
* Hardware for this is *fast and relatively inexpensive*
* Major limitations:
	* Large amount of memory required
	* High cost of processing each object independently
	* Entire color buffer (and various other buffers) must be the same size of the display and always available
	* Cannot handle global calculations
		* Because each primitive is processed independently and randomly
		* Complex shading effects (like reflections) cannot be handled unless approximated

## Image-Oriented Strategy

* Loop over pixels (or rows of pixels called **scanlines**) that make up the Frame Buffer
* For each pixel, work backward determining which geometric primitives contribute to its color
* Major disadvantage:
	* Don't know which primitives affect which pixels, unless we build a data structure from geometric data
* Can do global shading effects such as shadows and reflections

## Tasks in the Graphics Process

* **Modelling** — produce geometric objects (usually, a user program)
* **Geometry Processing** — determine which objects appear on the display
	* Projection — Change representations from object coordinates to camera or eye coordinates using model-view transformation
	* Normalisation — Transform vertices using the projection transformation to a normalised view volume in which objects that mights be visible are contained in a cube centered at the origin
	* Primitive Assembly — Vertices must be grouped into objects
	* Clipping, Shading, Hidden-Surface removal...
* **Rasterisation**
	* For lines: determines which fragments should be used to approximate a line between projected vertices
	* For polygons: determines which pixels lie inside the two-dimensional polygon determined by the projected vertices
	* Colors assigned to these fragments can be determined by the *vertex attributes* or obtained by *interpolating the shade** at the vertices
* **Fragment Processing**
	* Each fragment is assigned a color by the rasterizer then placed in the frame buffer
	* Separate pixel pipeline merges results with geometric pipeline at rasterization stage
	* Objects might be translucent: in this case, fragment colors are blended with those already in the color buffer