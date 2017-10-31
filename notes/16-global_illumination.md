[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# Global Illumination

## Local vs. Global Illumination

* OpenGL uses the [Phong Model](https://github.com/cjmlgrto/fit3088-notes/blob/master/notes/10-lighting.md) which is a local illumination model
	* It is very simplified
	* Doesn't cater for bouncing light
	* Uses basic light sources
	* Difficult to get reflections, shadows
	* Performance-heavy to simulate Global Illumination techniques
	* Also cannot be approximated well
* Global Illumination models are more sophisticated
	* **Ray Tracing** — good for rendering reflections & transparency
	* **Radiosity** — good for diffuse surfaces

## Ray Tracing

* Logical extension to rendering with a local lighting model
* Works on a pixel-by-pixel basis
* Based on observation that the only light rays that matter are those that enter the lens of the synthetic camera
* So, reverse the direction of the rays and consider only those rays that start at the center of projection — these are **cast rays**
* At least one ray is assigned through each pixel
* Each ray cast either:
	* Intersects a light source
	* Goes to infinity without striking anything
* If we compute the shade at the point of intersection, using the Phong Model, we would produce the same image as would our local renderer
* How rays enter the lens:
	* Directly from the source
	* From interaction with a surface visible to the camera
	* After multiple reflections from surfaces
	* After transmission through one or more surfaces
* Rather than immediately applying our reflection model, we first check whether the point of intersection between the cast ray and the surface is illuminated — **shadow rays** are computed from the point on the surface to each source

### Shadow Rays

* If a shadow ray intersects a surface before it meets the source, the light is blocked from reaching the point under consideration, and this point is in shadow (at least from the source) — as such, no lighting calculation is needed
* For highly reflective surfaces, we can follow the shadow ray as it bounces from surface to surface, until it either goes off to infinity or intersects a source
* If all surfaces are opaque, we do not consider light scattered from surface to surface
* Such calculations are done recursively and take into account any absorption of light at surfaces
* Ray tracing is particularly good at handling surfaces that both reflect and transmit light through refraction
* Usinng our basic paradigm, we follow a cast ray to a surface with the property that if a ray from a source strikes a point, then the light from the source is partially absorbed and some of this light contributes to the diffuse reflection term
* The rest of the incoming light is divided between a transmitted ray and a reflected ray
* If a light source is visible at the intersection point, we need to to:
	* Compute the contribution from the light source at the point, using our standard reflection model
	* Cast a ray in the direction of a perfect reflection
	* Cast a ray in the direction of the transmitted ray

### Simple Ray Tracer

```java
color c trace(point p, vector d, int step) {
	color local, reflected, transmitted;
	point q;
	normal n;
	
	if (step > max) return (background_color);
	q = intersect(p, d, status);
	if (status == light_source) return (light_source_color);
	if (status == no_intersection) return (background_color);
	
	n = normal(q);
	r = reflect(q, n);
	t = transmit(q, n);
	local = phone(q, n, r);
	
	reflected = trace(q, r, step+1);
	transmitted = trace(q, t, step+1);
	
	return (local + reflected + transmitted);
}

```

## Radiosity

* **Radiosity** — the amount of light leaving a surface
* Technique involves calculating the radiosity of surfaces in a scene and reflecting the light accordingly based on the laws of energy conservation
* Primarily addresses _diffuse lighting_ — specular effects need to be handled by another method
* It is a global illumination model because it models the effect of light energy being bounced around between objects in the scene
* Two important features of this approach:
	* Light sources are treated no differently from any other surface
	* Radiosity equations are solved once before the scene is rendered, so the scene can be viewed from anywhere without re-computation
* Radiosity is made up of two components:
	* the light reaching it from other surfaces and scattered by it (**Form Factor**)
	* the light which it generates directly (usually light sources)
* `Radiosity_i = Emission_i + Reflectivity_i × ∫(Radiosity_j × FormFactor_j)`
	* `i` and `j` are two surfaces
* It is impractical to calculate radiosity at every point — instead, a surface is divided into **patches**
	* Each patch is assumed to be energetically uniform
	* Each patch is shaded using Gouraud-shade triangles
	* A good estimate of radiosity is at the center of the patch, then interpolated with the triangles
	* An advantage of this method: we can have lights of any shape built from patches, not just point sources
	* **The radiosity of any patch depends on the radiosity of every other patch**

### Radiosity Equations

* `A_i` is the area of the patch `i`
* `B_i` is the radiosity of the patch `i`
* `E_i` is the generated energy per unit area per time (light source only)
* `R_i` is the reflection coefficient, the fraction of the incident energy that is reflected
* `F_ij` is the fraction of the energy which leaves patch `j` and reaches patch `i` (the _form factor_)
* `∫_j F_ij × B_j × d × A_j` is the total amount of energy reaching patch `i`
* But some of this is absorbed and the rest is reflected, so we multiply by `R_i`
* Finally, we get: `B_i × d × A_i = E_i × d × A_i + R_i ∫_j F_ij × B_j × d × A_j` as the **general radiosity equation**

### How to use Radiosity

1. Divide the scene into a mesh of patches
2. Compute the form factors
3. Solve the radiosity equations
4. Place the viewer in the scene and render conventionally

### Progressive Radiosity

* The major problem of this approach is the 
	* `O(N^2)` cost of calculating all the form factors
	* `O(N^2)` cost of storing them
* Progressive radiosity does this in `O(N)`
	* Instead of working on solutions to all patches simultaneously, the effects of one specific patch on all the others are calculated in full — then looped over all patches, accumulating contributions
	* If we iterate this process "forever" we arrive at the same solutions as we did calculating it in the original way
* "Important" surfaces can be processed first (e.g. light sources)
	* This is sometimes called the _shooting_ variant, because the energy from one patch is shot out to all the others
* When working iteratively, we have to be careful not to count energies more than once — we initialise all radiosities to zero, except for those of the light source patches
* Radiosity cannot handle highlights so it is often combined with Ray Tracing or Light Map techniques (which are visually important)

