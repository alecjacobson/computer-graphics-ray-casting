# Computer Graphics – Ray Casting

> **To get started:** Fork this repository then issue
> 
>     git clone --recursive http://github.com/[username]/computer-graphics-ray-casting.git
>

## Background

### Read Sections 4.1-4.4 of _Fundamentals of Comptuer Graphics (4th Edition)_.

_We will cover basic shading and shadows in the next assignment._

### Scene Objects

This assignment will introduce basic _primitives_ for 3D geometry: [spheres](https://en.wikipedia.org/wiki/Sphere),
[planes](https://en.wikipedia.org/wiki/Plane_(geometry)) and triangles. We'll
get a first glimpse that more complex shapes can be created as a collection of
these primitives.

The basic interaction that we need to compute with these shapes is ray-object
intersection. A ray emanating from a point $\mathbf{e} ∈ \mathbb{R}³$ (e.g., a
camera's "eye") in a direction $\mathbf{d} ∈ \mathbb{R}³$ can be _parameterized_
by a single number $t ∈ [0,∞)$. Changing the value of $t$ picks a different
point along the ray. Remember, a ray is a 1D object so we only need this one
"knob" or parameter to move along it. The [parametric
function](https://en.wikipedia.org/wiki/Parametric_equation) for a ray written
in [vector notation](https://en.wikipedia.org/wiki/Vector_notation) is:

$$
\mathbf{r}(t) = \mathbf{e} + t\mathbf{d}.
$$

For each object in our scene we need to find out:

  1. is there some value $t$ such that the ray $\mathbf{r}(t)$ lies on the
  surface of the object?
  2. if so, what is that value of $t$ (and thus what is the position of
     intersection $\mathbf{r}(t)∈\mathbb{R}³$
  3. and what is the surface's [unit](https://en.wikipedia.org/wiki/Unit_vector)
     [normal vector](https://en.wikipedia.org/wiki/Normal_(geometry)) at the
     point of intersection.

For each object, we should carefully consider how _many_ ray-object
intersections are possible for a given ray (always one? sometimes two? ever
zero?) and in the presence of multiple answers choose the closest one.

> **Question:** Why keep the closest hit?
> **Hint:** 🤦🏻

In this assignment, we'll use simple representations for primitives. For
example, for a plane we'll store a point on the plane and the normal anywhere on
the plane.

> **Question:** How many numbers are needed to uniquely determine a plane?
>
> **Hint:** A point position (3) + normal vector (3) is too many. Consider how
> many numbers are needed to specify a line in 2D.


> **Hint:** Mac OS X users can quickly preview the output images using
> ```
> ./raytracing ../shared/data/sphere-and-plane.json && qlmanage -p {id,depth,normal}.ppm
> ```
> Flicking the left and right arrows will toggle through the results

### Triangle Soup

Triangles are the simplest 2D polygon. On the computer we can represent a
triangle efficiently by storing its 3 corner positions. To store a triangle
floating in 3D, each corner position is stored as 3D position.

A simple, yet effective and popular way to approximate a complex shape is to
store list of (many and small) triangles covering the shape's surface. If we
place no assumptions on these triangles (i.e., they don't have to be connected
together or non-intersecting), then we call this collection a "[triangle
soup](https://en.wikipedia.org/wiki/Polygon_soup)". 

When considering the intersection of a ray and a triangle soup, we simply need
to find the _first_ triangle in the soup that the ray intersects first.

### False color image

Our scene does not yet have light so the only accurate rendering would be a
pitch black image. Since this is rather boring, we'll create false or pseudo
renderings of the information we computed during ray-casting.

#### Object ID image

The simplest image we'll make is just assigning each object to a color. If a
pixel's closest hit comes from the $i$th object then we paint it with the $i$th
rgb color in our `color_map`.

#### Depth images

The object ID image gives us very little sense of 3D. The simplest image to
encode the 3D geometry of a scene is a [depth
image](https://en.wikipedia.org/wiki/Depth_map). Since the range of depth is
generally $[d,∞)$ where $d$ is the distance from the camera's eye to the camera
plane, we must map this to the range $[0,1]$ to create a [grayscale
image](https://en.wikipedia.org/wiki/Grayscale). In this assignment we use a
simple non-linear mapping based on reasonable default values.

#### Normal images

The depth image technically captures all geometric information visible by
casting rays from the camera, but interesting surfaces will appear dull because
small details will have nearly the same depth.  During ray-object intersection
we compute or return the surface normal vector $\mathbf{n} ∈ \mathbf{R}³$ at the
point of intersection. Since the normal vector is [unit
length](https://en.wikipedia.org/wiki/Unit_vector), each coordinate value is
between $[-1,1]$. We can map the normal vector to an rgb value in a linear way
(e.g., $r = ½ x + ½$).

Although all of these images appear cartoonish and garish, together they reveal
that ray-casting can probe important pixel-wise information in the 3D scene.

## Tasks

## Whitelist

`#include <Eigen/Geometry>` has useful geometric functions such as `.dot` and
`.cross` for [dot product]() and [cross product]() respectively.

### `Sphere::intersect_ray` in `src/Sphere.cpp`

### `Plane::intersect_ray` in `src/Plane.cpp`

### `Triangle::intersect_ray` in `src/Triangle.cpp`

### `TriangleSoup::intersect_ray` in `src/TriangleSoup.cpp`

### `src/viewing_ray.cpp`

### `src/first_hit.cpp`

# Computer Graphics – Ray Tracing

Ray-Tracing

- Lambertian
- Blinn-Phong
- Ambient
- Shadows
- ideal specular reflection (mirror reflection)
