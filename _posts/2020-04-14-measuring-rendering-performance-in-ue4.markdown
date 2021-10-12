---
layout: post
title:  "Measuring rendering performance in Unreal Engine 4"
---
The basic performance metrics are frame rate, and its inverse, milliseconds per frame.

A key decision that you have to make is the frame rate at which your application should run. Having established that, everything you do should be geared towards achieving that frame rate and maintaining it: your settings, your content, your code.

The ***Shader Complexity*** view mode renders the scene with a color visualization of the cost in number of instructions of computing each pixel in the pixel shader, from green/cheap to red/expensive to white/extremely-expensive.

***Shader complexity view mode***
{: style="text-align: center;"}

![](/assets/2020-04-14-measuring-rendering-performance-in-ue4/1.png)
{: style="text-align: center;"}

***Shader complexity visualization***
{: style="text-align: center;"}

![](/assets/2020-04-14-measuring-rendering-performance-in-ue4/2.png)
{: style="text-align: center;"}

Complexity is influenced by very many factors: the number of translucent layers, the number and complexity of the materials assigned to meshes, the polygon count of the meshes, the number of meshes and the number of draw calls made, and whether you use dynamic shadows.

***Translucency*** is expensive. Say you have multiple overlapping planes with translucent colors that move up and down, back and forth. The recalculation of the color of each pixel on every frame is extremely expensive, as shown by the shader complexity visualization. The FPS is low.

Evidence that this cost is per pixel is that when you dolly the camera back, thus reducing the number of pixels occupied by the overlapping translucent planes, the FPS increases.

***Multiple overlapping translucent planes***
{: style="text-align: center;"}

![](/assets/2020-04-14-measuring-rendering-performance-in-ue4/3.png)
{: style="text-align: center;"}

***Shader complexity visualization of overlapping translucent planes***
{: style="text-align: center;"}

![](/assets/2020-04-14-measuring-rendering-performance-in-ue4/4.png)
{: style="text-align: center;"}

***Many more overlapping translucent planes***
{: style="text-align: center;"}

![](/assets/2020-04-14-measuring-rendering-performance-in-ue4/5.png)
{: style="text-align: center;"}

***Fewer expensive pixels when the camera dollies back***
{: style="text-align: center;"}

![](/assets/2020-04-14-measuring-rendering-performance-in-ue4/6.png)
{: style="text-align: center;"}

***Materials*** can be very expensive too. Pixels of meshes with complex materials are more expensive to compute. Meshes with multiple materials too.

The more pixels of the frame that a mesh with a complex material covers, the lower the frame rate becomes. When the camera dollies into a model with a complex material, the FPS decreases.

***A complex material***
{: style="text-align: center;"}

![](/assets/2020-04-14-measuring-rendering-performance-in-ue4/7.png)
{: style="text-align: center;"}

***A mesh with that complex material***
{: style="text-align: center;"}

![](/assets/2020-04-14-measuring-rendering-performance-in-ue4/8.png)
{: style="text-align: center;"}

***Shader complexity of that frame***
{: style="text-align: center;"}

![](/assets/2020-04-14-measuring-rendering-performance-in-ue4/9.png)
{: style="text-align: center;"}

A frame gets rendered ***mesh by mesh, draw call by draw call***. Pixels get recomputed over and over as each mesh gets drawn. The more meshes you have in view, the more draw calls are made, and the lower the FPS becomes.

***A scene with many meshes***
{: style="text-align: center;"}

![](/assets/2020-04-14-measuring-rendering-performance-in-ue4/10.png)
{: style="text-align: center;"}

***Dynamic shadows*** have a huge cost as well. The higher the polygon count in the scene, the more expensive it is to compute dynamic shadows.

For more, see Introducing the Principles of Real-time, [unrealengine.com](https://www.unrealengine.com/en-US/onlinelearning-courses).