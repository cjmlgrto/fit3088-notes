[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# Textures

* **Textures** — patterns that could be used to add realism to a scene
* **Texture Elements (Texels)** — arrays of textures
* **Texture Coordinates** — `s` and `t`, used to scale textures according to the interval `[0,1]`

## Texture Mapping in OpenGL

1. Create a texture object and specify a texture for that object
2. Indicate how texture is to be applied to each pixel
	* `GL_MODULATE`: multiply color components
	* `GL_BLEND`: linearly blend color components
	* `GL_DECAL`: use texture color in RGB (if with alpha, interpolates texel and fragment color)
	* `GL_REPLACE`: use the texture color
3. Enable texture mapping
4. Draw the scene, supplying both texture and geometric coordinates

### Binding and Loading

```java
glEnable(GL_TEXTURE_2D);
glTexEnvf(GL_TEXTURE_ENV, GL_TEXTURE_ENV_MODE, GL_DECAL);
GLUint texName;
glGenTextures(1, &texName);
glBindTexture(GL_TEXTURE_2D, texName);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA, ImageWidth, ImageHeight, 0, GL_RGBA, GL_UNSIGNED_BYTE, ImageData);
```

### Setting Texture Params

```java
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
// texture magnification
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
// texture minification
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
```

## MIP Maps

* **mipmaps** (or pyramids) are pre-calculated sequences of images, each of which is a progressively lower resolution representation of the same image
* Textured objects can be viewed, like any other objects in a scene, at different distances from the viewpoint — in a dynamic scene, as a textured object moves further away from the viewpoint, the texture map must decrease in size along with the size of the projected image
* To avoid such artefacts, you can specify a series of pre-filtered texture maps of decreasing functions, called **mipmaps**
* To specify these textures, call `glTexImage2D()` once for each resolution of the texture map, with different values for the `level`, `width`, `height` and image paramters. Starting with zero, `level` identifies which texture in the series is specified
* `void glGenerateMipmap(GLenum target)`
* `void glGenerateTextureMipmap(GLuint texture)`