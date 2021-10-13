---
layout: post
title:  "Shading models in Unreal Engine 4"
---
In the ***Materials editor***, you can see the HLSL code of a material.

***HLSL code viewer of materials***
{: style="text-align: center;"}

![](/assets/2020-04-30-shading-models-in-ue4/1.png)
{: style="text-align: center;"}

HLSL files internal to the engine implement different shading models.

***Built-in shading models for materials***
{: style="text-align: center;"}

![](/assets/2020-04-30-shading-models-in-ue4/2.png)
{: style="text-align: center;"}

One such shading model is ***PBR***: the bases of PBR are specularity, metalness and roughness.

You can use multiple shading models. Here, green uses PBR and the spheres use a different shading model each (e.g. hair, cloth, eye). The spheres are ***pixel masks*** in the GBuffer. Masks allow you to restrict the pixels on which the pixel shader runs.

***Material pixel masks in the GBuffer***
{: style="text-align: center;"}

![](/assets/2020-04-30-shading-models-in-ue4/3.png)
{: style="text-align: center;"}

The 4 factors that impact performance the most are draw calls, pixel shaders (because we use them for so many things), translucency, and dynamic shadows.

The expense of the pixel shader of a material grows with the number of pixels that that material covers.

For more, see Introducing the Principles of Real-time, [unrealengine.com](https://www.unrealengine.com/en-US/onlinelearning-courses).