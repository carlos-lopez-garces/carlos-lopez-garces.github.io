---
layout: post
title:  "Texture compression and mipmapping in Unreal Engine 4"
---
Unreal Engine always compresses the textures that you import. Some ***compression methods*** do a better job at maintaining quality while reducing the size. Other methods serve special purposes: DXT5/BC5, for example, is used for compressing ***normal maps***; they only store red and green, and blue can then be derived from them (that is, z can be derived from x and y). BC stands for ***block compression***.

***Texture compression methods***
{: style="text-align: center;"}

![](/assets/img/blog/2020-04-30-texture-compression-and-mipmapping-in-ue4/1.png)
{: style="text-align: center;"}

***Mipmapping*** embeds lower resolution copies of the texture within the texture itself. Each copy is ¼ of the size of the previous one. Mipmapping serves at least 3 purposes:

1. Efficient memory and CPU-to-GPU transmission bandwidth. When a textured object is far from the camera, you don't need the full-resolution texture; one of the lower resolution mipmaps suffices. As the camera nears the object, Unreal Engine will retexture progressively with a higher resolution mipmap.

2. Antialiasing: A full-resolution texture becomes aliased with distance. By dividing the texture in bands and using lower-resolution ***blurrier*** mipmaps as distance increases, aliasing is mitigated.

   ***The distant bands of a texture get aliased***
   {: style="text-align: center;"}

   ![](/assets/img/blog/2020-04-30-texture-compression-and-mipmapping-in-ue4/2.png)
   {: style="text-align: center;"}

   ***Full-resolution textures on distant objects get aliased***
   {: style="text-align: center;"}

   ![](/assets/img/blog/2020-04-30-texture-compression-and-mipmapping-in-ue4/3.png)
   {: style="text-align: center;"}

   ***Using bands of lower-resolution mipmaps for reducing aliasing of a single texture***
   {: style="text-align: center;"}

   ![](/assets/img/blog/2020-04-30-texture-compression-and-mipmapping-in-ue4/4.png)
   {: style="text-align: center;"}

   ***Lower-resolution mipmaps on distant objects reduce aliasing***
   {: style="text-align: center;"}

   ![](/assets/img/blog/2020-04-30-texture-compression-and-mipmapping-in-ue4/5.png)
   {: style="text-align: center;"}

3. Convincing visual effect on the appearance of distant objects. The human eye can't discern detail on distant objects. The use of lower-resolution mipmaps blurs those details.

For more, see Introducing the Principles of Real-time, [unrealengine.com](https://www.unrealengine.com/en-US/onlinelearning-courses).