[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# 2D Drawing Basics

## Frame Buffers

Inside a typical graphics system, a **frame buffer** is a special area in RAM that stores the intesity or color values of each pixel.

* Coordinates are non-negative integers
* Frame buffer memory is specified as **bit planes**
* With `n` bits per pixel (**bit depth**), there are `2^n` possible intensities
* For color displays, bit planes are required for each primary color (red, green, blue)

### Look-Up Tables

* Instead of storing the pixel color, frame buffers could instead be used to store a pointer or index to a pixel color.

### Hardware Display Systems

* **Resolution** refers to the number of points that can be displayed onscreen
* **Aspect Ratio** refers to the ratio of a display's width and height
* **Pixel Aspect Ratio** refers to the ratio of each pixel's width and height

## Processing

The Java-based API + IDE, [Processing](www.processing.org), could be used to develop interactive 2D graphics applications.

### Event-Driven Programming

Processing uses the Event-Driven Programming paradigm:

1. Events are emitted (mouse, keyboard, system, custom, etc.)
2. Events are lined up in a queue
3. The _Event Loop_ runs as there are still events in the queue
4. Event Handlers is where code executes

### Processing API

Made up of three components:

* Application (which makes function calls to and receives data from)
* The Graphics System (which changes application state, produces a visible output, while accepting and receiving input/output from)
* Input/Output Devices

### Processing Basics

An example of a basic Processing program that draws a rectangle. Note that the origin `(0,0)` is located at the top left of the window.

```java
void setup() {
	size(320,480);
	smooth();
	background(0);
}

void draw() {
	stroke(255);
	fill(50, 80);
	rect(50, 50, 100, 100);
}
```

#### Useful Functions

* `void setup()`
* `void draw()`
* `size(x,y)`
* `background(c)`
* `noLoop()`
* `smooth()`
* `stroke(c)`
* `fill(c)`
* `noStroke()`
* `noFill()`
* `strokeWeight(i)`
* `width` for window width
* `height` for window height

#### Color Modes

* **Hue, Saturation, Brightness**: `([0-360], [0-100], [0-100])`
* **Red, Green, Blue**: `([0-255], [0-255], [0-255])`

#### Drawing Primitives

* `triangle(x1, y1, x2, y2, x3, y3)`
* `line(x1, y1, x2, y2)`
* `point(x, y)`
* `ellipse(centerX, centerY, width, height)`
* `arc(centerX, centerY, width, height, start, stop)`
* `quad(x1, y1, x2, y2, x3, y3, x4, y4)`

#### Vertex Shapes

* `beginShape(KIND)` where `KIND` could be any of the following modes:
	* `POINTS`
	* `LINES`
	* `TRIANGLES`
	* `TRIANGLE_FAN`
	* `TRIANGLE_STRIP`
	* `QUADS`
	* `QUAD_STRIP`
* `vertex(x1, y1)`
* `endShape()`

#### Curves

* `beginShape()`
* `curveVertex(x, y)` or
* `bezierVertex(cx1, cxy1, cx2, cy2, x, y)` where it must be prefaced with a call to `vertex()` to set the first anchor point
* `endShape()`

#### Other Useful Functions

* `norm(value, low, high)` which normalises a value from another range into a value between 0 and 1
* `lerp(x1, x2, amt)`
* `map(x, low1, high1, low2, high2)`
* `constrain(x, min, max)`
* `random(min, max)`

#### Text in Processing

* Typefaces must be converted to a `.vlw` format
* `PFont fontObject = loadFont(filename)`
* `textFont(fontObject, size)`
* `fill(c)` to change the color of the text fill
* `textSize(size)`
* `textLeading(dist)` also known as line height
* `textAlign(LEFT CENTER OR RIGHT)`
* `textWidth()` which returns the pixel width of any string

#### Animation in Processing

* `draw()` loops, but its loop rate can be changed
* `frameCount` returns the current frame number
* `frameRate(n)` sets the frame rate to n per second
* `noLoop()` calls the draw function once

### Interaction in Processing

* `mouseX` and `mouseY` to track the cursor
* `pmouseX` and `pmouseY` to track previous mouse position
* `mousePressed == true`
* `mouseButton == LEFT, RIGHT OR CENTER`
* `keyPressed == true`
* `keyCode` for which key is pressed
* `mousePressed()` runs whenever a mouse button is pressed
* `mouseReleased()`
* `mouseMoved()`
* `mouseDragged()`