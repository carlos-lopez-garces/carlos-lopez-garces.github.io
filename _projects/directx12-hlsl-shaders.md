---
layout: projects
order: 3
title: 'CDX'
caption: A DirectX 12 renderer, featuring various real-time rendering techniques.
description: >
  A DirectX 12 renderer, featuring various real-time rendering techniques.
image: 
  path: /assets/img/projects/directx12-hlsl-shaders/hlsl-shadow-mapping.png
  srcset: 
    1920w: /assets/img/projects/directx12-hlsl-shaders/hlsl-shadow-mapping.png
    960w:  /assets/img/projects/directx12-hlsl-shaders/hlsl-shadow-mapping.png
    480w:  /assets/img/projects/directx12-hlsl-shaders/hlsl-shadow-mapping.png
links:
  - title: Github repo
    url: https://github.com/carlos-lopez-garces/d3d12
sitemap: false
show_collection: directx12-hlsl-shaders
no_groups: true
permalink: /projects/cdx/
---

**CDX** is my real-time toy renderer, which I use to learn real-time **rasterized** rendering techniques, as well as the DirectX 12 API. Unlike [**CDXR**](/projects/cdxr), which uses NVIDIA's Falcor framework, CDX issues DirectX 12 calls directly. I'm putting this renderer together out of foundational code found in the book [*Introduction to 3D Game Programming with DirectX 12*](https://www.d3dcoder.net/d3d12.htm){:target="_blank"} by Frank Luna.

Check out the implementation details by clicking on the cards below.

## Features

- glTF loader.
- **Shadow mapping.** 
- Stencil mirrors.