---
layout: project
order: 1
date: '29-09-2023'
title: 'Software rasterizer'
caption: A software triangle mesh rasterizer written from scratch.
image: 
  path: /assets/img/projects/upenn/cis5600/rasterizer.bmp
  srcset: 
    1920w: /assets/img/projects/upenn/cis5600/rasterizer.bmp
    960w:  /assets/img/projects/upenn/cis5600/rasterizer.bmp
    480w:  /assets/img/projects/upenn/cis5600/rasterizer.bmp
sitemap: false
no_groups: true
---

In this project, I wrote a software rasterizer for polygonal meshes. Essentially, this involved:

- Loading a .obj polygonal mesh.
- Triangulate each polygonal face.
- For each triangle:
    - Transform its vertices to camera space, project them to screen space, divide them by their z-coordinate to perform perspective diminishing, and then transform them from NDC coordinates to pixel coordinates.
    - Cull it if it lies completely behind the near plane or beyond the far plane of the camera.
    - Compute its axis-aligned bounding box in pixel space, clamped to the extents of the image.
    - For each pixel row contained by the bounding box:
        - Determine the interval of pixels in the row that the triangle overlaps.
        - For each pixel in this interval (a triangle fragment):
            - Compute its barycentric coordinates in relation to the triangle's vertices in pixel space.
            - Use these barycentric coordinates to compute the fragment's z-coordinate in a ***perspective-correct way***.
            - Perform the depth test and update the z-buffer.
            - Use the barycentric coordinates to interpolate vertex attributes in a ***perspective-correct way*** in pixel space. Vertex attributes include normal and texture UV coordinate.
            - Perform back-face culling of the triangle based on the orientation of the interpolated normal with repect to the camera.
            - Evaluate a reflection model (Lambertian diffuse reflection) to obtain the fragment's color. The camera acts as a flashlight; Lambert's cosine law can be evaluated with the interpolated normal in pixel space and the light vector in camera space.

Because of the distortion that results from a perspective projection, we can't simply use the barycentric coordinates of the fragment in pixel space to interpolate vertex normals and UVs. The method to perform perspective-correct interpolation that I implemented here is explained in [*Rasterization: a Practical Implementation*](https://www.scratchapixel.com/lessons/3d-basic-rendering/rasterization-practical-implementation/perspective-correct-interpolation-vertex-attributes.html){:target="_blank"}.

{: style="text-align: center;"}
![](/assets/img/projects/upenn/cis5600/rasterizer.gif)

An additional feature that I decided to implement was ***normal mapping***. This involved computing a tangent-bitangent-normal frame of reference for tangent space with respect to world space out of world-space vertex positions and UVs. The inverse of the corresponding homogeneous matrix can then be used to transform the world-space light vector to tangent space to compute Lambert's cosine law term with a tangent-space normal read from a normal map. 

| Without normal mapping    | With normal mapping |
| -------- | ------- |
| ![Without normal mapping](/assets/img/projects/upenn/cis5600/no_normal_mapping_stone.bmp) | ![With normal mapping](/assets/img/projects/upenn/cis5600/normal_mapping_stone.bmp)    |
| ![Without normal mapping](/assets/img/projects/upenn/cis5600/no_normal_mapping_wall.bmp) | ![With normal mapping](/assets/img/projects/upenn/cis5600/normal_mapping_wall.bmp)    |
| ![Without normal mapping](/assets/img/projects/upenn/cis5600/no_normal_mapping_rock.bmp) | ![With normal mapping](/assets/img/projects/upenn/cis5600/normal_mapping_rock.bmp)     |