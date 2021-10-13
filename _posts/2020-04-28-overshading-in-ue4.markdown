---
layout: post
title:  "Overshading in Unreal Engine 4"
---
The pixel shader executes mesh by mesh, because a pixel shader execution is the result of a draw call, and a draw call (or more) is made per mesh.

The pixel shader doesn't execute pixel by pixel. Instead, it executes for every 2x2 quad covered by each triangle of the mesh (that passes the Z-buffer depth test?). Processing in quads allows the pixel shader to take neighboring pixels into account. This is known as ***quad overshading***: when shading a triangle, not only the pixels actually occupied by it will be processed, the rest of the pixels of the quad will be processed too.

That's ***overshading level 1***. Another level of overshading happens when processing the rest of the triangles: the pixel quads occupied by the current triangle may have been occupied, and processed, previously by another triangle.

So the more overlapping triangles there are (the denser a pixel is with triangles), the more overshading takes place. And the farther from the scene that the camera moves away, the denser pixels become. The ***quad overdraw*** visualization shows the amount of overshading of the current frame.

***Quad overdraw visualization***
{: style="text-align: center;"}

![](/assets/2020-04-28-overshading-in-ue4/1.png)
{: style="text-align: center;"}

***Pixels aren't dense, little overshading***
{: style="text-align: center;"}

![](/assets/2020-04-28-overshading-in-ue4/2.png)
{: style="text-align: center;"}

***Pixels become denser with distance, more overshading***
{: style="text-align: center;"}

![](/assets/2020-04-28-overshading-in-ue4/3.png)
{: style="text-align: center;"}

***A lot of overshading***
{: style="text-align: center;"}

![](/assets/2020-04-28-overshading-in-ue4/4.png)
{: style="text-align: center;"}

For more, see Introducing the Principles of Real-time, [unrealengine.com](https://www.unrealengine.com/en-US/onlinelearning-courses).