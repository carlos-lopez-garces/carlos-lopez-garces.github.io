---
layout: project
order: 1
date: '15-02-2022'
title: 'Shadow mapping'
image: 
  path: /assets/img/projects/directx12-hlsl-shaders/hlsl-shadow-mapping.png
  srcset: 
    1920w: /assets/img/projects/directx12-hlsl-shaders/hlsl-shadow-mapping.png
    960w:  /assets/img/projects/directx12-hlsl-shaders/hlsl-shadow-mapping.png
    480w:  /assets/img/projects/directx12-hlsl-shaders/hlsl-shadow-mapping.png
sitemap: false
---

The basic shadow mapping algorithm renders the scene depth to a texture (i.e. the shadow map) from the viewpoint of the light source. The resulting rendered fragments cannot be in shadow because they have a nonoccluded line of sight with the light source. The shadow map will thus contain the depth values of all the visible fragments from the perspective of the light.

The shadow mapping algorithm does 2 render passes: 1) render the scene depth from the viewpoint of the light into the shadow map; 2) render the scene as usual to the back buffer from the viewpoint of the camera, using the shadow map in the shader.

## Rendering the shadow map

The shadow map texture is really a depth/stencil buffer, so we take advantage of the built-in depth buffer creation of the GPU. Since we let the hardware do it, we don't need CPU access to it, so a default heap (D3D12_HEAP_TYPE_DEFAULT) instead of an upload heap is where we create the shadow map's resource.

The dimensions of the texture determine the resolution of the shadow map (the larger it is, the higher quality it'll be). I used a resolution of 2048x2048.

Rendering the scene's depth from the perspective of the camera is not that different from doing it from the camera: we need a view matrix that transforms points from world space to light space and a projection matrix that defines the volume of emitted light: a frustum volume defined by a perspective projection can model spotlight volumes; a box volume defined by an orthographic projection can model bounded parallel light volumes; and a volume large enough to cover the entire scene can model the sun's volume.

In my first scene, I used a parallel light, so I modeled its volume using an orthographic projection. With orthographic projections, there's no foreshortening and parallel lines remain parallel; the viewing volume is a box that is axis-aligned with the view space basis vectors, with  width $$w$$ and height $$h$$ that depend on the chosen resolution of the shadow map, a near plane $$n$$, and a far plane $$f$$ that looks down the positive $$z$$-axis of view space.

The view space orthographic view volume (the axis-aligned box) is subject to the following mapping:

$$
[-\frac{w}{2},\frac{w}{2}]\times[-\frac{h}{2},\frac{h}{2}]\times[n,f]\rightarrow[-1,1]\times[-1,1]\times[0,1]
$$

after which the volume is in NDC space. This orthographic transformation from view space to NDC space preserves the relative depth of points; that's why there's no foreshortening. We derive the mapping for $$x$$ and $$y$$ of a point in view space by mapping the endpoints of those intervals:

$$
\begin{aligned}
\frac{2}{w}\cdot[-\frac{w}{2},\frac{w}{2}] &= [-1,1] \Longrightarrow x \rightarrow x\cdot\frac{2}{w} \\
\frac{2}{h}\cdot[-\frac{h}{2},\frac{h}{2}] &= [-1,1] \Longrightarrow x \rightarrow y\cdot\frac{2}{h}
\end{aligned}
$$

The mapping for $$z$$ corresponds to a scaling (from a range of size $$f-n$$ to one of size $$1$$) and a translation (one such that $$n$$ is taken to $$0$$). So the $$z$$ coordinate is subject to a mapping of the form $$g(z)=az+b$$ and we know that $$g(n)=0$$ and $$g(f)=1$$. We then solve the following system of 2 equations in 2 variables:

...

## Using the shadow map

...