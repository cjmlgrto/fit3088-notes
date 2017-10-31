[← Return to Index](https://github.com/cjmlgrto/fit3088-notes/)

# Shading

### Flat Shading (Constant)

* Three vectors `l`, `n` and `v` can vary from point to point on a surface
* For a **flat polygon**, `n` is constant
* If we assume a distant viewer, `v` is constant (same for `l` if the light source is distant)
* If the shading vectors are constant, then the shading calculation needs to be carried out only once for each polygon, and each polygon is assigned the shame shade

### Gouraud Shading (Interpolation)

* Suppose that the lighting calculation is made at each vertex using the material properties and the vectors `n`, `v` and `l` computed for each vertex
* Each vertex will have its own colour that the rasterizer can use to interpolate a shade for each fragment
* In Gouraud shading, the normal is the normalised average of the polygons of the polygons that share the vertex `n`

### Phong Shading (Interpolation)

* Interpolate normals across each polygon
* Compute vertex normals by interpolating over the normals of the polygon that share the vertex
* Next, use interpolation to interpolate the light vectors over the region
* `n_c(a) = (1-a)n_a + αn_b`
* `n(α,β) = (1-β)n_c + βn_d`
* Once we have the light vectors at each point, we can make an independent shading calculation
* Requires that the lighting model applied to each fragment
* Requires more computational resources