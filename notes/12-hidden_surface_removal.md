[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# Hidden Surface Removal

* **Hidden Surface Removal** is done to determine what part, if any, of each object within the view volume is visible to the viewer
* Two types of algorithms:
	* **Object Space** — compares objects and parts of objects to each other to determine which surface and lines should be invisible
	* **Image Space** — determine visibility at each pixel position on the projection plane

## Object Space

* For a scene composed of `k` 3D opaque flat polygons:
	* We consider each as an individual object
	* We pick one of the `k` polygons and compare it pairwise with the remaining `k-1` polygons
	* We repeat with all the other `k-1` polygons
	* Complexity is `O(k^2)` but can be reduced to `O(k log k)`
* Works best for scenes that contain few polygons

## Image Space

* Consider a ray that leaves the center of projection and passes through a pixel
* We can intersect this ray with each of the planes determined by our `k` polygons, determine for which planes the ray passes through
* For those rays, find the intersection closest to the center of projection — we color this pixel with the color of the polygon at the point of intersection
* For `n` by `m` viewport, we have to carry `nmk` operations — `O(k)` complexity
* In practice, image space algorithms are better
* However, because the work on the pixel-level, their accuracy is limited by the resolution of the Frame Buffer

## Back-Face Removal

* In OpenGL, we can choose to render only front-facing polygons
* We can reduce the work required for hidden surface removal by eliminating all back-facing polygons before we apply any other hidden-surface removal algorithm
* If θ is the angle between the normal and the viewer, then the polygon is facing forward if and only if:
	* -90 ≤ θ ≤ 90
	* or cosθ ≥ 0
	* or n^→ . v^→ ≥ 0
* Applied after transformation to normalized device coordinates — in this case, all views are orthographic with the direction of projection along the z-axis
* `V = vert([0 0 1 0])`, and so the plane equation is `ax + by + cz + d = 0`

## The Painter's Algorithm

* Sort all polygons in a scene by their depth, then paints them in this order (furthest to closest)
* Could fail during cyclic overlap — in this case, polygons need to be cut
* `N log N` sorting using Binary Tree
	* If the tree is not balanced, tends to act as `O(N^2)`

## Scan-Line Algorithm

* Scan projection matrix left to right for rays of a polygon that hit it
* Edges are detected which correspond to the drawing of that polygon as facing the projection

## Z-Buffer Algorithm

* A projector from the center of projections, passes through two polygons
* Because one of the surfaces is closer to the viewer than the other, it is the circle's color that determines the values placed in the color buffer
* As the surfaces are rendered, we will track the distance from the COP surface on each projector in a new memory location (Z-Buffer or **Depth Buffer**)
* The z-buffer has the same spatial resolution as the color buffers, and usually uses 32-bit floating point value to store depth information
* The depth buffer is initialised to a big negative number that corresponds to the farthest distance from the viewer
* For each polygon, the depth of each fragment is computed and compared to the value stored in the depth buffer at the corresponding location
* If the depth value of the current fragment is smaller than the depth buffer value, the color buffer is updated and the depth buffer buffer is updated with the fragment color and depth information
* In OpenGL, all you have to do is enable the Depth Buffer
* This is a form of hidden surface removal