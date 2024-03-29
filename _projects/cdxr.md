---
layout: project
order: 2
title: 'CDXR'
caption: A hybrid rasterization-raytracing renderer based on DirectX 12, DirectX Raytracing, and NVIDIA's Falcor.
description: >
  A hybrid rasterization-raytracing renderer based on DirectX 12, DirectX Raytracing, and NVIDIA's Falcor.
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

**CDXR** is my real-time toy renderer. Based on the [Falcor](https://github.com/NVIDIAGameWorks/Falcor) framework and the DirectX Raytracing API and following Chris Wyman's SIGGRAPH courses and the Raytracing Gems series, CDXR implements a hybrid rasterization-raytracing configurable pipeline.

Check out the source code on ***[Github](https://github.com/carlos-lopez-garces/cdxr)***.

## Features

- One-bounce diffuse global illumination.
- **Deferred rendering via G-Buffer.** 
- Ray-traced ambient occlusion.
- **Denoising and antialiasing via temporal accumulation and camera jittering.**
- Thin lens camera and depth of field. 
- **Tone mapping.** 
- Lambertian diffuse reflection and microfacet reflection models.
- **Unidirectional path tracing.**
- Ashikhmin-Shirley BRDF.

## Select images

***01/13/2023 Unidirectional path tracing, Ashikhmin-Shirley BRDF. Assets by NVIDIA.***
{: style="text-align: center;"}
The Ashikhmin-Shirley BRDF models surfaces that have a diffuse substrate and a glossy coat. To sample it, its diffuse and specular terms are chosen with 0.5 probability each. When the diffuse term is chosen, the incident direction $$ \omega_i $$ is sampled using a cosine-weighted distribution; when the specular term is chosen instead, $$ \omega_i $$ is sampled using a microfacet distribution (I chose Trowbridge-Reitz because that's what PBRT uses for its substrate material).
{: style="text-align: justify;"}
![](/assets/img/projects/cdxr/15.png)
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/16.png)
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/17.png)
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/18.png)
{: style="text-align: center;"}

***01/09/2023 Unidirectional path tracing, combined Lambertian and specular BRDFs, and visibility testing. Assets by NVIDIA.***
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/9.png)
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/10.png)
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/11.png)
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/12.png)
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/13.png)
{: style="text-align: center;"}
![](/assets/img/projects/cdxr/14.png)
{: style="text-align: center;"}

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