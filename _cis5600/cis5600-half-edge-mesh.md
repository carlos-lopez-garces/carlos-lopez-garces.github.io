---
layout: project
order: 2
date: '29-09-2023'
title: 'Half-edge meshes and Catmull-Clark subdivision'
caption: Catmull-Clark mesh subdivision, edge splitting, and face triangulation implemented on a half-edge data structure.
image: 
  path: /assets/img/projects/upenn/cis5600/subdivision.png
  srcset: 
    1920w: /assets/img/projects/upenn/cis5600/subdivision.png
    960w:  /assets/img/projects/upenn/cis5600/subdivision.png
    480w:  /assets/img/projects/upenn/cis5600/subdivision.png
sitemap: false
no_groups: true
---

In this project, we wrote code for importing .obj meshes and converting them to a [half-edge mesh data-structure](https://en.wikipedia.org/wiki/Doubly_connected_edge_list){:target="_blank"} that supports arbitrary edge splitting, face triangulation, and [Catmull-Clark subdivision](https://en.wikipedia.org/wiki/Catmull%E2%80%93Clark_subdivision_surface){:target="_blank"} (mesh smoothing).

A half-edge datastructure encodes mesh adjacency and connectivity, requires constant-size storage, enforces proper topology, and can be traversed starting from any arbitrary component (face, vertex, or half-edge).

The Catmull-Clark subdivision algorithm smoothes a half-edge mesh by inserting new vertices into the mesh and quadrangulating its faces. The result is a B-spline surface with the mesh's original vertices as control points.

{: style="text-align: justify;"}
![](/assets/img/projects/upenn/cis5600/subdivision_cube.gif)
{: style="text-align: center;"}
![](/assets/img/projects/upenn/cis5600/subdivision_cube_angle.gif)
{: style="text-align: center;"}
![](/assets/img/projects/upenn/cis5600/subdivision_dodecahedron.gif)
{: style="text-align: center;"}