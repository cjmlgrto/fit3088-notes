[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# Projections

* **Center of Projection** — the point at which rays of projection meet
* **Direction of Projection** — when the center of projection reaches a distance nearing infinity, the rays of projection become parallel

## Orthogonal Projection

* **Orthogonal Projection** is a special case of parallel projection
* `x_p = x`
* `y_p = y`
* `z_p = z`

```
|x_p|   |1 0 0 0| |x|
|y_p| = |0 1 0 0| |y|
|z_p|   |0 0 1 0| |z|
|1  |   |0 0 0 1| |1|
```

## Projection Normalisation

* We work in 4 dimensions using homogenous coordinates
* Depth information (distance along a projector) is retained for _hidden surface removal_ (later in the pipeline)
* **Projection Normalization**:
	* Distort the object
	* Project object orthogonally
	* Projection is desired projection
	* Translate → Scale
* `x = ±1`
* `y = ±1`
* `z = ±1`
* `T = T( -[right+left]/2, -[top+bottom]/2, [far+near]/2 )`
* `S = S( 2/[right-left], 2/[top-bottom], 2/[near-far] )`
* `z = -near` → `z = -1`
* `z = -far` → `z = 1`
* And as such, the final **Projection Normalization Matrix** is:

```
N = ST = | 2/[right-left] 0 0 -[left+right]/[right-left] |
         | 0 2/[top-bottom] 0 -[top+bottom]/[top-bottom] |
         | 0 0 -2/[far-near] -[far+near]/[far-near] |
         | 0 0 0 1 |
         
Na^(→) = | 2a_x/[right-left] - [right+left]/[right-left] |
         | 2a_y/[top-bottom] - [top+bottom]/[top-bottom] |
         | 2a_z/[far-near] - [far+near]/[far-near] |
```

## Oblique Projection

* **Oblique Projection** — same as orthogonal, except the direction of projection is skewed by an angle θ
* `tanθ = z/(x_p - x)`
* `x_p = x + zcotθ`
* `y_p = y + zcotθ`
* `z_p = 0`
* Projection = `M_orth × S × T × H(θ, ɸ)`
* Where H is the oblique projection matrix:

```
H(θ, ɸ) = |1 0 cotθ 0|
          |0 1 cotɸ 0|
          |0 0 1    0|
          |0 0 0    1|
```

## Perspective Projection

* **Perspective Projection** — 
* `x/z = x_p/d` → `x_p = x/(z/d)` → `x_p = -xd/z`
* `y/z = y_p/d` → `y_p = y/(z/d)` → 'y_p = -yd/z`
* `x' = x`
* `y' = y`
* `z' = az + b`
* `w' = -z`
* `x'' = -x/z`
* `y'' = -y/z`
* `z'' = -([az + b]/z)`
* And in matrix form:

```
N = |1 0 0 0|
    |0 1 0 0|
    |0 0 a b|
    |0 0 -1 0|
```

* `z'' = -(a × -near + b)/(-near) = -1`
* `z'' = -(a × -far + b)/(-far) = 1`
* `a = (n+f)(n-f)` → `a = -(f+n)/(f-n)`
* `b = n × ([n+f]/[n-f] - [n-f]/[n-f])` → `b = 2nf/(n-f)` → `b = -2nf/(f-n)`
* And another matrix:

```
| 2near/[right-left] 0 [right+left]/[right-left] 0 |
| 0 2near/[top-bottom] [top+bottom]/[top-bottom] 0 |
| 0 0 -[far+near]/[far-near] [-2far × near]/[far-near] |
| 0 0 -1 0 |
```