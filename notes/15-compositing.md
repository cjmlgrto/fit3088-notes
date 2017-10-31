[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# Compositing

* Both the Color Buffer and the Depth Buffer have limited resolution — both spatially and in depth
* 2D buffer is a block of memory with `nm k` _bit_ elements
* **Frame Buffer** refers to the set of buffers the graphics system uses for rendering
* At a given spatial location in the frame buffer, the `k` bits can include 32-bit RGBA colors, floats representing depths, or bits that can be used for masks
* **Compositing** — when fragments from multiple objects can can contribute to the same color pixel

## Opacity and Blending

* The opacity of a surface is a measure of how much light penetrates through that surface
* In RGBA mode, if blending is enabled, the value of A controls how the RGB values are written into the frame buffer
* As a polygon is processed, pixel-sized fragments are computed and if they are visible are assigned colors based on the shading model in use
* If we regard the fragment as the source pixel and the frame-buffer pixel as the destination, we can combine these values in various ways
* `s = [s_r, s_g, s_b, s_a]` (source)
* `d = [d_r, d_g, d_b, d_a]` (destination)
* `b = [b_r, b_g, b_b, b_a]` (source blend factor)
* `c = [c_r, c_g, c_b, c_a]` (destination blend factor)
* `d' = [b_r×s_r + c_r×d_r, b_g×s_g + c_g×d_g, b_b×s_b + c_b×d_b, b_a×s_a + c_a×d_a]` (final destination)
* Many applications use the source's alpha as the source blending factor, and those (1 - source alpha) for the destinatio factor — this ensure that neither colors nor opacities can saturate
* Enabled with:
	* `glEnable(GL_BLEND)`
	* `glBlendFunc(source_factor, destination_factor)`
* Need to override the Z-buffer algorithm to enable this blending
	* `glDepthMask(GL_FALSE)`
	* This function makes the depth buffer read-only for any polygon that is translucent
	* When the depth buffer is read-only, a translucent polygon that lies behind any opaque polygon already rendered is discarded
	* A translucent polygon that lies in front of any polygon that has already been rendered is blended with the color of the polygons it is in front of
	* However, because the Z-buffer is read-only for this polygon, the depth values in the buffer are unchanged