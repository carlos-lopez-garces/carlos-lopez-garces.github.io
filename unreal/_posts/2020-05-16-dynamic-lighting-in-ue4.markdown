---
layout: post
title:  "Dynamic lighting in Unreal Engine 4"
---
Unlike static lighting, dynamic lighting happens in the GBuffer in deferred rendering mode.

Dynamic lighting is one more pass of the GBuffer.

In general, UE4's dynamic lighting system should not be used to do radiosity and global illumination; it has many limitations in this area. They ***don't do soft shadows well*** either; the shadows produced are very sharp.

Lighting can be classified by ***mobility*** type:

![](/assets/img/blog/2020-05-16-dynamic-lighting-in-ue4/1.png)
{: style="text-align: center;"}

	- Static, baked light maps.
	- Stationary, a combination of static and dynamic (movable).
	- Movable, fully dynamic.

***Regular dynamic lights*** produce hard shadows that are extremely sharp, unrealistically sharp. Their darkness softens a little when blended with the light maps. These lights don't bounce either, so they don't participate in global illumination. See here how the shadow reveals how low-poly the occluder mesh is:

![](/assets/img/blog/2020-05-16-dynamic-lighting-in-ue4/2.png)
{: style="text-align: center;"}

***Stationary light shadows*** are per-object, unlike regular dynamic shadows, whose lights affect all the objects within range. They bake a lightmap into the object and also lights it in real-time in the GBuffer. Less sharp than regular dynamic shadows, softer.

![](/assets/img/blog/2020-05-16-dynamic-lighting-in-ue4/3.png)
{: style="text-align: center;"}

***Visualized in Lighting Only mode***
{: style="text-align: center;"}

![](/assets/img/blog/2020-05-16-dynamic-lighting-in-ue4/4.png)
{: style="text-align: center;"}

None of these are suitable for large exteriors: you don't need that kind of detailed shadows for far away objects, and it would also be prohibitively expensive to light and shadow such an area. What you want instead are cascading shadow maps that gradually fade and transition from one level of detail of shadow maps to another. See here how the shadow gets revealed as the camera moves near the balustrade:

![](/assets/img/blog/2020-05-16-dynamic-lighting-in-ue4/5.png)
{: style="text-align: center;"}

![](/assets/img/blog/2020-05-16-dynamic-lighting-in-ue4/6.png)
{: style="text-align: center;"}

![](/assets/img/blog/2020-05-16-dynamic-lighting-in-ue4/7.png)
{: style="text-align: center;"}

Not only does the shadow get revealed as the camera moves closer to the object, the resolution of the shadow map also increases:

***Low resolution when the camera is far***
{: style="text-align: center;"}

![](/assets/img/blog/2020-05-16-dynamic-lighting-in-ue4/8.png)
{: style="text-align: center;"}

***High resolution when the camera is near***
{: style="text-align: center;"}

![](/assets/img/blog/2020-05-16-dynamic-lighting-in-ue4/9.png)
{: style="text-align: center;"}

For more, see Introducing the Principles of Real-time, [unrealengine.com](https://www.unrealengine.com/en-US/onlinelearning-courses).