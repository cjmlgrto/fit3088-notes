[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# 3D Transformations

## The Camera Analogy

* **Viewing Transformation** — setting up tripod and pointing camera
* **Modelling Transformation** — arranging the scene
* **Projection Transformation** — adjusting lens or zoom
* **Viewport Transformation** — how large you want the photo to be
* Given a set of coordinates `v` and transformation `M`, then `v' = Mv`

### Viewing & Modelling Transformation

* `modelview(GL_MODELVIEW)` matrix — combined viewing and modelling transformations, applied to incoming object coordinates to yield eye coordinates
* `glLoadIdentity()` matrix — called to reset the ModelView matrix
* `gluLookAt(GLdouble eyex, GLdouble eyey, GLdouble eyez, GLdouble centerx, GLdouble centery, GLdouble centerz, GLdouble upx, GLdouble upy, GLdouble upz)` — viewing transformation function
	* e.g. `gluLookAt(0.0, 0.0, 5.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0)`
* `glTranslatef(x, y, z)` — used to model/move the objects in the scene

### Projection Transformation

* Selecting using `GL_PROJECTION`
	* `glMatrixMode(GL_PROJECTION)`
	* `glLoadIdentity()`

#### Perspective Projection

* `glFrustrum(left, right, bottom, top, near, far)` calculates a matrix that specifies the perspective viewing volume
* `gluPerspective(fovy, aspect, near, far)` is the same as above, but uses aspect ratio to specific the viewbox and the angle (`fovy`) of viewing

#### Orthographic Projection

* `glOrtho(left, right, bottom, top, near, far)` calculates a viewing matrix where distance of objects from a camera doesn't affect its perceived size

### Viewport Transformation

* **Viewport** defines the size and location of the final processed image in the application window
* `glViewPort(x, y, width, height)` specifies the viewport
* `reshape()` is called to assign new width and height variables on window resize

### Example

```java
public void reshape(GLAutoDrawable d, int x, int y, int w, int h) {
	GL2 gl = d.getGL().getGL2();
	
	if (h == 0) h = 1;
	float aspect = (float) w / h;
	
	gl.glViewport(0,0, w, h);
	
	gl.glMatrixMode(GL_PROJECTION);
	gl.glLoadIdentity();
	glu.gluPerspective(45.0, aspect, 0.1, 100.0);
	
	gl.glMatrixMode(GL_MODELVIEW);
	gl.glLoadIdentity();

}
```

## 3D Transformation

### Translation

* `x' = x + ∆x`
* `y' = y + ∆y`
* `z' = z + ∆z`
* `w' = w`
* And expressed as a vector:

```
|x'|   |1 0 0 ∆x| |x|
|y'| = |0 1 0 ∆y| |y|
|z'|   |0 0 1 ∆z| |z|
|w'|   |0 0 0 1 | |w|
```

### Scaling

* `x' = ax`
* `y' = by`
* `z' = cz`
* `w' = w`
* And expressed as a vector:

```
|x'|   |a 0 0 0| |x|
|y'| = |0 b 0 0| |y|
|z'|   |0 0 c 0| |z|
|w'|   |0 0 0 1| |w|
```

### Shearing

* `tanθ = y/∆x` → `cotθ = ∆x/y`
* `x' = x + ycotθ`
* `y' = y`
* `z' = z`
* `w' = w`
* And in vector notation:

```
|x'|   |1 cotθ 0 0| |x|
|y'| = |0 1    0 0| |y|
|z'|   |0 0    1 0| |z|
|w'|   |0 0    0 1| |w|
```
### Rotation

* `x' = rcos(θ + ɸ) = xcosθ - ysinθ`
* `y' = rsin(θ + ɸ) = ycosθ + xsinθ`
* `z' = z`
* `w' = w`
* `glRotate*(θ, 0, 0, 1)` for z-axis
* `glRotate*(θ, 1, 0, 0)` for x-axis
* `glRotate*(θ, 0, 1, 0)` for y-axis
* To rotate about an arbitrary point `(x,y)`, 
	* it must be transformed to origin
	* rotated
	* then transformed back to its original position
* And expressed as a vector:

```
Z ROTATION:
|x'|   |cosθ -sinθ 0 0| |x|
|y'| = |sinθ cosθ  0 0| |y|
|z'|   |0    0     1 0| |z|
|w'|   |0    0     0 1| |w|

X ROTATION:
|x'|   |1 0     0     0| |x|
|y'| = |0 cosθ  -sinθ 0| |y|
|z'|   |0 sinθ  cosθ  0| |z|
|w'|   |0 0     0     1| |w|

Y ROTATION:
|x'|   |cosθ  0 sinθ 0| |x|
|y'| = |0     1 0    0| |y|
|z'|   |-sinθ 0 cosθ 0| |z|
|w'|   |0     0 0    1| |w|
```

### Clearing Color and Depth Buffer

* `gl.glClearColor(r,g,b,a)`
* `gl.glClear(GL2.GL_COLOR_BUFFER_BIT)`
* Or for both: `gl.glClear(GL2.GL_COLOR_BUFFER_BIT | GL2.GL_DEPTH_BUFFER_BIT)`