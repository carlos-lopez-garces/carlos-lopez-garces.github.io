---
layout: post
title:  "Summary of GPU Gems 2, chapter 28: High-Quality Global Illumination Rendering Using Rasterization"
---
High-quality global illumination is hard for GPUs, even in offline rendering (2004).

The characteristic light of GI is that which reflects off of objects that are not light emitters.

At the time of that publication, ray tracing was the technique of choice for high-quality indirect lighting.

What is the "final gather" operation that it talks about?

Defining characteristics of this technique:

	- Use of rasterization hardware in an innovative way to perform ray-object intersections.
	- Fast ray casting in the GPU.
	- Doesn't map to or emulate ray tracing on the GPU; it's not ray tracing.
	- Ray tracing on the CPU requires acceleration structures so that it doesn't have to shoot rays in directions that are not going to hit objects. This technique doesn't need acceleration structures, because the GPU helps you execute your code on fragments of objects already, so you are sure to be executing code on scene locations that actually have objects.

Two-pass methods

	1. Compute a rough render of the scene using radiosity or photon mapping. The two-pass technique doesn't use the full power of either of these methods; this pass is supposed to be quick.
	2. Use the result of the first pass as a source of indirect illumination.

Two-pass methods are more efficient than path tracing and produce an acceptable result. Path tracing requires too many rays to produce a render without noise.

This is the local reflectance equation (see note BRDF) and it computes the outgoing radiance at point x in the direction of the camera:



The BRDF is a ratio of outgoing radiance to incoming radiance, i.e. the amount of light that the surface reflects at the input point relative to the amount of light receives.

Li is the incoming radiance. This is where final gathering occurs: rays are cast over the hemisphere of the point. This is where two-pass methods factor in the result of the first pass, the source of indirect illumination.

The dot product that follows serves the same role as it does in Lambert or ADS, i.e. reflect more light when the light faces the surface directly, as opposed to at an angle. (?) but vector w is the view ray, not the light ray.

Final gathering in green