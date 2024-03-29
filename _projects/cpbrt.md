---
layout: project
order: 1
title: 'CPBRT'
caption: My implementation of the PBRT physically-based renderer.
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

**CPBRT** is my physically-based, offline toy renderer. It is the result of studying ***[Physically Based Rendering: From Theory to Implementation](https://pbr-book.org/)*** (Pharr, Jakob, and Humphreys, 2016), writing down the code fragments provided by the authors, and filling in the gaps. I started CPBRT in November 2020 and it took me nearly 9 months to produce a 1st render with it; it was a tough but infinitely rewarding experience.

Check out the source code on ***[Github](https://github.com/carlos-lopez-garces/cpbrt)***.

## Features

| **Light transport algorithms:** Kajiya path tracing (unidirectional, unbiased Monte Carlo estimation of the light transport equation). Direct-lighting (no indirect illumination) and path (full global illumination) integrators. | **Reflectance models and BRDFs:** Lambert diffuse model; Oren-Nayar diffuse model for rough surfaces; Fresnel perfectly specular model and Fresnel glossy specular model (with Torrance-Sparrow microfacets with Beckmann-Spizzichino or Trowbridge-Reitz distributions); Disney diffuse, retro-reflective, and specular BSDF. |
| **Textures:** Floating-point and spectrum constant-value textures. Procedural checkerboard texture, antialiased with a box filter. Mipmapping. | **Materials:** Matte with either a perfect diffuse Lambertian BRDF or an Oren-Nayar BRDF for various degrees of roughness; plastic with diffuse and glossy specular BRDFs; mirror with a perfectly-specular BRDF; gold; glass with perfectly-specular BRDF and BTDF; diffuse substrate and glossy coat with an Ashikhmin-Shirley BRDF; Disney BSDF (diffuse, diffuse retro-reflection, and specular reflection only). |
| **Shapes:** Triangle meshes, single triangles, and spherical implicit surfaces. | **Accelerators:** BVH with 5 different primitive (or object) subdivision methods: linear BVH, hierarchical linear BVH, midpoint partitioning, equal counts partitioning, and surface area heuristic (SAH).|
| **Samplers:** Uniform or jittered stratified pixel sampling for 1D samples and Latin Hypercube sampling for 2D samples. Samplers rely on a Permuted Congruential Generator (PCG) pseudo-random number generator. | **Filters:** Box, triangle, Gaussian, Mitchell-Netravali, and Lanczos windowed-sinc filters. |
| **Lights:** Point, distant, and diffuse area light sources. An area light can take the form of any of the supported *shapes*. Infinite area light source backed by environment map. | **Cameras:** Thin lens perspective and orthographic projective cameras with configurable aperture and focal distance (for depth of field) and film aspect ratio. The perspective camera also has a configurable field of view. |
| **Participating media:** Homogeneous-density and grid-based variable-density media. | **Live viewer:** One thread runs a Vulkan rasterization pipeline that renders the film to a full-screen quad concurrently. |

## Project blog posts

**[Sampling and reconstruction](/blog/cpbrt/2022-01-10-sampling-and-reconstruction-in-cpbrt/)** (Jan 10, 2022, work in progress): Some of the theory behind the sampling of continuous functions and their reconstruction out of samples. 

**[Oren-Nayar reflectance model](/blog/cpbrt/2021-11-24-oren-nayar-reflectance-model/)** (Nov 24, 2021): A generalization of Lambert's reflectance model for diffuse reflection that takes into account the effects of the microgeometry of the surface on light reflection, as well as the position of the camera relative to it.

**[Subsurface scattering and the BSSRDF](/blog/cpbrt/2022-08-09-subsurface-scattering-and-the-bssrdf)** (Aug 09, 2022, work in progress): A description of the Bidirectional Scattering Distribution Function (BSSRDF) and its role in simulating subsurface light transport.

## Select images

***01/24/2024 First working version of live viewer.***
{: style="text-align: center;"}
The offline renderer divides the film into tiles. Each of the threads of a thread pool grabs one and renders to it, moving on to other tiles as they finish with them. A dedicated thread runs a Vulkan rasterization pipeline that displays the contents of the film simultaneously.

The operation is simple, but perhaps not optimal: a second film is shared between the tile rendering threads and the live viewer thread. The live viewer thread waits on a condition variable that tile rendering threads signal every time they finish processing a pixel sample and merging it with the second, shared film. Upon waking up, the live viewer thread obtains the (filtered) contents of this film as a uint8_t[], maps them to a staging VkBuffer, copies the data to it, creates a VkImage, and copies the staging buffer to the image; it also recreates the associated VkImageView and updates the descriptor set to make the film's contents available to the fragment shader in a texture; the texture is then mapped to a full-screen quad in the fragment shader.

There's surely a better scheme for sharing film updates with the live viewer thread. The constant recreation of the staging VkBuffer, VkImage, and VkImageView is probably not optimal either. These are things I want to investigate next.

The video shows a film of 512x512 resolution, divided into 64x64 pixel tiles. My CPU has 12 logical processors (6 cores), so up to 11 threads render tiles simultaneously using 1 spp and 1 runs the live viewer. 

{: style="text-align: center;"}
<video muted controls width="100%" preload="auto">
    <source src="/assets/img/projects/cpbrt/31.mp4" type="video/mp4">
</video>

***08/31/2023 Live viewer.***
{: style="text-align: center;"}
A Vulkan-based live viewer that renders the contents of the film, running concurrently on a thread. At the moment, only the final contents are rendered, but the idea is for it to display the contents of the film in real time as the render progresses. Since runs typically take a very long time, this should help me catch errors earlier and reduce debugging time.

![](/assets/img/projects/cpbrt/29.png)
{: style="text-align: center;"}

***08/19/2023 Specular reflection with a Disney BSDF.***
{: style="text-align: center;"}
Burley's *Physically Based Shading at Disney* says that the model for specular reflection in the Disney material is simply the (Torrance-Sparrow) microfacet BRDF with a ***generalized form*** of the Trowbridge-Reitz distribution (GTR) given by

$$
\begin{aligned}
D_{GTR}=\left(\frac{c}{\alpha^2\cos^2{\theta_h}+\sin^2{\theta_h}}\right)^\gamma
\end{aligned}
$$

where $$c$$ is a scaling constant and $$\alpha$$ is a roughness parameter. As for the Smith masking-shadowing $$G$$ function, Burley says that any of the functions derived by Walter in *Microfacet models for refraction through rough surfaces* works. For simplicity, though, and while I muster the courage to dive into that paper, I'm using $$G$$ as implemented already in PBRT.

{: style="text-align: justify;"}
![](/assets/img/projects/cpbrt/28.png)
{: style="text-align: center;"}

***07/21/2023 Diffuse reflection and retro-reflection with a Disney BSDF.***
{: style="text-align: center;"}
The Disney BSDF is a mix of a number of BRDFs, BTDFs, and BSSRDFs controlled by a relatively large set of parameters. The model is physically-based for the most part, but it incorporates a couple of additional reflectance models for artistic control.

The base diffuse BRDF adds a couple of Fresnel factors to the Lambertian BRDF to make the model physically-plausible. The resulting images are darker on object edges compared to measured data, though. To compensate, a diffuse retro-reflection BRDF parameterized by a roughness factor increases reflectance at grazing angles, brightening up the edges of objects. This can be appreciated in the following renders.

My Disney BSDF implementation doesn't include the specular reflection and transmission components, the BSSRDF, or the sheen component yet. It is based on Burley's notes *Physically Based Shading at Disney* and it doesn't include the enhancements and corrections in Burley's *Extending the Disney BRDF to a BSDF with Integrated Subsurface Scattering*.

{: style="text-align: justify;"}
![](/assets/img/projects/cpbrt/26.png)
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/27.png)
{: style="text-align: center;"}

***01/23/2023 Diffuse substrate and glossy coat with an Ashikhmin-Shirley BRDF.***
{: style="text-align: center;"}
The Ashikhmin-Shirley BRDF models surfaces that have a diffuse substrate and a glossy coat. To sample it, its diffuse and specular terms are chosen with 0.5 probability each. When the diffuse term is chosen, the incident direction $$ \omega_i $$ is sampled using a cosine-weighted distribution; when the specular term is chosen instead, $$ \omega_i $$ is sampled using a microfacet distribution (Trowbridge-Reitz in this case).

I'm still looking for a choice of reflectances and roughness values that really illustrates the properties of this BRDF.

The second render was produced using the volumetric path integrator; the prominent salt-and-pepper noise is something I haven't been able to fix.
{: style="text-align: justify;"}
![](/assets/img/projects/cpbrt/24.png)
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/25.png)
{: style="text-align: center;"}

***10mm fish-eye lens.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/21.png)
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/22.png)
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/23.png)
{: style="text-align: center;"}

***Dragon triangle mesh by Christian Schüller, subsurface scattering, skin material.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/19.png)
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/20.png)
{: style="text-align: center;"}

***Utah teapot triangle mesh by Benedikt Bitterli, glass material, lit with an infinite area light backed by a latitude-longitude radiance map.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/18.png)
{: style="text-align: center;"}

***Utah teapot triangle mesh by Benedikt Bitterli, glass material, volumetric path tracing; a bug prevents the environment map from being sampled.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/17.png)
{: style="text-align: center;"}

***Utah teapot triangle mesh by Benedikt Bitterli, metal material, lit with an infinite area light backed by a latitude-longitude radiance map.***
{: style="text-align: center;"}
![](/assets/img/projects/cpbrt/16.png)
{: style="text-align: center;"}

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

## Roadmap

| **January 2022:** Nothing. | **February 2022:** Volumetric light transport. |
| **March 2022:** Gold metal material. Checkerboard texture. | **April 2022:** Infinite area lights. Glass material. |
| **May 2022:** Infinite area lights. Radiance/environment maps. Glass material. Utah Teapot (full) by [Benedikt Bitterli.](https://benedikt-bitterli.me/) | **June 2022:** Bump mapping. | |
{:.noscroll-table}