---
layout: project
order: 1
title: 'CPBRT'
caption: My implementation of the PBRT physically-based renderer. (Mesh by Christian Schüller.)
description: >
  My implementation of the PBRT physically-based renderer.
date: '01-01-2020'
image: 
  path: /assets/img/projects/cpbrt.png
  srcset: 
    1920w: /assets/img/projects/cpbrt.png
    960w:  /assets/img/projects/cpbrt.png
    480w:  /assets/img/projects/cpbrt.png
links:
  - title: Github repo
    url: https://github.com/carlos-lopez-garces/cpbrt
sitemap: false
---

* TOC
{:toc}

**CPBRT** is my physically-based, offline toy renderer. It is the result of studying ***[Physically Based Rendering: From Theory to Implementation](https://pbr-book.org/)*** (Pharr, Jakob, and Humphreys, 2016), writing down the code fragments provided by the authors, and filling in the gaps. For the longest time, I wanted to have a playground to learn interesting rendering techniques and try out new ideas. Taking inspiration from great rendering engineers like Yining Karl Li and his [Takua Renderer](https://www.yiningkarlli.com/projects/takuarender.html), Edward Liu and his [EDXRay](http://behindthepixels.io/EDXRay/), and Peter Kutz and his [Photorealizer](https://peterkutz.com/), I started CPBRT in November 2020 and it took me nearly 9 months to produce a 1st render with it; it was a tough, but infinitely rewarding experience.

## Features

| **Light transport algorithms:** Kajiya path tracing (unidirectional, unbiased Monte Carlo estimation of the light transport equation). Direct-lighting (no indirect illumination) and path (full global illumination) integrators. | **Reflectance models and BRDFs:** Lambert diffuse model, Oren-Nayar diffuse model for rough surfaces, Fresnel perfectly specular model, and Fresnel glossy specular model (with Torrance-Sparrow microfacets with Beckmann-Spizzichino distribution). |
| **Textures:** Floating-point and spectrum constant-value textures. (I haven't paid much attention to texturing.) | **Materials:** Matte with either a perfect diffuse Lambertian BRDF or an Oren-Nayar BRDF for various degrees of roughness; plastic with diffuse and glossy specular BRDFs; and mirror with a perfectly-specular BRDF. |
| **Shapes:** Triangle meshes, single triangles, and spherical implicit surfaces. | **Accelerators:** BVH with 5 different primitive (or object) subdivision methods: linear BVH, hierarchical linear BVH, midpoint partitioning, equal counts partitioning, and surface area heuristic (SAH).|
| **Samplers:** Uniform or jittered stratified pixel sampling for 1D samples and Latin Hypercube sampling for 2D samples. Samplers rely on a Permuted Congruential Generator (PCG) pseudo-random number generator. | **Filters:** Box, triangle, Gaussian, Mitchell-Netravali, and Lanczos windowed-sinc filters. |
| **Lights:** Point and diffuse area light sources. An area light can take the form of any of the supported *shapes*. | **Cameras:** Thin lens perspective and orthographic projective cameras with configurable aperture and focal distance (for depth of field) and film aspect ratio. The perspective camera also has a configurable field of view. |
| **Participating media:** Homogeneous-density and grid-based variable-density media coming soon. | |

## Roadmap

| **January 2022:** Nothing. | **February 2022:** Volumetric light transport. |
| **March 2022:** Gold metal material. Checkerboard texture. Glass material. Utah Teapot (full) by [Benedikt Bitterli.](https://benedikt-bitterli.me/) | **April 2022:** Infinite area lights. |
| **May 2022:** ? | **June 2022:** ? | |
{:.noscroll-table}

## Select images

***Dragon triangle mesh by Christian Schüller, gold material, Torrance-Sparrow microfacets with Trowbridge-Reitz distribution.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/1.png)
{: style="text-align: center;"}

***Ganesha mesh by Wenzel Jakob; notice the absence of shadows (wrong).***
{: style="text-align: center;"}
 ![](/assets/img/projects/cpbrt/2.png)
{: style="text-align: center;"}

***Participating media. Not working yet.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/3.png)
{: style="text-align: center;"}

***Cornell box, direct lighting integrator, stratified sampling, 30 samples per pixel.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/15.png)
{: style="text-align: center;"}

***Cornell box, path integrator, stratified sampling, 10 samples per pixel.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/4.png)
{: style="text-align: center;"}

***Dragon triangle mesh by Christian Schüller, plastic material with Torrance-Sparrow microfacet BRDF to simulate glossy hard plastic.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/5.jpg)
{: style="text-align: center;"}

***Dragon triangle mesh by Christian Schüller, spherical diffuse area light, matte material with Lambertian reflection.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/6.png)
{: style="text-align: center;"}

***Dragon triangle mesh by Christian Schüller, spherical diffuse area light, plastic material w/o specular reflection.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/7.png)
{: style="text-align: center;"}

***Dragon triangle mesh by Christian Schüller, matte material with Oren-Nayar diffuse reflection.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/8.png)
{: style="text-align: center;"}

***Dragon triangle mesh by Christian Schüller, matte material with perfect Lambertian diffuse reflection.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/9.png)
{: style="text-align: center;"}

***Cover of the PBRT book by Yining Karl Li, perspective camera, 2 point lights, mirror material, specular BRDF.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/10.jpg)
{: style="text-align: center;"}

***Dragon triangle mesh by Christian Schüller, perspective camera, 2 point lights, matte material.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/11.png)
{: style="text-align: center;"}

***Cover of the PBRT book by Yining Karl Li, perspective camera, 2 point lights, matte material.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/12.png)
{: style="text-align: center;"}

***One triangle of a triangle mesh, matte material.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/13.png)
{: style="text-align: center;"}

***First render, implicitly defined sphere.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/14.png)
{: style="text-align: center;"}