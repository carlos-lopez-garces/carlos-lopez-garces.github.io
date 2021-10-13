---
layout: post
title:  "Reflections in Unreal Engine 4"
---
No single technique for reflections suffices. Unreal Engine uses a combination of 3:

	1. Cubemap captures.
	2. Planar captures.
	3. Screen space reflections or SSR.

***Static captures*** place a camera inside of a cube or a sphere centered at a point marked by an ***actor*** and renders 6 images, one for each interior face of the cube, if the actor is a cube, or 1 360 degree image if the actor is a sphere. 

When a mesh is close enough to the actor and it has a reflective material, the draw call of that mesh + material will use the captured images in the GBuffer composite frame. "Close enough" means that the mesh is within the range of the actor; objects beyond the range of the actor do not participate in the capture.

***The range of the actor: the floor is within the range of the actor, so it uses its captured reflections; the capture includes only 5 of the 7 pillars and the teapot, because the other 2 pillars are beyond the range***
{: style="text-align: center; width: 70%; margin: 0 auto; padding-bottom: 15px;"}

![](/assets/2020-05-10-reflections-in-ue4/1.png)
{: style="text-align: center;"}

These images are ***baked*** into the actor when loading the level and are ***not computed in real time***. If the scene is dynamic and objects move close enough to appear in the reflection, they nevertheless won't be accounted for.

The baked reflections only make sense from the POV that the camera had when they were built, and will cease to make sense when the camera moves.

***When the camera is not placed where the static capture actor is, the reflections of the pillars don't make sense***
{: style="text-align: center; width: 70%; margin: 0 auto; padding-bottom: 15px;"}

![](/assets/2020-05-10-reflections-in-ue4/2.png)
{: style="text-align: center;"}

***When the camera is placed where the actor is, the reflections make sense***
{: style="text-align: center;"}

![](/assets/2020-05-10-reflections-in-ue4/3.png)
{: style="text-align: center;"}

***Planar captures*** are used with planar surfaces (e.g. polished floor and tables, mirrors). These reflections are not baked, they are dynamic and computed in real-time (the motion of objects will be reflected and will make sense from any camera POV).

***Screen space reflections*** are dynamic too, but they are noisy. They are called ***screen space*** because only the objects that appear on the screen participate in the reflection, without accounting for objects that are outside of the view frustum (e.g. objects behind the camera or objects that are not in view).

For example, these pillars are tall, but they appear to be short in the reflection. That's because only the portions (of the pillars) that appear on the screen participate in the reflection.

![](/assets/2020-05-10-reflections-in-ue4/4.png)
{: style="text-align: center;"}

Here the reflections are longer because a larger portion of each pillar is visible on the screen.

![](/assets/2020-05-10-reflections-in-ue4/5.png)
{: style="text-align: center;"}

Skylights also do reflection captures by default. You can use that capture to have reflections of the sky on exteriors.

For more, see Introducing the Principles of Real-time, [unrealengine.com](https://www.unrealengine.com/en-US/onlinelearning-courses).