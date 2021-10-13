---
layout: post
title:  "Deferred and forward rendering in Unreal Engine 4"
---
You can set up Unreal to do either ***deferred*** or ***forward*** rendering.

Deferred rendering is recommended when your device is powerful. Pretty much every blockbuster game uses deferred rendering. Forward rendering, for devices with limitations. Mobile games and VR applications tend to use forward rendering, for instance.

One thing that forward does better than deferred is ***antialiasing***. Deferred can only do ***temporal antialiasing***. If you see ***ghosting*** while playing your game, that's an artifact of temporal antialiasing. Forward does antialiasing better.

The use of the ***GBuffer*** in deferred rendering is a form of ***real-time image compositing***: the frame is the end result of a combination of different temporary frames (or render targets), each of which stores a particular kind of information per pixel: depth, normal, metalness, roughness, specularity, diffuse, etc. The GBuffer is this collection of buffers.

![](/assets/2020-04-11-deferred-and-forward-rendering-in-ue4/1.png)
{: style="text-align: center;"}

***Normals***
{: style="text-align: center;"}

![](/assets/2020-04-11-deferred-and-forward-rendering-in-ue4/2.png)
{: style="text-align: center;"}

***Depth***
{: style="text-align: center;"}

![](/assets/2020-04-11-deferred-and-forward-rendering-in-ue4/3.png)
{: style="text-align: center;"}

***Screen-space reflections***
{: style="text-align: center;"}

These frames were captured using [Renderdoc](https://renderdoc.org/).