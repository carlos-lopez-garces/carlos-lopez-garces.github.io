---
layout: project
order: 3
date: '01-11-2023'
title: 'Skeletons and mesh skinning'
caption: Skeleton joint control and mesh skinning.
image: 
  path: /assets/img/projects/upenn/cis5600/skinning.png
  srcset: 
    1920w: /assets/img/projects/upenn/cis5600/skinning.png
    960w:  /assets/img/projects/upenn/cis5600/skinning.png
    480w:  /assets/img/projects/upenn/cis5600/skinning.png
sitemap: false
no_groups: true
---

In this project, I wrote a hierarchical skeleton data structure of joints, bound it to a half-edge mesh, and skinned it using linear blend skinning in OpenGL shaders.

Each of the joints has a local frame of reference that orients and positions it with respect to its parent joint, and a global frame of reference with respect to the world (a homogeneous matrix that results from pre-multiplying all the local matrices up to the root joint). Transforming a given joint thus transforms all of its descendants relative to world space.

At bind time (when pressing the Skin Mesh button), the position and orientation of the skeleton with respect to the world is established. A ***bind matrix*** is computed for each joint that transforms vectors from the world coordinate system to each joint's coordinate system; this matrix is the inverse of the joint's global frame of reference matrix.

At the same time, 2 joints are associated with each mesh vertex based on proximity. These 2 joints will ***influence*** the position and orientation of all the vertices associated with them, so that when the joints are translated or rotated, the influenced vertices move too. The level of influence of each of the 2 joints over a vertex is directly proportional to their distance to that vertex and takes the form of a weight.

In the vertex shader, the position of the vertex is transformed to bind space (the joint's local coordinate system) using each of the 2 joints' bind matrices, and then transformed back to world space using each joint's global transformation. The position of the vertex in world space will be the ***linear combination of the 2 joints' positions***, where their influence weights are the coefficients. This is known as linear blend skinning.

{: style="text-align: center;"}
![](/assets/img/projects/upenn/cis5600/skinning.gif)

Linear blend skinning is not the ideal method, but it at least keeps the mesh connected (having a vertex be influenced by a single joint results in mesh breakages). One of its shortcomings is that it doesn't preserve volume.

{: style="text-align: center;"}
***Candy wrapper effect.***

{: style="text-align: center;"}
![](/assets/img/projects/upenn/cis5600/skinning-candy-wrapper.png)