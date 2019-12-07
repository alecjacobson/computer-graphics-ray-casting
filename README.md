# Computer Graphics – Ray Casting

> **To get started:** Clone this repository and its submodule using 
> 
>     git clone --recursive http://github.com/alecjacobson/computer-graphics-ray-casting.git
>
> **Do not fork:** Clicking "Fork" will create a _public_ repository. If you'd like to use GitHub while you work on your assignment, then mirror this repo as a new _private_ repository: https://stackoverflow.com/questions/10065526/github-how-to-make-a-fork-of-public-repository-private

## Background

### Read Sections 4.1-4.4 of _Fundamentals of Computer Graphics (4th Edition)_.

_We will cover basic shading, shadows and reflection in the next assignment._

### Scene Objects

This assignment will introduce a few _primitives_ for 3D geometry:
[spheres](https://en.wikipedia.org/wiki/Sphere),
[planes](https://en.wikipedia.org/wiki/Plane_(geometry)) and triangles. We'll
get a first glimpse that more complex shapes can be created as a collection of
these primitives.

The core interaction that we need to start visualizing these shapes is
ray-object intersection. A ray emanating from a point <img src="/tex/685ef93932f99f701fad51a2deb266ca.svg?invert_in_darkmode&sanitize=true" align=middle width=47.18021714999998pt height=26.76175259999998pt/>
(e.g., a camera's "eye") in a direction <img src="/tex/ab117c03bd3f18f26b12262a764e44e1.svg?invert_in_darkmode&sanitize=true" align=middle width=49.018091099999985pt height=26.76175259999998pt/> can be
_parameterized_ by a single number <img src="/tex/72b79076ffc7e3f7cf113ae36a0f2d68.svg?invert_in_darkmode&sanitize=true" align=middle width=68.94967199999999pt height=24.65753399999998pt/>. Changing the value of <img src="/tex/4f4f4e395762a3af4575de74c019ebb5.svg?invert_in_darkmode&sanitize=true" align=middle width=5.936097749999991pt height=20.221802699999984pt/> picks
a different point along the ray. Remember, a ray is a 1D object so we only need
this one "knob" or parameter to move along it. The [parametric
function](https://en.wikipedia.org/wiki/Parametric_equation) for a ray written
in [vector notation](https://en.wikipedia.org/wiki/Vector_notation) is:

<p align="center"><img src="/tex/bff2303ea1893c9e46e750055ad849f4.svg?invert_in_darkmode&sanitize=true" align=middle width=98.18462279999999pt height=16.438356pt/></p>

For each object in our scene we need to find out:

  1. is there some value <img src="/tex/4f4f4e395762a3af4575de74c019ebb5.svg?invert_in_darkmode&sanitize=true" align=middle width=5.936097749999991pt height=20.221802699999984pt/> such that the ray <img src="/tex/1eefdf0e160501a4fd1c7d665cf5a46b.svg?invert_in_darkmode&sanitize=true" align=middle width=26.506900199999986pt height=24.65753399999998pt/> lies on the
  surface of the object?
  2. if so, what is that value of <img src="/tex/4f4f4e395762a3af4575de74c019ebb5.svg?invert_in_darkmode&sanitize=true" align=middle width=5.936097749999991pt height=20.221802699999984pt/> (and thus what is the position of
     intersection $\mathbf{r}(t)\in \mathbb{R}^3 $
  3. and what is the surface's [unit](https://en.wikipedia.org/wiki/Unit_vector)
     [normal vector](https://en.wikipedia.org/wiki/Normal_(geometry)) at the
     point of intersection.

For each object, we should carefully consider how _many_ ray-object
intersections are possible for a given ray (always one? sometimes two? ever
zero?) and in the presence of multiple answers choose the closest one.

> **Question:** Why keep the closest hit?
>
> **Hint:** 🤦🏻

In this assignment, we'll use simple representations for primitives. For
example, for a plane we'll store a point on the plane and the normal anywhere on
the plane.

> **Question:** How many numbers are needed to uniquely determine a plane?
>
> **Hint:** A point position (3) + normal vector (3) is too many. Consider how
> many numbers are needed to specify a line in 2D.

### Camera

In this assignment we will pretend that our "camera" or "eye" looking into the
scene is shrunk to a single 3D point <img src="/tex/685ef93932f99f701fad51a2deb266ca.svg?invert_in_darkmode&sanitize=true" align=middle width=47.18021714999998pt height=26.76175259999998pt/> in space. The
image rectangle (e.g., 640 pixels by 360 pixels) is placed so the image center
is directly _in front_ of the
"eye" point at a certain ["focal
length"](https://en.wikipedia.org/wiki/Focal_length) <img src="/tex/2103f85b8b1477f430fc407cad462224.svg?invert_in_darkmode&sanitize=true" align=middle width=8.55596444999999pt height=22.831056599999986pt/>. The image of pixels is
scaled to match the given `width` and `height` defined by the `camera`. Camera
is equipped with a direction that moves left-right across the image
<img src="/tex/129c5b884ff47d80be4d6261a476e9f1.svg?invert_in_darkmode&sanitize=true" align=middle width=10.502226899999991pt height=14.611878600000017pt/>, up-down <img src="/tex/f6fc3ac36dff143d4aac9d145fadc77e.svg?invert_in_darkmode&sanitize=true" align=middle width=10.239687149999991pt height=14.611878600000017pt/>, and from the "eye" to the image
<img src="/tex/0ec5127b899177eb46b5463853208557.svg?invert_in_darkmode&sanitize=true" align=middle width=26.700900599999994pt height=19.1781018pt/>. Keep in mind that the `width` and `height` are measure in the
units of the _scene_, not in the number of pixels. For example, we can fit a
1024x1024 image into a camera with width <img src="/tex/ef69061d50ca83722ebdd84b564309be.svg?invert_in_darkmode&sanitize=true" align=middle width=25.570741349999988pt height=21.18721440000001pt/> and height <img src="/tex/ef69061d50ca83722ebdd84b564309be.svg?invert_in_darkmode&sanitize=true" align=middle width=25.570741349999988pt height=21.18721440000001pt/>.

**Note:** The textbook puts the pixel coordinate origin in the bottom-left, and
uses <img src="/tex/77a3b857d53fb44e33b53e4c8b68351a.svg?invert_in_darkmode&sanitize=true" align=middle width=5.663225699999989pt height=21.68300969999999pt/> as a column index and <img src="/tex/36b5afebdba34564d884d347484ac0c7.svg?invert_in_darkmode&sanitize=true" align=middle width=7.710416999999989pt height=21.68300969999999pt/> as a row index. In this assignment; the
origin is in the _top-left_, <img src="/tex/77a3b857d53fb44e33b53e4c8b68351a.svg?invert_in_darkmode&sanitize=true" align=middle width=5.663225699999989pt height=21.68300969999999pt/> is a _row_ index, and <img src="/tex/36b5afebdba34564d884d347484ac0c7.svg?invert_in_darkmode&sanitize=true" align=middle width=7.710416999999989pt height=21.68300969999999pt/> is a _column_ index.

> **Question:** Given that <img src="/tex/129c5b884ff47d80be4d6261a476e9f1.svg?invert_in_darkmode&sanitize=true" align=middle width=10.502226899999991pt height=14.611878600000017pt/> points right and <img src="/tex/f6fc3ac36dff143d4aac9d145fadc77e.svg?invert_in_darkmode&sanitize=true" align=middle width=10.239687149999991pt height=14.611878600000017pt/> points up,
> why does _minus_ <img src="/tex/5ddc1b22140b2658931d8962d8c90c33.svg?invert_in_darkmode&sanitize=true" align=middle width=13.91546639999999pt height=14.611878600000017pt/> point into the scene?
>
> **Hint:** ☝️

![Our [pinhole](https://en.wikipedia.org/wiki/Pinhole_camera)
[perspective](https://en.wikipedia.org/wiki/3D_projection#Perspective_projection)
camera with notation (inspired by [Marschner & Shirley
2015])](images/ray-casting-camera.png)

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

### False color images

Our scene does not yet have light so the only accurate rendering would be a
pitch black image. Since this is rather boring, we'll create false or pseudo
renderings of the information we computed during ray-casting.

#### Object ID image

The simplest image we'll make is just assigning each object to a color. If a
pixel's closest hit comes from the <img src="/tex/77a3b857d53fb44e33b53e4c8b68351a.svg?invert_in_darkmode&sanitize=true" align=middle width=5.663225699999989pt height=21.68300969999999pt/>-th object then we paint it with the <img src="/tex/77a3b857d53fb44e33b53e4c8b68351a.svg?invert_in_darkmode&sanitize=true" align=middle width=5.663225699999989pt height=21.68300969999999pt/>-th
rgb color in our `color_map`.

![This "object id image" shows which object is _closest_ along the ray passing
through each pixel. Each object is assigned to its own unique color.](images/sphere-packing-id.png)

#### Depth images

The object ID image gives us very little sense of 3D. The simplest image to
encode the 3D geometry of a scene is a [depth
image](https://en.wikipedia.org/wiki/Depth_map). Since the range of depth is
generally <img src="/tex/bce1ae9da7f9c7dd24d9ede388f14dfe.svg?invert_in_darkmode&sanitize=true" align=middle width=43.25919014999999pt height=24.65753399999998pt/> where <img src="/tex/2103f85b8b1477f430fc407cad462224.svg?invert_in_darkmode&sanitize=true" align=middle width=8.55596444999999pt height=22.831056599999986pt/> is the distance from the camera's eye to the camera
plane, we must map this to the range <img src="/tex/acf5ce819219b95070be2dbeb8a671e9.svg?invert_in_darkmode&sanitize=true" align=middle width=32.87674994999999pt height=24.65753399999998pt/> to create a [grayscale
image](https://en.wikipedia.org/wiki/Grayscale). In this assignment we use a
simple non-linear mapping based on reasonable default values.

![This grayscale "depth image" shows the distance to the nearest object along
the ray through each pixel. Shorter distances are brighter than farther
distances.](images/sphere-packing-depth.png)

#### Normal images

The depth image technically captures all geometric information visible by
casting rays from the camera, but interesting surfaces will appear dull because
small details will have nearly the same depth.  During ray-object intersection
we compute or return the surface normal vector <img src="/tex/52e148ad5c7c0ee019e5ff76348ed65e.svg?invert_in_darkmode&sanitize=true" align=middle width=51.32391824999999pt height=26.76175259999998pt/> at the
point of intersection. Since the normal vector is [unit
length](https://en.wikipedia.org/wiki/Unit_vector), each coordinate value is
between <img src="/tex/699628c77c65481a123e3649944c0d51.svg?invert_in_darkmode&sanitize=true" align=middle width=45.66218414999998pt height=24.65753399999998pt/>. We can map the normal vector to an rgb value in a linear way
(e.g., <img src="/tex/39c6d214492caeb8a4c5dd605f494d6c.svg?invert_in_darkmode&sanitize=true" align=middle width=78.29964614999999pt height=27.77565449999998pt/>).

Although all of these images appear cartoonish and garish, together they reveal
that ray-casting can probe important pixel-wise information in the 3D scene.

![This colorized "normal image" shows surface normal at the nearest point in the
scene along the ray through each pixel.](images/sphere-packing-normal.png)

## Tasks

In this assignment you will implement core routines for casting rays into a 3D
and collect "hit" information where they intersect 3D objects.

## Whitelist

This assignment uses the [Eigen](https://eigen.tuxfamily.org) for numerical
linear algebra. This library is used in both professional and academic numerical
computing. We will use its `Eigen::Vector3d` as a double-precision 3D vector
class to store <img src="/tex/244be3c7db382d3e1400c7c4caa1023a.svg?invert_in_darkmode&sanitize=true" align=middle width=41.02358204999999pt height=14.15524440000002pt/> data for 3D points and 3D vectors. You can add (`+`)
vectors and points together, multiply them against scalars (`*`) and compute
vector [dot products](https://en.wikipedia.org/wiki/Dot_product) (`a.dot(b)`).
In addition, `#include <Eigen/Geometry>` has useful geometric functions such as
3D vector [cross product](https://en.wikipedia.org/wiki/Cross_product)
(`a.cross(b)`).

### `src/write_ppm.cpp`

See
[computer-graphics-raster-images](https://github.com/alecjacobson/computer-graphics-raster-images#srcwrite_ppmcpp).

### `src/viewing_ray.cpp`
Construct a viewing ray given a camera and subscripts to a pixel.

### `src/first_hit.cpp`
Find the first (visible) hit given a ray and a collection of scene objects

### `Sphere::intersect_ray` in `src/Sphere.cpp`
Intersect a sphere with a ray.

### `Plane::intersect_ray` in `src/Plane.cpp`
Intersect a plane with a ray.

![Running `./raycasting` should produce `id.ppm` that looks like this.](images/sphere-and-plane-id.png)

![Running `./raycasting` should produce `depth.ppm` that looks like this.](images/sphere-and-plane-depth.png)

![Running `./raycasting` should produce `normal.ppm` that looks like this.](images/sphere-and-plane-normal.png)

### `Triangle::intersect_ray` in `src/Triangle.cpp`
Intersect a triangle with a ray.

![Running `./raycasting ../data/triangle.json` should produce `id.ppm` that looks like this.](images/triangle-id.png)

### `TriangleSoup::intersect_ray` in `src/TriangleSoup.cpp`
Intersect a triangle soup with a ray.

![Running `./raycasting ../data/bunny.json` should produce images that like this. _**Note:** This example may take a while to compute; try using a release build if you're confident your code works._](images/bunny.gif)

> **Pro Tip:** Mac OS X users can quickly preview the output images using
>
> ```
> ./raycasting && qlmanage -p {id,depth,normal}.ppm
> ```
>
> Flicking the left and right arrows will toggle through the results

> **Pro Tip:** After you're confident that your program is working _correctly_,
> you can dramatic improve the performance simply by enabling [compiler
> optimization](https://en.wikipedia.org/wiki/Optimizing_compiler): 
>
> ```
> mkdir build-release
> cd build-release
> cmake ../ -DCMAKE_BUILD_TYPE=Release
> make
> ```
