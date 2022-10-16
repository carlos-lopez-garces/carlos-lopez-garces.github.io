---
layout: project
order: 2
title: 'CDXR'
caption: A hybrid rasterization-raytracing renderer based on DirectX 12 and DirectX Raytracing.
description: >
  A hybrid rasterization-raytracing renderer based on DirectX 12 and DirectX Raytracing.
date: '01-01-2022'
image: 
  path: /assets/img/projects/cdxr.png
  srcset: 
    1920w: /assets/img/projects/cdxr.png
    960w:  /assets/img/projects/cdxr.png
    480w:  /assets/img/projects/cdxr.png
links:
  - title: Github repo
    url: https://github.com/carlos-lopez-garces/cdxr
sitemap: false
---

**CDXR** is my real-time toy renderer. Based on the [Falcor](https://github.com/NVIDIAGameWorks/Falcor) framework and the DirectX Raytracing API, CDXR implements a hybrid rasterization-raytracing configurable pipeline. For writing this renderer, I relied a lot on Chris Wyman's SIGGRAPH courses and the Ray Tracing Gems series.

Check out the source code on ***[Github](https://github.com/carlos-lopez-garces/cdxr)***.

## Features

- One-bounce diffuse global illumination.
- **Deferred rendering via G-Buffer.** 
- Ray-traced ambient occlusion.
- **Denoising and antialiasing via temporal accumulation and camera jittering.**
- Thin lens camera and depth of field. 
- **Tone mapping.** 
- Lambertian diffuse reflection and microfacet reflection models.

## Select images

***09/28/2022 Direct lighting on a microfacet reflection model. Assets by NVIDIA.***
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/8.png)
{: style="text-align: center;"}

***09/28/2022 Direct lighting on a microfacet reflection model. Assets by NVIDIA.***
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/6.png)
{: style="text-align: center;"}

***09/28/2022 Direct lighting on a pure Lambertian reflection model. Assets by NVIDIA.***
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/7.png)
{: style="text-align: center;"}

***07/06/2022 1-bounce GI and hard shadows. Assets by NVIDIA.***
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/5.png)
{: style="text-align: center;"}

***05/13/2022 Occlusion testing with shadow rays, with and without depth of field. Assets by NVIDIA.***
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/3.png)
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/4.png)
{: style="text-align: center;"}

***05/13/2022 Lambertian diffuse reflection, with and without depth of field. Assets by NVIDIA.***
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/1.png)
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/2.png)
{: style="text-align: center;"}