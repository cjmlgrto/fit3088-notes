[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# Clipping

* **Culling** — removing primitives outside the view colume
* **Accepted** — primitives within the view volume
* **Clipping** — primitives that are partially within the view volume
* Can occur at one or more places in the viewing pipeline
* In OpenGL, primitives are clipped against a 3D view volume before rasterization

## Clipping in 2D

### Cohen-Sutherland Algorithm

1. For each endpoint `(p1, p2)` of a line:
	* Compute the **outcode**
2. Compare endpoints
	* `p1 = p2 = 0` — the entire line segment is _inside_
	* `p1 ≠ 0 && p2 = 0` or vice-versa — _clipped_
	* `p1 && p2 ≠ 0` — _culled_
	* `p1 && p2 = 0` — both endpoints are outside, but middle segment could be _inside_

* Works best when there are many line segments but few are actually displayed
* This algorithm cna be extended to three dimensions
* But it must be used recursively

#### Algorithm for Outcode

```c
typedef int OutCode;
const int INSIDE = 0;
const int LEFT = 1;
const int RIGHT = 2;
const int BOTTOM = 4;
const int TOP = 8;

OutCode ComputeOutCode(double x, double y) {
	Outcode code;
	code = INSIDE;
	if (x < xmin)
		code |= LEFT;
	else if (x > xmax)
		code |= RIGHT;
	if (y < ymin)
		code |= BOTTOM;
	else if (y > ymax)
		code |= TOP;
	return code;
}
```

#### Formulas for Clipping

* Let `m = (x1-x0)/(y1-y0)`
* Let `n = (y1-y0)/(x1-x0)`
* Top
	* `y = y_max`
	* `x = x0 + m(ymax - y0)`
* Bottom
	* `y = y_min`
	* `x = x0 + m(ymin - y0)`
* Right
	* `x = xmax`
	* `y = y0 + n(xmax - x0)`
* Left
	* `x = xmin`
	* `y = y0 + n(xmin - x0)`

### Liang-Barsky Clipping

* `p(a) = p1 + a(p2 - p1)`
	* `x(a) = x1 + a(x2 - x1)`
	* `y(a) = y1 + a(y2 - y1)`
* A point `(x,y)` is in the clip window if:
	* `xmin ≤ x(a) ≤ xmax`
	* `ymin ≤ y(a) ≤ ymax`
* Which can be expressed as the 4 inequalities:
	* `ap_i ≤ q_i : i ∈ [1,4]`
* Where
	* `p1 = -∆x` and `q1 = x1 - xmin` — Left
	* `p2 = ∆x` and `q2 = xmax - x1` — Right
	* `p3 = -∆y` and `q3 = y1 - ymin` — Bottom
	* `p4 = ∆y` and `q4 = ymax - y1` — Top
* Given these,
	* If `p_i = 0` a line is parallel for that boundary
	* If `q_i < 0` line is completely outside
		* `u_i = q_i/p_i` where `t1 = max(0, u_i)`
	* If `p_i < 0` line goes outside to inside
		* `u_i = q_i/p_i` where `t2 = max(1, u_i)`
	* If `t1 > t2`, then the line is completely outside
	* Else, clipped between `t1` and `t2`

### Pipeline Clipping

* Involves clipping objects according to the order:
	* Top (clip all those sticking out above)
	* Bottom (clip all those sticking out below)
	* Right
	* Left

### Polygon Clipping

* Involves line clipping and pipeline clipping

## Clipping in 3D

* Done by finding the intersection between a line (vector in 3D) and a plane
* The _Cohen-Sutherland_ algorithm can be extended into 3D space by using a 6-bit outcode instead of a 4 bit outcode (i.e. front and behind is added as a comparison)