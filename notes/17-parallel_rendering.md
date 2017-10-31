[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# Parallel Rendering

* **Geometry Processing** is usually parallelized by assigning each processor a subset of the primitives in the scene
* **Rasterization** is usually parallelized by assigning each processor a portion of the pixel calculations
* The essence of the rendering task is to calculate the effect of each primitive on each pixel
* Due to the arbitrary nature of the modelling and viewing transformations, a primitive can fall anywhere on (or off) screen
* Thus rendering can be viewed as a problem of sorting primitives to the screen
* This sort involves a redistribution of data between processors, because responsibility for primitives and pixels is distributed

## Sort-First Rendering

* Distribute primitives early in the rendering pipeline, during geometry processing, to processors that can do the remaining rendering calculations
* This is generally done by dividing the screen into disjoint regions and making processors (called renderers) responsible for all rendering calculations that affect their respective screen regions
* Initially, primitives are assigned to renders in some arbitrary fashion
* When rendering begins, each renderer does enough transformation to determine into which region(s) each primitive falls, generally computing the screen-space bounding box of the primitive (pre-transformation)
* In some cases, primitives will fall into the screen regions of renderers other than the one on which they reside
* These primitives must then be redistributed over an interconnected network to the appropriate renderer
* This redistribution of primitives at the beginning of the process clearly involves overhead, since for some primitives, a portion of geometry processing is done on the wrong renderer

### Advantages

* Low communication requirements when the tessellation ratio and the degree of oversampling are high, or when frame-to-frame coherence can be exploited
* Processors implement entire rendering pipeline for a portion of the screen

### Disadvantages

* Primitives may clump into regions, concentrating work on a few renderers
* To take advantage of frame-to-frame coherence, complex data handling code is necessary

## Sort-Middle Rendering

* Primitives are redistributed in the middle of the rendering pipeline, between geometry processing and rasterization
* Primitives at this point have been transformed into screen coordinates, ready for rasterization
* Geometry processors are assigned arbitrary subsets of the primities to be displayed; rasterizers are assigned a portion of the display screen

### Advantages

* General and straightforward: redistribution occurs at a natural place in the pipeline

### Disadvantages

* High communication costs of tesselation is high
* Subject to load imbalance between rasterizers when primitives are distributed unevenly over the screen

## Sort-Last Rendering

* Defers sorting until the end of the rendering pipeline, after primitives have been rasterized into pixels, samples, or pixel fragments
* Processors are each assigned arbitrary subsets of the primitives
* Each computes pixel values for its subset, no matter where they are onscreen
* Renderers transmit these pixels over an interconnecting network to compositing processors which resolve the visibility of pixels from each renderer

### Advantages

* Renderers implement the full rendering pipeline and are independent until pixel merging
* Less prone to load imbalance

### Disadvantages

* Pixel traffic may be extremely high, particularly when oversampling