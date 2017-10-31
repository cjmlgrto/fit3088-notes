[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# Tessellation

* **Simple Polygon**:
	* Edges intersect only at vertices
	* No duplicate vertices
	* Exactly two edges meet at any vertex
* **Tessellation** — surface subdivision into simpler polygons, used for:
	* Display of concave polygons
	* Polygons containing holes
	* Polygons with intersecting edges
* **Winding Number** — the signed number of revolutions we make around that point while travelling once around the contour
	* **Counterclockwise** is positive
	* **Clockwise** is negative
* **Winding Rule** — classifies which region is "inside" a polygon (aka filled) given the winding number of a point
	* e.g. if a point is "3" and the winding rule is "odd" then, it will be filled

## Tessellation in OpenGL

* `gluNewTess()` — creates a new tessellation object
* `gluTessCallback()` — used to register callback functions to perform operations during tessellation
* `gluTessProperty()` — specify properties
	* Create and render tessellated polygons by specifing the **countours** of one or more closed polygons
* `gluDeleteTess()` — to delete a tessellation object


### Example

Inside the `display()` method:

```java
GLUtessellator tobj = glu.gluNewTess();

// vertexCallback
glu.gluTessCallback(tobj, GLU.GLU_TESS_VERTEX, tessCallback);
// beginCallback
glu.gluTessCallback(tobj, GLU.GLU_TESS_BEGIN, tessCallback);
// endCallBack
glu.gluTessCallback(tobj, GLU.GLU_TESS_END, tessCallback);
// errorCallback
glu.gluTessCallback(tobj, GLU.GLU_TESS_ERROR, tessCallback);
// combineCallback
glu.gluTessCallback(tobj, GLU.GLU_TESS_COMBINE, tessCallback);

// Rectangle with triangular hole inside

gl.glNewList(startList, GL2.GL_COMPILE);
gl.glShadeModel(GL2.GL_FLAT);
glu.gluTessBeginPolygon(tobj, null);

// draw rectangle
glu.gluTessBeginContour(tobj);
glu.gluTessVertex(tobj, rect[0], 0, rect[0]);
glu.gluTessVertex(tobj, rect[1], 0, rect[1]);
glu.gluTessVertex(tobj, rect[2], 0, rect[2]);
glu.gluTessVertex(tobj, rect[3], 0, rect[3]);
glu.gluTessEndContour(tobj);

// draw triangle
glu.gluTessBeginContour(tobj);
glu.gluTessVertex(tobj, tri[0], 0, tri[0]);
glu.gluTessVertex(tobj, tri[1], 0, tri[1]);
glu.gluTessVertex(tobj, tri[2], 0, tri[2]);
glu.gluTessEndContour(tobj);

glu.gluTessEndPolygon(tobj);
gl.glEndList();

```

