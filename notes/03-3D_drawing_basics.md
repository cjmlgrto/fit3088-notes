[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# 3D Drawing Basics

## OpenGL

* **OpenGL** is a hardware and OS independent standard for real-time 2D and 3D graphics programming
* It is an open standard, cross-platform, with bindings for many languages (Java, Perl, C, Python...)
* Concept: think of the graphics system as a _finite state machine_ in which functions are called to change the state of the program (i.e. what is displayed onscreen)
* Pipeline:
	* OpenGL Application (→ Transform → Project → Clip → down to Frame Buffer below)
	* Pixel Operations
	* Frame Buffer

### Java and OpenGL: JOGL

* **JOGL** is a Java binding for OpenGL
* Requires to link with two main files: `jogl-all.jar` and `gluegen-rt.jar`

### OpenGL Drawable: `GLCanvas` and `GLJPanel`

* `GLCanvas`: a heavyweight AWT component (subclass of `java.awt.Canvas`), also implements `GLAutoDrawable`. This provides:
	* access to the `GL` object for calling OpenGL routines
	* a callback mechanism (`GLEventListener`) for performing OpenGL rendering
	* a `display()` method for forcing OpenGL rendering to be performed synchronously
	* AWT- and Swing-independent abstractions for getting and setting the size of the widget and adding and removing event listeners

```java
GLCanvas canvas = new GLCanvas();
JFrame frame = new JFrame();
frame.getContentPane().add(canvas);
canvas.addGLEventListener(...);
```

* `GLJPanel`: a lightweight Swing component (subclass of `javax.swing.JPanel`)

```java
GLJPanel canvas = new GLJPanel();
JFrame frame = new JFrame();
frame.getContentPane().add(canvas);
canvas.addGLEventListener(...);
```
* `GLDrawable`: target for openGL rendering to be performed
* `GLCapabilities`: specifies requests for OpenGL parameters such as bit depth, single or double buffering, depth buffering, etc.


#### Callbacks via `GLEventListener`

* JOGL uses event callbacks to respond to graphics events
* The interface `GLAutoDrawable` defines these abstract methods to add or remove a `GLEventListener`
* Events include: drawing a window, resizing the window, mouse movements...
* Applications implement `GLEventListener` to perform OpenGL drawing
* The `init()` method is called when a new OpenGL context is created for the given GLAutoDrawable — this method is used for OpenGL initialisation
* The `display()` method is called to perform per-frame rendering
* The `reshape()` method is called when the drawable has been resized — the default implementation resizes the OpenGL viewport, so this method is often empty

#### Animator

* We need an animator to drive the `display()` method in a loop to refresh the display regularly
* JOGL provides two animator classes: `Animator` and `FPSAnimator` via `com.jogamp.opengl.util`.

#### Control Functions: `GLUT` in JOGL

* In OpenGL it is assumed that the output will appear in a window which is being managed by a windowing system
* `GLUT` is a library of functions that provides a simple interface between the graphics progamming system and windowing system
* Applications that use `GLUT` should run under multiple windowing systems (e.g. X11, Win32, etc.)
* GLUT is available in JOGL:
	* `import com.jogamp.opengl.util.gl2.GLUT`
* OpenGL uses the term **window** to denoate a rectangular area of the display
* Positions in the window are measured in window or screen coordinates, where units are pixels
	* Screen coordinates are 2D
	* World coordinates are 3D
* References to the window that contains the output of the graphics program are relative to one corner of the window
* Usually, the **lower left** corner is the origin `(0,0)`
* The window need not take up all display space
* OS window defines: **Frame Buffer** (color information), **Depth Buffer** (hidden surface removal) and can have single or double buffers

### Example: Drawing in JOGL

```java
public void display(GLAutoDrawable d) {
	gl.glClear(GL_COLOR_BUFFER_BIT);
	gl.glBegin(GL_POLYGON);
	gl.glvertex2f(-0.5, -0.5);
	gl.glVertex2f(-0.5, 0.5);
	gl.glVertex2f(0.5, 0.5);
	gl.glVertex2f(0.5, -0.5);
	gl.glEnd();
	gl.glFlush();
}
```

### Aspect Ratio and Viewports

* A **viewport** is a rectangular area of the display window
* By default, it is the entire windw — but can be defined not to take up the entire display window via:
	* `void glViewport(GLint x, GLint y, GLsizei w, GLsizei h)`
	* where `(x, y)` is the lower left corner of the viewport (measured relative to the lower left corner of the display window, in pixels)
	* `w` and `h` give the width and height of the viewport
* The viewport is part of the OpenGL state — if it is changed between rendering objects (or re-render the same objects with the viewport change), we achieve the effect of multiple viewports with different images in different parts of the window

### Double Buffer

* Typically the frame buffer is redisplayed at a regular rate, known as the **refresh rate**, which is in the range of **60 to 100 hz** (or frames per second)
* An application program operates asynchronously and can cause changes to the frame buffer at any time
* Hence, a redisplay of the frame buffer can occur when its contents are still being altered by the application and the viewer will see only a partially drawn display
* Some operating systems give the user a parameter to set that will couple or sync the drawing into and display of the frame buffer
* The more common solution is **double buffering** — instead of a single frame buffer, the hardware has to frame buffers
	* The **front buffer** is the one that is displayed
	* The **back buffer** is then available for constructing what we would like to display
	* Once the drawing is complete, we swap the front and back buffers

### Objects and Viewers

#### Synthetic Camera Model

* Creating a CG image is similar to forming an image using an optical system
* Specification of the objects is independent of the specification of the viewer
* We find the image of a point on the object on the virtual image plane by drawing a line, called a **projector**, from the point to the center of the lens or the **center of projection (COP)**
* The virtual image plane that we have moved in front of the lens is called the **projection plane**

#### Viewing

* OpenGL uses a **synthetic camera model** to view the world
* The programmer specifies the objects in a 3D world, and the graphics program shows what this would look like to a synthetic camera placed at a particular position in the 3D world
* OpenGL handles 2D graphic as a special case of 3D graphic, however it is possible fto begin 2D viewing before progressing with 3D viewing
* 2D viewing is based on taking a rectangular area of a 2D world and mapping its contents to an area of a 2D display
* The area of the 2D world that appears in the image is known as a **viewing rectangle** — objects outside it are not displayed
* Objects that straddle the border of the viewing volume get **clipped**
* In OpenGL, 2D viewing is a special case of 3D viewing — in 3D, objects within a **view volume** are displayed on a 2D surface
* The viewing volume is a 2×2×2 cube with its centre at the origin of the coordinate system — to convert the 3D coordinates to 2D, an orthographic projection is carried out

### Primitives and Attributes

* Basic OpenGL primitives are specified via points in space or vertices
* By default, a point covers 1 pixel
* Objects are defined by sequences of the form:

```java
glBegin(type);
glVertex*(...);
...
glVertex*(...);
glEnd();
```
* Other code and OpenGL function calls can occur between `glBegin()` and `glEnd()`

#### `glVertexNTV(...)`

* `n` denotes the number of dimensions (2, 3 or 4)
* `t` denotes the data type (`i` for integer, `f` for float, `d` for double)
* `v` if present, denotes variables specified through a pointer to an array and not an argument list

Examples:

```java
glVertex2i(GLint Xi, GLint Yi);
glVertex3f(GLfloat x, GLfloat y, GLfloat z);
GFloat vertex[3]; glVertex3fv(vertex);
```

* The underlying representation is always the same and actually uses 3 coordinates
* All other geometric types are specified either in terms of points or line segments
* Line segments themselves are specified by pairs of points
* Apart from points and line segments, other basic geometric types have interiors that can be coloured in different ways
* `GL_POINTS` for pixel dots
* `GL_LINES` where successive pairs of points form a line
* `GL_LINE_STRIP` where each dot is connected to the next
* `GL_LINE_LOOP` which closes a path

#### Polygon Basics

* Line segments and polylines can model the edges of objects
* An object that has a border that can be described by a line loop and which has an interior is called a **polygon**
* Polygons can be displayed rapidly by graphics hardware can can be used to approximate curved surfaces
* In two dimensions, as long as no two edges of a polygon cross each other, the polygon is _simple_
* Some implementations require polygons to be convex — a polygon is **convex** if a line segment connecting any two points in the polygon or its boundary does not go outside the polygon
* In 3 dimensions, a polygon is not necessarily planar (flat)
* `GL_POLYGON` fills in all connected points
* `GL_QUADS` fills in only 4 pairs of points
* `GL_TRIANGLES` fills in only 3 pairs of triangles
* `GL_TRIANGLE_STRIP`
* `GL_QUAD_STRIP`
* `GL_TRIANGLE_FAN`

#### Triangulation

* General polygons are problematic — a set of vertices may not all lie in the same plane or specify a polygon is neither simple nor convex
* Such problems do not arise with triangles: as long as three vertices of a triangle or anot collinear, its interior is well-defined and the triangle is simple, flat and convex
* Consequently, triangles are easy to render
* The usual strategy is to start with a list of vertices and generate a set of triangles consistent with the polygon defined by the list — this is known as **triangulation**

#### Curved Objects

* To create curved objects, we can approximate curves by line segments and meshes of convex polygons — this is known as **tessellation**
* The GLU contains function calls to perform tesselation and to render Non-Uniform Rational Basis Spline **(NURBS)** curves and surfaces
	* A NURBS curve is defined by its order, a set of weighted control points, and a knot vector
	* NURBS curves and surfaces are generalisations of both B-splines and Bezier curves and surfaces

#### Text in OpenGL

* There are two types of text: **stroke** and **raster**
* **Stroke** text is created using vertices and line segments — it can be transformed and viewed like other graphical objects
	* A character need only be defined once
	* Defining every character for 128 or 256 characters in a particular font can require a lot of details
	* Manipulating stroke characters requires a lot of resources
* **Raster** text (aka bitmap) is simple and fast
	* Each character is described as a rectangular block of bits
	* Hardware can move raster characters quickly into the frame buffer via bit-block-transform (**bitblt**) operations
	* OpenGL allows applications to manipulate the frame buffer directly
	* Raster characters can only be increased in size by **replicating**
	* They cannot be transformed however
	* Fonts might reside in hardware on the graphics display and may not be portable
* OpenGL does not have text primitives — GLUT can create characters from other primitives (it provides a few bitmap and stroke character sets in software)
* Characters are placed in the current raster position in the display

#### Attributes

* Graphics primitives may be of the same type but displayed differently
* An attribute is any property that determines how a geometric primitive is to be rendered
* e.g. color (`glColor`), line width (`glLineWidth`) or a pattern to fill a polygon (`glPolygonStipple`)
* **Immediate Mode**:
	* Primitives are not stored in the system, but are passed through the graphics pipeline for possible display as soon as they are define
	* When a primitive is defined, the present state (values) of the attribtes are used — there is no memory of the primitive in the system
	* The primitive's image appears on the display: once the image is erased, there is no memory of the primitive — it must be defined again
	* In order to allow objects to be re-displayed, they must be kept in memory
* Points
	* Color
	* Size
* Lines
	* Color
	* Thickness
	* Type (solid, dashed or dotted)
* Filled primitives
	* Fill
	* Edges
* Stroke text
	* direction
	* path
	* height and width
	* font
	* style (bold, italic, underlined)

#### Color

* In OpenGL, RGB values range from 0 to 1
* `gl.glColor3f(1.0,1.0,1.0)`

 