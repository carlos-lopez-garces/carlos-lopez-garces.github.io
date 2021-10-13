---
layout: post
title:  "Early Z-pass and LOD in Unreal Engine 4"
---
***Early Z-pass*** is a kind of object depth testing for avoiding the execution of the pixel shader on objects whose pixels will be overwritten by the pixels of objects in front of them.

***Extremely bad shader complexity without early Z-pass [(video)](https://www.youtube.com/watch?v=aPo8FsZzF4w&ab_channel=Steve%27sTutorials)***
{: style="text-align: center;"}

![](/assets/2020-04-26-early-z-pass-and-lod-in-ue4/1.png)
{: style="text-align: center;"}

***Excellent shader complexity with early Z-pass***
{: style="text-align: center;"}

![](/assets/2020-04-26-early-z-pass-and-lod-in-ue4/2.png)
{: style="text-align: center;"}

One ***draw call per material per object*** is made. If an object has 10 materials, 10 draw calls are made for that object.

The order of the draw calls is influenced by the materials in the scene. See this scene, for example, the 1st and 2nd cylinders and the bottom of the 3rd one share the same material. Now see the rendering order: the bottom half of the 3rd one and the 1st and 2nd ones are rendered before the top half of the 3rd; that's because they shared the same material.

***Objects with shared materials***
{: style="text-align: center;"}

![](/assets/2020-04-26-early-z-pass-and-lod-in-ue4/3.png)
{: style="text-align: center;"}

***Rendering order influenced by the materials***
{: style="text-align: center;"}

![](/assets/2020-04-26-early-z-pass-and-lod-in-ue4/4.png)
{: style="text-align: center;"}

Polygon count has an impact on performance, and it used to be a major one in the 90s when renderers and GPUs were simpler and less powerful. Nowadays, the number of draw calls has a bigger impact than polygon count: for one, sophisticated renderers like Unreal Engine add their own draw calls for system purposes; and then there's the overhead of each call.

If you want to reduce the number of draw calls, start by merging your meshes into larger ones so that you have fewer. And the meshes that you decide to merge should have common materials; otherwise there'll still be separate draw calls per material of that larger mesh. But keep in mind that occlusion will be  less effective: the entire set of triangles of the larger mesh will pass the occlusion test if even one of them appears in the current frame. More triangles for the next step to work with. More triangles to keep in memory for the next step.

The ***Statistics window*** shows the number of instances per distinct object, as well as the number of triangles per instance.

***Statistics window***
{: style="text-align: center;"}

![](/assets/2020-04-26-early-z-pass-and-lod-in-ue4/5.png)
{: style="text-align: center;"}

![](/assets/2020-04-26-early-z-pass-and-lod-in-ue4/6.png)
{: style="text-align: center;"}

Another way to reduce the number of draw calls is with ***instanced static meshes***. When you copy and paste a mesh in the editor, you are creating copies of the memory of the copied mesh, and each one will be rendered in its own draw call. But if you select and group them as static instances, then you'll only use the memory of the original one and all of the instances will be part of the same draw call. This is recommended for grass.

***LOD*** replaces a mesh with a given number of polygons with an equivalent mesh with a different number of polygons dynamically and based on distance.

***Low polygon count mesh***
{: style="text-align: center;"}

![](/assets/2020-04-26-early-z-pass-and-lod-in-ue4/7.png)
{: style="text-align: center;"}

***Replaced with higher polygon count mesh when the distance becomes shorter***
{: style="text-align: center;"}

![](/assets/2020-04-26-early-z-pass-and-lod-in-ue4/8.png)
{: style="text-align: center;"}

LOD doesn't have any effect on the number of draw calls. ***Hierarchical LOD***, or HLOD, however, allows you to replace a number of separate meshes with a single lower-poly one dynamically, so that when the distance with respect to that group of meshes increases, they get replaced with a single mesh. Up close, each of the meshes would make a draw call; at some distance, the group would make only one.

***Up close: 4 high-poly meshes, 4 draw calls (if they had the same material)***
{: style="text-align: center;"}

![](/assets/2020-04-26-early-z-pass-and-lod-in-ue4/9.png)
{: style="text-align: center;"}

***At some distance: 1 low-poly mesh, 1 draw call***
{: style="text-align: center;"}

![](/assets/2020-04-26-early-z-pass-and-lod-in-ue4/10.png)
{: style="text-align: center;"}

Grass, foliage, semi-still water and cloth tend to be animated in the vertex shader and not on the CPU. It would be prohibitively expensive to animate every blade of grass on the GPU. Instead, Unreal lets you take advantage of the massively parallel execution of the vertex shader: a material has a vertex offsetting property, a vector by which the vertex shader will displace each vertex. This is only a visual effect: the CPU is never aware of the displacement.

For more, see Introducing the Principles of Real-time, [unrealengine.com](https://www.unrealengine.com/en-US/onlinelearning-courses).