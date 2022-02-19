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

The following are the key points of the implementation:

- The shadow map texture is really a depth/stencil buffer. We take advantage of the built-in depth buffer creation of the GPU. Since we let the hardware do it, we don't need CPU access to it, so a default heap (D3D12_HEAP_TYPE_DEFAULT) instead of an upload heap is where we create the shadow map's resource.

- The dimensions of the texture determine the resolution of the shadow map (the larger it is, the higher quality it'll be). I used 2048x2048.

- We need a light view matrix that transforms points from world space to light space. We also need a light projection matrix that defines the volume of emitted light: a frustum volume defined by a perspective projection can model spotlight volumes; a box volume defined by an orthographic projection can model bounded parallel light volumes; and a volume large enough to cover the entire scene can model the sun's volume.

- ...