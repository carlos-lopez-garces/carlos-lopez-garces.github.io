---
layout: post
title:  "Static lighting in Unreal Engine 4"
---
There are different ways to light up the scene; some techniques are static and pre-calculated and some are dynamic and real-time.

Note in the following list that shadow rendering is somewhat independent of light rendering: shadows have their own techniques. Some lighting techniques generate shadows as well, though, like lightmaps.

***Static and dynamic lighting and shadows techniques***
{: style="text-align: center;"}

![](/assets/img/blog/2020-05-13-static-lighting-in-ue4/1.png)
{: style="text-align: center;"}

An example of a static/pre-calculated technique is ***lightmaps***. 

Lightmaps are textures that are ***baked***/built only once and that are then mapped to meshes using UVs. They are calculated ***offline*** during a process called ***lightmass*** in UE. The result is a series of ***lightmap atlases***, which collect lightmaps for different objects.

The lightmap texture contains projected shadows and lit regions. This texture then gets combined with the mesh's diffuse texture or diffuse color.

![](/assets/img/blog/2020-05-13-static-lighting-in-ue4/2.png)
{: style="text-align: center;"}

    1. 2 light sources.
    2. Baking light to a lightmap texture.
    3. Base color texture.
    4. Multiplying lightmap on top of base color texture.
    5. End result.

Like every texture, a lightmap needs UVs to be mapped to a mesh. So meshes contain ***lightmap UVs***. The resolution of the lightmap and the layout of the UVs determine the quality of the lighting of an object. Now, lightmaps, like any texture, consume memory and their resolution is limited by the hardware (the limit could be 4K or sometimes 8K).

After the lightmaps are built, the lights that produced them are lost. If the scene is dynamic, those lights won't illuminate the objects that approach them. If you want those lights to affect the scene in real-time, you can use ***volume lighting samples***, which can be queried to find out the brightness of a region of the scene.

You can see the lightmaps in the UE editor, under World Settings > Lightmass > Lightmaps. There's also a ***lightmap atlas viewer***.

For more, see Introducing the Principles of Real-time, [unrealengine.com](https://www.unrealengine.com/en-US/onlinelearning-courses).