---
layout: post
title:  "Visibility and occlusion in Unreal Engine 4"
---
Much in rendering is a mix of techniques used simultaneously. A single one doesn't cut it. That is true for ***occlusion***.

Occlusion in Unreal designates the process that eliminates the objects in the scene that are not visible to the camera. It happens mostly on the CPU so that the GPU can work with what is visible.

Occlusion is a per-object process, not a per-polygon process. It suffices for a single polygon of a given object to be visible for that object to be considered visible.

Unreal Engine uses 4 techniques of occlusion in sequence:

	1. Distance culling
	2. Frustum culling
	3. Precomputed visibility
	4. Occlusion culling

They execute in order of cost, from cheap to compute to expensive. Each step filters objects out, leaving the next step with fewer objects to execute over.

***Distance culling*** eliminates the objects that are farther than a configurable distance away from the camera. This is an LOD setting, and is set per object: object A may get rendered, whereas B may not, even if A is farther away from the camera than B, if A's distance culling setting allows it.

Distance culling can also be set by volume, where the setting applies to all the objects enclosed by the given ***cull distance volume***.

***Multiple cull distance volumes***
{: style="text-align: center;"}

![](/assets/img/blog/2020-04-20-visibility-and-occlusion-in-ue4/1.png)
{: style="text-align: center;"}

***Frustum culling*** eliminates the objects that are not currently in view. Notice next that this dungeon-like environment is not a single mesh, but multiple ones. As the camera pans the scene, meshes that are not in view get culled.

***Panning camera, with meshes getting in and out of view***
{: style="text-align: center;"}

![](/assets/img/blog/2020-04-20-visibility-and-occlusion-in-ue4/2.png)
{: style="text-align: center;"}

![](/assets/img/blog/2020-04-20-visibility-and-occlusion-in-ue4/3.png)
{: style="text-align: center;"}

***Precomputed visibility*** divides the scene with a grid inside a precomputed visibility volume. Each cell of the grid stores the other cells that are visible from there.

***Precomputed visibility grid***
{: style="text-align: center;"}

![](/assets/img/blog/2020-04-20-visibility-and-occlusion-in-ue4/4.png)
{: style="text-align: center;"}

***Precomputed visibility volumes***
{: style="text-align: center;"}

![](/assets/img/blog/2020-04-20-visibility-and-occlusion-in-ue4/5.png)
{: style="text-align: center;"}

***Occlusion culling*** is the last step and is extremely accurate (but it executes with fewer objects, owing to the filtering done by all the previous steps).

The ***freezerendering*** command pauses occlusion. Every frame that follows gets rendered with the last occlusion results computed before the command. You can then see the objects that were left by the occlusion process.

***Before freezerendering***
{: style="text-align: center;"}

![](/assets/img/blog/2020-04-20-visibility-and-occlusion-in-ue4/6.png)
{: style="text-align: center;"}

***Freezerendering***
{: style="text-align: center;"}

![](/assets/img/blog/2020-04-20-visibility-and-occlusion-in-ue4/7.png)
{: style="text-align: center;"}

***After freezerendering***
{: style="text-align: center;"}

![](/assets/img/blog/2020-04-20-visibility-and-occlusion-in-ue4/8.png)
{: style="text-align: center;"}

On the composition of meshes and their impact to occlusion and rendering: you may choose a model to be comprised of a single large mesh, or of smaller component meshes. The large mesh makes the process of occlusion faster, because being a per-object process, it has a single object to check; but then the larger the mesh is, the more likely it is that it will be in view and pass the occlusion test and therefore have its entire set of triangles be transferred to the GPU. Whereas smaller component meshes is more work for the occlusion process, but fewer resulting triangles for the GPU.

The more meshes that pass the occlusion tests, the more polygons are transferred to GPU memory; the more polygons you have, the more vertices you have; the more vertices you have, the more vertex shader executions take place.

The occlusion process's job is to reduce the number of meshes/triangles/vertices that get passed to the GPU.

For more, see Introducing the Principles of Real-time, [unrealengine.com](https://www.unrealengine.com/en-US/onlinelearning-courses).