---
layout: post
title:  "Lightmass in Unreal Engine 4"
---
Lightmass is the system in Unreal that bakes the lighting in the scene. When you place a static light object in the scene, it will illuminate the scene dynamically ***until you rebuild the lighting***, that is, the lighting won't be baked until ***lightmass*** runs and bakes it. Before you bake it, the light is temporarily dynamic, because it shows you a ***preview*** of what the baked lighting would look like.

![](/assets/img/blog/2020-05-20-lightmass-in-ue4/1.png)
{: style="text-align: center;"}

What lightmass does is generate ***lightmaps*** for the objects of the scene influenced by all the static lights and uses them to texture them.

Once lightmass finishes and the lighting is baked, if you move the light object again, you'll get 2 distinct sets of shadows and lights: the previously baked and the new preview. If you remove the object, the lighting remains:

![](/assets/img/blog/2020-05-20-lightmass-in-ue4/2.png)
{: style="text-align: center;"}

Lightmass is run by a ***swarm agent***, which is a process that can run in parallel or distributed.

***Lightmass importance volumes*** are boxes that you can place in the scene to surround regions where you want lightmass to operate with high quality, as opposed to operating at high quality in the entire scene.

![](/assets/img/blog/2020-05-20-lightmass-in-ue4/3.png)
{: style="text-align: center;"}

***With light object and preview***
{: style="text-align: center;"}

![](/assets/img/blog/2020-05-20-lightmass-in-ue4/4.png)
{: style="text-align: center;"}

***After baking and removing the light object***
{: style="text-align: center;"}

![](/assets/img/blog/2020-05-20-lightmass-in-ue4/5.png)
{: style="text-align: center;"}

Just like lightmass importance volumes, ***lightmass portals*** help you tell the lightmass process where to focus its computations. Lightmass portals are typically placed at entrances and openings of buildings: the portals attract more bounces of the skylight, that is, it tells the lightmass process to send more light bounces through the portal and into the building to properly illuminate it.

![1](/assets/img/blog/2020-05-20-lightmass-in-ue4/6.png)
{: style="text-align: center;"}