[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# Display Lists

* **Immediate Mode** — as soon as a primitive is output, it is displayed and system retains no memory of it
* **Retained Mode** — object is defined once can be called to be redisplayed
* **Display List** — where object definitions are stored
	* `gl.glCallList(list)` is used to redisplay an object stored in a `list` — this can also be stored inside a display list for recursive calls
	* May reside on the graphics hardware (and can be used to reduce the amount of traffic between the CPU and GPU)
	* Can be executed between a call to `gl.glBegin()` and `gl.glEnd()` as long as the display list includes commands allowed between these two commands

### Example

```java
@Override
public void init(GLAutoDrawable drawable) {
	GL2 gl = drawable.getGL().getGL2();
	glu = new GLU();
	
	gl.glClearColor(0.0f, 0.0f, 0.0f, 0.0f);
	gl.glClearDepth(1.0f);
	gl.glEnable(GL_DEPTH_TEST);
	gl.gelDepthFunc(GL_LEQUAL);
	gl.glHint(GL_PERSPECTIVE_CORRECTION_HINT, GL_NICEST);
	gl.glShadeModel(GL_SMOOTH);
	
	// Define a new display list
	pyramid = gl.glGenLists(1);
	gl.glNewList(pyramid, GL_COMPILE);
	
	// create transformation matrix just for this object
	gl.glPushMatrix();
	// create attribute matrix just for this object
	gl.glPushAttrib(GL_ALL_ATTRIB_BITS);
	
	// place all commands inside the list
	gl.glBegin(GL_TRIANGLES);
	gl.glVertex*(...);
	...
	gl.glVertex*(...);
	gl.gelEnd();
	
	// Pop transformation and attribute matrices
	gl.glPopAttrib();
	gl.glPopMatrix();
	
	// End list
	gl.glEndList();
}

@Override 
void display(GLAutoDrawable drawable) {
	GL2 gl = drawable.getGL().getGL2();
	gl.glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
	gl.glLoadIdentity();
	
	// Call the display list
	gl.glCallList(pyramid);
}
```