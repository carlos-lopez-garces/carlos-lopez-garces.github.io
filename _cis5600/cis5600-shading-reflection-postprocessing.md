---
layout: project
order: 4
date: '06-10-2023'
title: 'Shading models, reflections models, and post-processing effects'
caption: OpenGL shaders of basic shading and reflection models, and post-processing effects.
image: 
  path: /assets/img/projects/upenn/cis5600/shaders.png
  srcset: 
    1920w: /assets/img/projects/upenn/cis5600/shaders.png
    960w:  /assets/img/projects/upenn/cis5600/shaders.png
    480w:  /assets/img/projects/upenn/cis5600/shaders.png
sitemap: false
no_groups: true
---

In this project, I implemented a variety of shading and reflection models, and post-processing effects using OpenGL.

***Matcaps***, or *Material Captures*, are texture images of a pre-rendered sphere with a material. To sample them, map the unit sphere centered at the origin, as defined by the x,y coordinates of the vertex normal, to the [0,1] unit square of UV space in the vertex shader.

***Procedural cosine-curve color palettes*** were popularized by Iñigo Quilez in the article [Palettes](https://iquilezles.org/articles/palettes/){:target="_blank"} of his blog. Given 4 RGB colors $$a$$, $$b$$, $$c$$, and $$d$$, one can get a color palette by evaluating $$ f(t) = a+b\cos(2\pi(ct+d)) $$ with $$ t \in [0,1]$$ in the fragment shader.

***Gaussian blur***, ***Sobel filter edge detection***, ***greyscale*** and ***vignetting***, and ***fake bloom*** are all post-processing effects applied to a render target where the model has previously been rendered using some shading or reflection model. The ***Sobel filter*** is of particular interest because it is a key component of a [**Variable Rate Shading**](/blog/vulkan/2023-10-13-vulkan-variable-rate-shading) solution.

| Clay matcap | Chrome matcap | Pearl matcap |
| -------- | ------- | ------- |
| ![](/assets/img/projects/upenn/cis5600/matcap-clay.png) | ![](/assets/img/projects/upenn/cis5600/matcap-chrome.png) | ![](/assets/img/projects/upenn/cis5600/matcap-pearl.png) |

| Plastic matcap | Cosine curve color palette | Silhouette |
| -------- | ------- | ------- |
| ![](/assets/img/projects/upenn/cis5600/matcap-plastic.png) | ![](/assets/img/projects/upenn/cis5600/procedural-color-palette.png) | ![](/assets/img/projects/upenn/cis5600/silhouette.png) |

| Normals | Blinn-Phong | Gaussian blur |
| -------- | ------- | ------- |
| ![](/assets/img/projects/upenn/cis5600/normals.png) | ![](/assets/img/projects/upenn/cis5600/blinn-phong.png)     | ![](/assets/img/projects/upenn/cis5600/gaussian-blur.png) |

| Sobel filter edge detection | Greyscale and vignette | Fake bloom |
| -------- | ------- | ------- |
| ![](/assets/img/projects/upenn/cis5600/sobel-filter.png) | ![](/assets/img/projects/upenn/cis5600/greyscale-vignette.png)     | ![](/assets/img/projects/upenn/cis5600/fake-bloom.png) |