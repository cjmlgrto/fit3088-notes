[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# 2D Transformations

Transformations:

* Change an object in some way
* Are called **affine** if they preserve straight lines
* Are state-based
* Accumulate
* Must be set before the object is drawn
* Are represented as matrices
* Exist on a stack that can be pushed and popped to control the cumulative effect of transformations

An example of a basic transformation (`translate(x, y)`) in Processing:

```java
pushMatrix();
translate(20, 0);
rect(0, 10, 70, 20); // Draws at (20, 10)
pushMatrix();
translate(30, 0);
rect(0, 30, 70, 20); // Draws at (50, 30)
popMatrix();
rect(0, 50, 70, 20); // Draws at (20, 50)
popMatrix();
translate(10, 30);
rect(0, 70, 70, 20); // Draws at (10, 100)
```

### Transformation Functions in Processing

* `pushMatrix()` saves the current transformation on a first-in-last-out stack
* `popMatrix()` pops the matrix off the stack, undoing the effect of any transformations since the last `pushMatrix()` call
* `rotate(angle_in_radians)` performs a rotation about the origin
* `rotate(radians(angle))` for degrees
* `scale(x, y)`
* In Processing, the order matters

## Affine Transformations

General 2D affine transformation:

* `x' = ax + by + c`
* `y' = dx + ey + f`

Which can be represented in a matrix as:

```
|x'|   |a b c|   |x|
|y'| = |d e f| × |y|
|1 |   |0 0 1|   |1|
```

### Translation

* Shifts all points by an equal amount
* `x' = x + ∆x`
* `y' = y + ∆y`

### Rotation

* Rotate a point by θ about the origin (in an anti-clockwise direction)
* In polar form:
* `x = rcosɸ`
* `y = rsinɸ`
* `x' = rcos(θ + ɸ) = xcosθ - ysinθ`
* `y' = rsin(θ + ɸ) = ycosθ + xsinθ`

In matrix form, we get:

```
|x'| = |cosθ -sinθ| × |x|
|y'|   |sinθ  cosθ|   |y|
```

### Scaling

* `x' = ax`
* `y' = by`

In matrix form, we get:

```
|x'| = |a 0| × |x|
|y'|   |0 b|   |y|
```

### Shearing

* A transformation in which all points along a given line _L_ remain fixed while other points are shifted _parallel to L_ by a distance proportional to their perpendicular distance from _L_
* X-Shear:
	* `x' = x + Shear_x(y)`
	* `y' = y`

And in matrix form:

```
|x'|   |1 S 0|   |x|
|y'| = |0 1 0| × |y|
|1 |   |0 0 1|   |1|
```

* Y-Shear
	* `y' = y + Shear_y(x)`
	* `x' = x`

And in matrix form:

```
|x'|   |1 0 0|   |x|
|y'| = |S 1 0| × |y|
|1 |   |0 0 1|   |1|
```

Where **S** is the shear function

## Combining Transformations

* Combining scaling and rotation is straightforward. If `R` deontes a rotation and `S` a scaling matrix, then
	* `A = RS` denotes scaling following by a rotation
	* `B = SR` denotes rotation followed by scaling
* Matrix multiplication is not commutative, so the order in which calculations are carried out is significant! That is, `AB != BA`.

## Homogenous Coordinates

* Representing a 2-vector (`(x,y)`) as a 3-vector (`(x, y, 1)`)
* `x' = 1x + 0y + 1t_x`
* `y' = 0x + 1y + 1t_y`
* And the following matrices:

```
|x'|   |1 0 t_x|   |x|
|y'| = |0 1 t_y| × |y|
|1 |   |0 0 1  |   |1|

For rotation:
|x'|   |cosθ -sinθ 0| × |x|
|y'| = |sinθ  cosθ 0|   |y|
|1 |   |0 0 1 |         |1|

For scaling:
|x'| = |a 0 0| × |x|
|y'|   |0 b 0|   |y|
|1 |   |0 0 1|   |1|
```

* If we use an affine transformation to produce a transformed point `p' = Ap` then if `A^(-1)` exists, we have `p = A^(-1)p`
* For the basic transformation, the inverse can be calculated:
	* T^(-1)(∆x, ∆y) = T(-∆x, -∆y)
	* R^(-1)(θ) = R(-θ)
	* S^(-1)(a, b) = S(1/a, 1/b)
* Furthermore, if the composite transformation is given by AB:
	* (AB)^(-1) = B^(-1)A^(-1)

## Matrix Functions in Processing

* `printMatrix()`
* `resetMatrix()`
* `applyMatrix()`, which takes 16 floats representing the matrix to be multiplied to the current transformation matrix

## Rotation about an arbitrary point

In Processing, rotation is applied to the origin. To rotate an object about an arbitrary point, `(x,y)`:

1. Translate `(x,y)` to the origin
2. Rotate
3. Translate back `(x,y)` to its original position

## Coordinate Systems

* **World Coordinates**: a user-defined coordinate system which has its own units of measures, axes and origin
* **Window Coordinates**: region of the 'world' that is visible
* **Screen Coordinates**: coordinate system used to address device display coordinates (usually pixels)
* **Viewport**: the rectangular region of the screen used to display the application
* `wx_min, wy_min` and `wx_max, wy_max` defines a window in the **world**
* `vx_min, vy_min` and `vx_max, vy_max` defines a window in the **screen**

### Viewport Transform

* `S_x = (vx_max - vx_min)/(wx_max - wx_min)`
* `S_y = (vy_max - vy_min)/(wy_max - wy_min)`
* Translate `wx_min, wy_min` to the origin
* Scale by `S_x, S_y`
* Translate origin to 	`vx_min, vy_min`

```
|1 0 vx_min|   | S_x 0 0|   |1 0 -wx_min|
|0 1 vy_min| × | 0 S_y 0| × |0 1 -wy_min|
|0 0 1     |   | 0 0 1  |   |0 0 1      |
```

