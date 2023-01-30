---
layout: post
title:  "Stencil mirrors with DirectX 12"
---

![](/assets/img/projects/directx12-hlsl-shaders/hlsl-stencil-mirrors/8.png)
{: style="text-align: center"}

* TOC
{:toc}

One of the applications of the stencil test is to obtain planar reflections. In this blog post, I describe my understanding of the key elements of this technique. For an expert treatment, I refer the reader to Frank Luna's *Introduction to 3D Game Programming with DirectX 12* and other sources that I mention throughout.

We start by drawing the geometry of opaque objects; this excludes mirrors and, of course, transparent objects. 

![](/assets/img/projects/directx12-hlsl-shaders/hlsl-stencil-mirrors/1.png)
{: style="text-align: center"}

{% highlight c++ %}
D3D12_GRAPHICS_PIPELINE_STATE_DESC opaquePSODesc;
opaquePSODesc.InputLayout = ...;
opaquePSODesc.pRootSignature = ...;
opaquePSODesc.VS = ...;
opaquePSODesc.PS = ...;
opaquePSODesc.RasterizerState = CD3DX12_RASTERIZER_DESC(D3D12_DEFAULT);
opaquePSODesc.BlendState = CD3DX12_BLEND_DESC(D3D12_DEFAULT);
opaquePSODesc.DepthStencilState = CD3DX12_DEPTH_STENCIL_DESC(D3D12_DEFAULT);
opaquePSODesc.SampleMask = UINT_MAX;
opaquePSODesc.PrimitiveTopologyType = D3D12_PRIMITIVE_TOPOLOGY_TYPE_TRIANGLE;
opaquePSODesc.NumRenderTargets = 1;
opaquePSODesc.RTVFormats[0] = DXGI_FORMAT_R8G8B8A8_UNORM;
opaquePSODesc.SampleDesc.Count = ...;
opaquePSODesc.SampleDesc.Quality = ...;
// 24 bits for depth and 8 bits for stencil. Normalized [0,1].
opaquePSODesc.DSVFormat = DXGI_FORMAT_D24_UNORM_S8_UINT;

Microsoft::WRL::ComPtr<ID3D12PipelineState> opaquePSO;
device->CreateGraphicsPipelineState(&opaquePSODesc, IID_PPV_ARGS(&opaquePSO));

commandList->Reset(commandListAllocator.Get(), opaquePSO.Get());
{% endhighlight %}

## The stencil test

We then draw the geometry of the mirror to the stencil buffer to set up the ***stencil test***. The stencil test is performed by the DirectX graphics pipeline after the ***pixel shader stage*** and during the ***output merger stage***; its function is to discard fragments that don't pass the test so that they don't end up being written to the back buffer.

For our purposes, we apply the stencil test to the fragments of object reflections: we'll reflect the geometries of opaque objects across the mirror's plane so that they end up behind the mirror; then, for each reflected geometry, we'll write to the back buffer the fragments that are visible through the visible surface of the mirror; such a visibility test is performed, precisely, with the stencil test.

Before performing the stencil test on object reflections, we need to set it up. The [official docs](https://learn.microsoft.com/en-us/windows/win32/direct3d9/stencil-buffer-techniques) describe the test as follows:

{% highlight c++ %}
if ((StencilRef & StencilReadMask) StencilFunc (StencilBufferValue & StencilReadMask))
  pass
else
  fail
{% endhighlight %}

Observe that, basically, the test compares the left-hand side value with the right-hand side value using <code>StencilFunc</code>. In our case, for a given reflection fragment, we want the test to pass only if the fragment is contained by a pixel covered by the visible surface of the mirror. We can identify the pixels covered by the visible surface of the mirror by drawing the mirror to the stencil buffer and marking them with 1s.

![](/assets/img/projects/directx12-hlsl-shaders/hlsl-stencil-mirrors/2.png)
{: style="text-align: center"}

With the stencil buffer set up this way, when we get to draw the reflected geometries, each of the fragments will be subject to the stencil test: if the fragment is contained by a pixel whose stencil value is 1, then we want to write it to the back buffer because it's visible through the unoccluded surface of the mirror; on the other hand, if the stencil value is 0, we want to discard it. The stencil test thus becomes:

{% highlight c++ %}
if (1 = StencilBufferValue)
  write to back buffer
else
  discard
{% endhighlight %}

where our <code>StencilRef</code> is constant (<code>1</code>, the value that marks the mirror's visible surface), <code>StencilFunc</code> is the equality operator, and <code>StencilReadMask</code> is not really used.

Observe then that the process can be broken up into 2 stages: (1) set up the stencil buffer and (2) perform the stencil test.

## Setting up the stencil buffer

To set up the stencil buffer, we draw the mirror using an additional PSO with very particular depth, blend, and stencil settings.

{% highlight c++ %}
// Depth/stencil buffer descriptor.
D3D12_DEPTH_STENCIL_DESC mirrorDSDesc;
...

// Blend state descriptor.
CD3DX12_BLEND_DESC mirrorBlendState(D3D12_DEFAULT);
...

// Mirror-marking PSO.
D3D12_GRAPHICS_PIPELINE_STATE_DESC mirrorMarkingPSODesc = opaquePSODesc;
mirrorMarkingPSODesc.BlendState = mirrorBlendState;
mirrorMarkingPSODesc.DepthStencilState = mirrorDSDesc;
{% endhighlight %}

Recall that opaque objects have already been rendered to the back buffer. Thus, the depth values of visible opaque fragments are currently stored in the depth buffer.

![](/assets/img/projects/directx12-hlsl-shaders/hlsl-stencil-mirrors/3.png)
{: style="text-align: center"}

By leaving the depth test enabled, we can filter out the fragments of the mirror that are occluded by opaque objects; the pixels of these discarded fragments of the mirror won't be marked with 1s in the stencil buffer. Now, one thing is to apply the depth test to the mirror's fragments and another is to write their depth values to the depth buffer if they pass the test (these 2 operations are usually done back to back, though). While we do want to keep the depth test enabled, we don't want to write the mirror's depth values: as explained earlier, we'll reflect the geometry of opaque objects across the mirror's plane so that they end up behind the mirror; if we wrote the mirror's depth values (as in the next image), the fragments of reflections would not pass the depth test and would end up hidden behind the mirror (as shown in the second one).

![](/assets/img/projects/directx12-hlsl-shaders/hlsl-stencil-mirrors/4.png)
{: style="text-align: center"}

![](/assets/img/projects/directx12-hlsl-shaders/hlsl-stencil-mirrors/5.png)
{: style="text-align: center"}

With that in mind, this is how we want to configure the depth buffer operations for this PSO:

{% highlight c++ %}
mirrorDSDesc.DepthEnable = true;
mirrorDSDesc.DepthWriteMask = D3D12_DEPTH_WRITE_MASK_ZERO;
mirrorDSDesc.DepthFunc = D3D12_COMPARISON_FUNC_LESS;
{% endhighlight %}

Just as we don't want to touch the depth buffer, we don't want to touch the back buffer either, so we disable writes to it while drawing with this PSO. Frank Luna, in *Introduction to 3D Game Programming with DirectX 12*, does it through the PSO's blend state as follows:

{% highlight c++ %}
mirrorBlendState.RenderTarget[0].RenderTargetWriteMask = 0;
{% endhighlight %}

Now, interestingly enough, setting up the stencil buffer involves performing the stencil test itself, followed by the depth test: when drawing the mirror to the stencil buffer, we get to decide what to do when both the stencil test and the depth test pass (<code>StencilPassOp</code>), when the stencil test passes but the depth test doesn't (<code>StencilDepthFailOp</code>), and when the stencil test fails (<code>StencilFailOp</code>), in which case the depth test doesn't get to take place. For each of these outcomes, one of many possible actions is associated: we can keep the current stencil buffer value (<code>D3D12_STENCIL_OP_KEEP</code>), we can replace it with <code>StencilRef</code> (which we'll set shortly), or we can choose any of the actions in the enum <code>D3D12_STENCIL_OP</code>.

Since our objective at this stage is to fill the stencil buffer with 1s where the mirror's surface is visible and with 0s elsewhere, we clear with 0s the stencil buffer first and then make the stencil test pass every time (<code>StencilFunc = D3D12_COMPARISON_FUNC_ALWAYS</code>) for every mirror fragment so that it is the depth test that ultimately determines whether a 1 (<code>StencilRef</code>) is written (<code>StencilPassOp = D3D12_STENCIL_OP_REPLACE</code>) or the existing 0 is left untouched (<code>StencilFailOp = D3D12_STENCIL_OP_KEEP</code>).

{% highlight c++ %}
...
mirrorDSDesc.StencilEnable = true;
mirrorDSDesc.StencilReadMask = 0xff;
mirrorDSDesc.StencilWriteMask = 0xff;

mirrorDSDesc.FrontFace.StencilFunc = D3D12_COMPARISON_FUNC_ALWAYS;
// Set stencil value to StencilRef = 1 when (stencil test and) depth test passes.
mirrorDSDesc.FrontFace.StencilPassOp = D3D12_STENCIL_OP_REPLACE;
// Shouldn't be needed given that the stencil test is set to pass always.
mirrorDSDesc.FrontFace.StencilFailOp = D3D12_STENCIL_OP_KEEP;
// Keep the existing stencil value, which should be the 0 written when clearing the buffer. 
mirrorDSDesc.FrontFace.StencilDepthFailOp = D3D12_STENCIL_OP_KEEP;

...

// Clear the depth buffer with 1s and the stencil buffer with 0s.
commandList->ClearDepthStencilView(..., D3D12_CLEAR_FLAG_DEPTH | D3D12_CLEAR_FLAG_STENCIL, 1.0f, 0, ...);

...

// Set StencilRef = 1.
commandList->OMSetStencilRef(1);
{% endhighlight %}

This should be enough to obtain a stencil buffer that looks like this:

![](/assets/img/projects/directx12-hlsl-shaders/hlsl-stencil-mirrors/2.png)
{: style="text-align: center"}

## Performing the stencil test

Having marked the mirror on the stencil buffer, we proceed to draw the reflections of objects using yet another PSO. This time, we do want a fully functional depth test, as well as to write to the back buffer whenever both the stencil and depth tests pass.

{% highlight c++ %}
// Mirror reflections PSO.
D3D12_DEPTH_STENCIL_DESC reflectionsDSDesc;
reflectionsDSDesc.DepthEnable = true;
reflectionsDSDesc.DepthWriteMask = D3D12_DEPTH_WRITE_MASK_ALL;
reflectionsDSDesc.DepthFunc = D3D12_COMPARISON_FUNC_LESS;
{% endhighlight %}

To reiterate the goal, we want to create the illusion that the mirror reflects the objects in front of it. It's just that, an illusion. In reality, the mirror acts as a window to the other side of it, where we'll place the reflected geometries of objects. If we didn't perform the stencil test, the illusion would be dispelled and the trick revealed.

![](/assets/img/projects/directx12-hlsl-shaders/hlsl-stencil-mirrors/6.png)
{: style="text-align: center"}

To reflect opaque objects across the mirror's plane, we may represent the mirror's plane using its normal vector and then obtain a reflection matrix for it, which we can then multiply by the original object's world matrix to obtain the reflection's world matrix.

{% highlight c++ %}
XMFLOAT4X4 originalWorldMatrix;
...
XMFLOAT4X4 reflectionWorldMatrix:
XMVECTOR mirrorPlaneNormal = XMVectorSet(...);
XMMATRIX R = XMMatrixReflect(mirrorPlaneNormal);
XMStoreFloat4x4(&reflectionWorldMatrix, originalWorldMatrix * R);
{% endhighlight %}

Draw calls for a reflected object would then use the original object's geometry, the newly obtained world matrix, and the PSO that we are currently building.

Now's time to set up the stencil test described earlier: whenever the stencil buffer value is equal (<code>StencilFunc = D3D12_COMPARISON_FUNC_EQUAL</code>) to 1 (<code>StencilRef = 1</code>), the stencil test should pass and, if the depth test passes too, the output of the pixel shader should be written to the back buffer. Unlike the case of the mirror marking PSO, with this PSO we don't want to update the stencil buffer, so all the registered actions should be <code>D3D12_STENCIL_OP_KEEP</code>.

{% highlight c++ %}
reflectionsDSDesc.StencilEnable = true;
reflectionsDSDesc.StencilReadMask = 0xff;
reflectionsDSDesc.StencilWriteMask = 0xff;
reflectionsDSDesc.FrontFace.StencilFunc = D3D12_COMPARISON_FUNC_EQUAL;
// Don't update the stencil buffer.
reflectionsDSDesc.FrontFace.StencilPassOp = D3D12_STENCIL_OP_KEEP;
reflectionsDSDesc.FrontFace.StencilFailOp = D3D12_STENCIL_OP_KEEP;
reflectionsDSDesc.FrontFace.StencilDepthFailOp = D3D12_STENCIL_OP_KEEP;

D3D12_GRAPHICS_PIPELINE_STATE_DESC reflectionsPSODesc = opaquePSODesc;
reflectionsPSODesc.DepthStencilState = reflectionsDSDesc;
{% endhighlight %}

With these settings, we obtain the desired effect. However, the reflection on the mirror doesn't look quite right. 

![](/assets/img/projects/directx12-hlsl-shaders/hlsl-stencil-mirrors/7.png)
{: style="text-align: center"}

Frank Luna, in *Introduction to 3D Game Programming with DirectX 12*, explains that this is because vertices got transformed by the reflection matrix, but their order in the vertex buffer did not change and this order, the triangle winding order, is what determines the normal vectors of triangles and thus their orientation; after all, we reused the vertex buffer of the object geometries. Correcting this is as simple as reversing the vertex winding order in the PSO's rasterizer state:

{% highlight c++ %}
...
reflectionsPSODesc.RasterizerState.FrontCounterClockwise = true;
{% endhighlight %}

The end result is as follows:

![](/assets/img/projects/directx12-hlsl-shaders/hlsl-stencil-mirrors/8.png)
{: style="text-align: center"}