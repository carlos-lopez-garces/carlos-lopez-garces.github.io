---
layout: post
title:  "Variable Rate Shading in Vulkan"
---

Rendering engines that implement *Variable Rate Shading* (VRS) are capable of changing at run-time the rate at which regions of pixels are shaded by the fragment shader. Regions of a given frame may be shaded at full rate, with one fragment shader invocation per fragment, as usual, while others may share the same output. When implemented properly, rendering time per frame can be reduced substantially, while maintaining the same perceived quality.

*VRS* grabbed my attention recently again because we implemented a Sobel filter in [CIS-4600](https://www.cis.upenn.edu/~cis4600/23fa/index.html){:target="_blank"} and, at least in [Castorina and Sassone's engine](https://www.packtpub.com/product/mastering-graphics-programming-with-vulkan/9781803244792){:target="_blank"}, this filter is a key element of a VRS solution.

* TOC
{:toc}

The VK_KHR_fragment_shading_rate extension allows us to influence the number of pixels that a given fragment shader invocation contributes to. This number of pixels is specified in the form of a horizontal x vertical rate: a rate of 1x1 specifies that a each fragment is shaded by its own fragment shader invocation; a rate of 2x1 specifies that a given fragment shader invocation contributes to 2 pixels located side by side horizontally; a rate of 1x2, that it contributes to 2 pixels located side by side vertically; a rate of 2x2, that it contributes to 4 pixels in a 2x2 layout; and so on.

Castorina and Sassone, in [Mastering Graphics Programming with Vulkan](https://www.packtpub.com/product/mastering-graphics-programming-with-vulkan/9781803244792){:target="_blank"}, reduce the shading rate in areas of the image where luminance is uniform, while keeping it 1x1 in areas of transition. Since the human eye pays more attention to these areas of transition, we'd better shade them at full-rate, whereas the loss of definition (?) caused by shading at a reduced rate, in areas where luminance doesn't change much, will barely be noticeable.

In the detection of areas of transition is where the Sobel filter comes into play. Given an image, applying a Sobel filter will result in one that reveals areas of high-frequency content. If we apply it to an image where each pixel value is the luminance of the corresponding pixel in the previous frame, the resulting image will emphasize sharp changes of luminance in the form of edges. Then, informed by the Sobel-filtered image, we can choose to render the next frame at a reduced shading rate in areas (of relatively constant luminance) that are far from the edges, and at full rate in areas near them (where preserving detail is important).

|![](/assets/img/projects/vulkan/cow_lambert.png)|![](/assets/img/projects/vulkan/cow_sobel.png)|

{: style="text-align: center;"}
*Mesh and background image provided by UPenn's CIS-4600. [Cow texture by juicy_fish](https://www.freepik.com/free-vector/set-black-blobs-flat_44472049.htm#query=cow%20texture&position=2&from_view=keyword&track=ais) on Freepik.*
