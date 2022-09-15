---
layout: post
title:  "Subsurface scattering and the BSSRDF (WIP)"
---

The BRDF, BTDF, and BSSRDF are distribution functions that describe how incident light is scattered by a surface. This post will describe the BSSRDF and its role in simulating subsurface light transport.


### Bidirectional Scattering Distribution Function or BBSRDF

The BSSRDF is a generalization of the BRDF that also models subsurface light transport: light that enters a surface at some point $$p_{i}$$ may exit through a different point $$p_{o}$$. Most materials exhibit subsurface light transport to varying degrees. For many of them, subsurface light transport has such a minimal effect on how light reflects off of the surface, that it's better to ignore it. The BRDF does just that.

What the BRDF and BSSRDF model are the spectral and directional distributions of reflected light. The spectral power distribution (SPD) of incident light is not the same as the SPD of reflected light: as light scatters beneath the surface, radiance gets absorbed at different rates per wavelength before it exits the surface. Furthermore, the microgeometry of the surface determines the direction.

The BSSRDF generalizes the BRDF to account for light that exits the surface at a point other than where it enters.

The BSSRDF is the ratio of exitant differential radiance at $$p_o$$ in direction $$\omega_o$$ to the incident differential flux at $$p_i$$ from direction $$\omega_i$$:

$$
\begin{aligned}
S(p_o, \omega_o, o_i, \omega_i) = \frac{dL_o(p_o, \omega_o)}{d\Phi(p_i, \omega_i)}
\end{aligned}
$$

The subsurface scattering equation is a generalization of the scattering equation:

$$
\begin{aligned}
L_o(p_o, \omega_o) = \int_{A}{\int_{H^2(n)}{S(p_o, \omega_o, p_i, \omega_i) L_i(p_i, \omega_i)\vert\cos{\theta_i} \vert \,d\omega_i} \,dA}
\end{aligned}
$$

Observe that unlike the scattering equation, which is an integral over incident direction, the subsurface scattering equation is a double integral over incident direction and area.

As the distance between points $$p_i$$ and $$p_o$$ increases, the value of $$S$$ generally diminishes.

The implementation of subsurface scattering is based on volume light transport in participating media.

The subsurface scattering equation gives exitant radiance at $$p_o$$ in the direction $$\omega_o$$ given incident irradiance at point $$p_i$$ from direction $$\omega_i$$:

$$
\begin{aligned}
L_o(p_o, \omega_o) = \int_{A}{\int_{H^2(n)}{S(p_o, \omega_o, p_i, \omega_i) L_i(p_i, \omega_i)\vert\cos{\theta_i} \vert \,d\omega_i} \,dA}
\end{aligned}
$$

The BSSRDF $$S(p_o, \omega_o, p_i, \omega_i)$$ computes the ratio of differential exitant radiance at $$p_o$$ in the direction $$\omega_o$$ to the incident differential flux at $$p_i$$ from direction $$\omega_i$$.

Subsurface light transport is described by volumetric scattering processes and the volume light transport equation (the equation of transfer).

BSSRDF is the main interface. A BSSRDF is initialized with the SurfaceInteraction at $$p_o$$ and the index of refraction of the scattering medium.

BSSRDF::S evaluates the BSSRDF S, which is an 8-dimensional function and computes the ratio of differential exitant radiance at $$p_o$$ in the direction $$\omega_o$$ (found in SurfaceInteraction::wo) to differential incident flux at $$p_i$$ from $$\omega_i$$.

### Separable BSSRDFs

A separable BSSRDF is a product of 3 functions that approximates the BSSRDF. There's one spatial factor and 2 directional factors:

$$
\begin{aligned}
S(p_o, \omega_o, p_i, \omega_i) \approx (1 - F_r(\cos⁡{\theta_o})S_p(p_o, p_i)S_{\omega}(\omega_i)
\end{aligned}
$$

which effectively decouples the spatial and directional arguments of $$S$$.

The Fresnel term at the beginning models the fraction of the light that is transmitted into direction $$\omega_o$$ after exiting the surface. $$F_r(\cos⁡{\theta_o})$$ is the fraction of light that gets reflected, so $$1 - F_r(\cos{\theta_o})$$ is the fraction that gets transmitted if we follow the principle of conservation of energy.

A second Fresnel term contained inside $$S_{\omega}(\omega_i)$$ accounts for the influence of the boundary on the directional distribution of light entering the object from direction $$\omega_i$$:

$$
\begin{aligned}
S_{\omega}(\omega_i) = \frac{1 - F_r{\cos{\theta_i}}}{c\pi}
\end{aligned}
$$

where $$c$$ is a normalization factor chosen so that $$S_{\omega}(\omega_i)\cos⁡{\theta}$$ integrates to 1 over the cosine-weighted hemisphere $$H^2$$:

$$
\begin{aligned}
\int_{H^2}{S_\omega(\omega)\cos{\theta}\,d\omega} = 1
\end{aligned}
$$

That is, $$c$$ is given by

$$
\begin{aligned}
c &= \int_{0}^{2\pi}{\int_{0}^{\pi/2}{\frac{1 - F_r(\eta, \cos{\theta})}{\pi}\sin{\theta}\cos{\theta}\,d\theta}\,d\phi} \\
&= 1 - 2\int_{0}^{\pi/2}{F_r{\eta, \cos{\theta}\sin{\theta}\cos{\theta}}\,d\theta}
\end{aligned}
$$

where the integral is known as the first moment of the Fresnel reflectance function. The ith Fresnel moment is

$$
\begin{aligned}
\bar{F}_{r, i}(\eta)=\int_{0}^{\pi/2}{F_r(\eta, \cos{\theta})\sin{\theta}\cos^{i}{\theta}\,d\theta}
\end{aligned}
$$

PBRT computes approximations of the 1st and 2nd moments using polynomials of degree 5.

The scattering profile term $$S_p$$ is a spatial distribution characterizing how far light travels after entering the material. PBRT doesn't give the actual expression of $$S_p$$ and instead gives and implements an approximation. This approximation is based on 2 assumptions:

	- The surface is locally planar.
	- It isn't the location of the 2 points, but the distance between them that affects the value of $$S$$.

The approximation is:

$$
\begin{aligned}
S_p(p_o, p_i) \approx S_r(\Vert p_o - p_i \Vert)
\end{aligned}
$$

where $$S_r$$ is implemented by SeparableBSSRDF subclasses and is called radial profile.

$$S_r$$ is a 1D function (as shown above) but only when the rest of the parameters are fixed: the index of refraction $$\eta$$, the scattering anisotropy $$g$$, the albedo $$\rho$$, and the extinction coefficient $$\sigma_t$$ (rate of scattering or absorption per unit distance): $$S_r(\eta, g, \rho, \sigma_t, r)$$.

The separable approximation simplifies the subsurface scattering equation by allowing us to integrate solely with respect to incident direction first and then with respect to area only.

$$
\begin{aligned}
L_o(p_o, \omega_o) &= \int_{A}{\int_{H^2(n)}{S(p_o, \omega_o, p_i, \omega_i) L_i(p_i, \omega_i)\vert\cos{\theta_i} \vert \,d\omega_i} \,dA} \\

&= (1 - F_r(\cos{\theta_o}))\int_{A}{S_p(p_o, p_i)\int_{H^2(n)}{S_{\omega}L_i(p_i,\omega_i)\vert\cos{\theta_i}\vert \,d\omega_i}\,dA(p_i)}
\end{aligned}
$$

This decoupling of spatial and directional arguments of the BSSRDF $$S$$ reduces the dimension of $$S$$.

### Sampling the BSSRDF

Point $$p_o$$ is given and points $$p_i$$ are sampled on the surface. At each of these points $$p_i$$, we compute incident radiance. The idea is that radiance arriving at several points of incidence $$p_i$$  contributes to the radiance leaving at $$p_o$$. The BSSRDF weights these contributions.

Media with high albedo is traversed deeper. Hundreds or thousands of scattering events must be considered, for example, in the case of skim milk: after 100 scattering events, 87.5% of the incident light is still carried by a path, 51% after 500 scattering events, and still 26% after 1000.

Recall that 

$$
\begin{aligned}
S(p_o, \omega_o, p_i, \omega_i) \approx (1 - F_r(\cos⁡{\theta_o})S_p(p_o, p_i)S_{\omega}(\omega_i)
\end{aligned}
$$

is an approximation that decouples the directional and spatial components, allowing us to turn the double integral into an iterated integral. This approximation also allows us to sample each of the components independently.

We can use the term $$(1 - F_r(\cos⁡{\theta_o})$$ of the separable approximation as the probability of evaluating the BSSRDF for a given ray, given that that term gives the fraction of light that gets transmitted.

$$S_p(p_o,p_i)$$ is sampled around $$p_o$$ using SeparableBSSRDF::Sample_p. We obtain a point of incidence or entry $$p_i$$ sampled from a distribution that characterizes how far light entering at $$p_i$$ travels underneath the surface before reemerging at $$p_o$$. Choosing the point of incidence $$p_i$$ around $$p_o$$ involves mapping this distribution onto the surface around $$p_o$$; this surface may be arbitrary, with macro and micro valleys, hills, etc. To simplify this challenging problem, the spatial factor $$S_p (p_o,p_i)$$ is approximated using a radial profile function $$S_r$$:

$$
\begin{aligned}
S_p(p_o, p_i) \approx S_r(\Vert p_o - p_i \Vert)
\end{aligned}
$$

Given $$p_o$$ and normal $$n_o$$, we assume that the surface is locally planar around them, but we want to choose $$p_i$$ on the actual surface (which may not be planar). Then we sample a polar coordinate: the polar angle $$\varphi$$ uniformly from $$[0,2\pi)$$ and the radius $$r$$ according to the radial scattering profile $$S_r$$. We use this polar coordinate to somehow cast a probe ray towards the surface; the intersection point will be the sampled point $$p_i$$.

This way of sampling has a number of things to be careful about:

The scattering radial profile $$S_r$$ isn't uniform across wavelengths. That is, the mean free path may vary across spectral channels. This is solved with MIS, by using tailored distributions: a different sampling technique per wavelength. Every Sample_Sp call will choose a single spectral channel/wavelength. Spectrum::nSamples counts the number of spectral channels or wavelengths.

Points where $$S$$ is high might be sampled with too low a probability, introducing variance. This is the case when $$n_o \cdot n_i \approx 0$$ (parallel or almost parallel) because then the perpendicular probe ray will graze the surface, etc... This is solved with MIS, by using tailored distributions: 3 different axes of projection corresponding to the basis vectors of shading space (one of them is $$n_o$$). When the local surface is indeed planar, it only makes sense to project along $$n_o$$ (perpendicular projection) since probe rays along the other 2 basis vectors will miss the local surface for sure (because they are parallel to the surface; parallel projections). Since it's less likely that the parallel projection probe rays will hit the surface, a perpendicular projection probe ray is used with 0.5 probability, and each of the parallel projection probe rays get 0.25 each (this is referred to as ray budget). Every Sample_Sp will choose a single axis of projection.

Radiance coming in through some point $$p_i$$ will fall of before reaching a given exit point $$p_o$$. We sample $$S_r$$ twice for a given $$p_o$$: first to determine the radius $$r$$ to obtain $$p_i$$ (through a probe ray) and then to obtain $$r_{max}$$, the maximum radius around $$p_o$$ within which the contained points of incidence $$p_i$$ can transmit radiance to $$p_o$$; radiance coming in through points $$p_i$$ beyond $$r_{max}$$ won't reach $$p_o$$. If $$r>r_{max}$$, we abort.

### Path tracing

Approximate the generalized scattering equation using Monte Carlo integration.

$$
\begin{aligned}
L_o(p_o, \omega_o) \approx \frac{S(p_o, \omega_o, p_i, \omega_i)(L_d(p_i, \omega_i)+L_i(p_i, \omega_i))\vert \cos{\theta_i} \vert}{p_1(p_i)p_2(\omega_i)}
\end{aligned}
$$

where $$L_d$$ is incident direct radiance and $$L_i$$ is incident indirect radiance.

To generate a sample $$(p_i, \omega_i)$$ to evaluate the Monte Carlo estimator using SeparableBSSRDF::Sample_S: first, Sample_Sp samples a point of incidence $$p_i$$ around $$p_o$$ using the radial scattering profile $$S_r$$; then, a SeparableBxDF is allocated and initialized at point $$p_i$$. This BxDF determines the directional distribution (component) of $$S$$ and is used to sample $$\omega_i$$. SeparableBxDF may be an arbitrary BxDF.

$$
\begin{aligned}
L_o(p_o, \omega_o) &\approx \frac{S(p_o, \omega_o, p_i, \omega_i)(L_d(p_i, \omega_i)+L_i(p_i, \omega_i))\vert \cos{\theta_i} \vert}{p_1(p_i)p_2(\omega_i)} \\
&= \frac{S(p_o, \omega_o, p_i, \omega_i)L_d(p_i, \omega_i)\cos{\theta_i}}{p_1(p_i)p_2(\omega_i)} + \frac{S(p_o, \omega_o, p_i, \omega_i)L_i(p_i, \omega_i)\vert \cos{\theta_i} \vert}{p_1(p_i)p_2(\omega_i)}
\end{aligned}
$$

one for direct incident radiance and one for indirect incident radiance.
