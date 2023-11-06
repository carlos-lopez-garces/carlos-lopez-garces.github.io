---
layout: welcome
title: About me
selected_projects:
  - _projects/cpbrt.md
  - _projects/cdxr.md
  - _projects/directx12-hlsl-shaders.md
  - _projects/upenn.md
  - _projects/vulkan.md
projects_page: projects.md
posts_page: /blog/
---

Hi there! I'm a rendering engineer interested in physically-based rendering, real-time graphics techniques, and light transport theory. I'm currently a **graduate student** of Computer Graphics at [**UPenn**](https://www.upenn.edu/){:target="_blank"} ('23-'25). 

Previously, I earned a **BSc in Computer Science** from [Tecnológico de Monterrey](https://tec.mx/en){:target="_blank"} in 2010 and a **BSc in Mathematics** from [Indiana University](https://www.iu.edu/index.html){:target="_blank"} in 2023.

In the past, I worked in Silicon Valley for [**Oracle**](https://www.oracle.com/index.html){:target="_blank"} on the kernel of the Oracle database server, for [**Apcera**](https://en.wikipedia.org/wiki/Apcera){:target="_blank"} (now a subsidiary of Ericsson) on a container runtime and orchestrator for the cloud, and at [**HOVER**](https://hover.to){:target="_blank"} doing real-time rendering and geometry processing. 

## Select projects

<p class="read-more mt1">
  See <a class="heading flip-title" href="/projects/">Projects</a> for more
</p>

My personal projects include a physically-based offline renderer (CPBRT), a real-time DirectX12 toy renderer (CDXR), and various HLSL and GLSL shaders featuring well-known rendering techniques.

This year (2023), I've been learning Vulkan and modern rendering techniques through [*Mastering Graphics Programming with Vulkan*](https://www.packtpub.com/product/mastering-graphics-programming-with-vulkan/9781803244792){:target="_blank"}, a fantastic book altogether: GPU-driven rendering, meshlets, clustered deferred rendering, DDGI, and frame graphs (which blew my mind). I've written a little bit about this in my **[Vulkan Renderer Study](/_projects/vulkan.md)**. I have also worked on a few interactive graphics and animation projects of the ***[MSE in Computer Graphics](https://www.cis.upenn.edu/graduate/program-offerings/mse-in-computer-graphics-and-game-technology/){:target="_blank"}*** at UPenn.

**[CPBRT](/_projects/cpbrt.md)** (2020-today), the offline renderer, is the result of studying version 3 of the PBRT renderer of ***[Physically Based Rendering: From Theory to Implementation](https://www.pbrt.org/){:target="_blank"}***, writing down the code fragments in the book, and filling in the gaps. As soon as the fourth edition came out, I began backporting the GPU-accelerated *WavefrontPathIntegrator* of Chapter 15. Wavefront Rendering on GPUs; it's work in progress.

**[CDXR](/_projects/cdxr.md)** (2022), a real-time, hybrid rasterization-raytracing renderer on top of NVIDIA's [Falcor](https://github.com/NVIDIAGameWorks/Falcor){:target="_blank"}, is the result of following Chris Wyman's [SIGGRAPH courses](https://intro-to-dxr.cwyman.org/){:target="_blank"} and extending the renderer with various techniques found in the ***[Ray Tracing Gems series](https://www.realtimerendering.com/raytracinggems/){:target="_blank"}***, implemented using the DirectX Raytracing (DXR) API.

I have also implemented a few **[rasterization-based effects using the DirectX12 API](/_projects/directx12-hlsl-shaders.md)**: shadow mapping and stencil mirrors. And a few **[more basic effects in OpenGL](/_projects/opengl-glsl-shaders.md)**, such as image-based lighting with irradiance maps and environment mapping reflection and refraction via cubemaps.

<!--projects-->

## Latest blog posts

<!--posts_list-->