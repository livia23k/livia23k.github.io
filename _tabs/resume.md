---
# the default layout is 'page'
layout: post
title: "Resume"
toc: true
icon: fas fa-sailboat
order: 24
img_path: /assets/img/
---


<div style="height: 5px;"></div>

--- 
## Education

<div style="height: 20px;"></div>

<p style="text-align:left;"><b>Carnegie Mellon University</b><span style="float:right;">Pittsburgh, PA, U.S.</span></p>
<p style="text-align:left;">Master of Science in Electrical and Computer Engineering<span style="float:right;">Aug. 2023 - May 2025</span></p>

<p class="course-title">Courses:</p>

<div class="course-sub">
    <p class="course-sub-title">Downstream</p>
    <p class="course-sub-content">Foundations of Computer Systems (CS:APP), Computer Graphics, Introduction to Computer Security;</p>
</div>

<div class="course-sub">
    <p class="course-sub-title">Upstream</p>
    <p class="course-sub-content">Building Reliable Distributed System, How to Write Fast Code II;</p>
</div>

<div class="course-sub">
    <p class="course-sub-title">Others</p>
    <p class="course-sub-content">Introduction to Game Design;</p>
</div>

<div class="course-sub">
    <p class="course-sub-title">More Later</p>
    <p class="course-sub-content">Computer Game Programming, Real Time Graphics, Computer Vision for Engineers, Cloud Infrastructure and Servicess;</p>
</div>

<div style="height: 15px;"></div>

<p style="text-align:left;height:20px;"><b>Tiangong University</b><span style="float:right;">Tianjin, China</span></p>
<p style="text-align:left;height:20px;">Bachelor of Engineering in Computer Science and Technology<span style="float:right;">Aug. 2018 - Jun. 2022</span></p>

<p class="course-title">Courses:</p>


<div class="course-sub">
    <p class="course-sub-title">Fundamental</p>
    <p class="course-sub-content">Calculas, Linear Algebra, Probabilistic, Data Structure and Algorithms;</p>
</div>

<div class="course-sub">
    <p class="course-sub-title">System</p>
    <p class="course-sub-content">Principles of Computer Composition, Compile System, Operating System, Computer Networking, Database, Cloud and Distributed System;</p>
</div>

<div class="course-sub">
    <p class="course-sub-title">AI Related</p>
    <p class="course-sub-content">Machine Learning, Computer Vision, Digital Image Processing, Reinforcement Learning;</p>
</div>

<div style="height: 25px;"></div>

---

## Technical Skills

<div style="height: 20px;"></div>

[saving space]


<div style="height: 25px;"></div>

---

## Projects

#### 2024 Summer

<!-- ================================================================================================================================================ -->

<div style="height: 20px;"></div>

<div class="proj"> 
    <p class="proj-title">◼︎ Shadows&nbsp;&nbsp;
        <a href="{{site.baseurl}}/posts/games202-shadow/">[Link]</a> 
    </p>
</div>

<div class="proj-sub">
    <p class="proj-sub-title">Brief</p>
    <p class="proj-sub-content">Implemented the core functions for rendering Hard and Soft shadows based on already built Shadow Maps.</p>
</div>

<div class="proj-sub">
    <p class="proj-sub-title">Features</p>
    <p class="proj-sub-content">Shadow Maps, Percentage-Closer Filtering, Percentage-Closer Soft Shadows, Poission Disk Sampling.</p>
</div>

![](/post/2024-05-13-shadow/preview.png){: w="700px"}
_Preview: Render Result of GAMES202 Mary_

<!-- ================================================================================================================================================ -->

#### 2024 Spring

<div style="height: 20px;"></div>

<!-- ================================================================================================================================================ -->

<div class="proj">
    <p class="proj-title">◼︎ Mesh Edit&nbsp;&nbsp;
        <a href="{{site.baseurl}}/posts/15662-meshedit/">[Link]</a> 
    </p>
</div>

<div class="proj-sub">
    <p class="proj-sub-title">Brief</p>
    <p class="proj-sub-content">Implemented some local and global operations for model editting based on Halfedge data structure.</p>
</div>

<div class="proj-sub">
    <p class="proj-sub-title">Features</p>
    <p class="proj-sub-content">(Local Op) Edge Flip, Edge Split, Edge Collapse, Face Extrude; (Global Op) Triangulation, Linear Subdivision, Catmull-Clark Subdivision, Loop Subdivision.</p>
</div>

![](/post/2024-03-13-meshedit/meshpreview.png){: w="400px"}
_Preview: Editting Stanford Bunny_

<!-- ================================================================================================================================================ -->

<div class="proj">
    <p class="proj-title">◼︎ Software Rasterizer&nbsp;&nbsp;
        <a href="{{site.baseurl}}/posts/15662-rasterizer/">[Link]</a> 
    </p>
</div>

<div class="proj-sub">
    <p class="proj-sub-title">Brief</p>
    <p class="proj-sub-content">Implemented a complete rasterization graphics rendering pipeline, supporting offline rendering.</p>
</div>

<div class="proj-sub">
    <p class="proj-sub-title">Features</p>
    <p class="proj-sub-content">Model Display Mode (Wire/Flatten/Interpolation), Bresenham Line Algorithm, Depth test, Alpha Blend, Mipmap, Bilinear / Trilinear barycentric interpolation, MXAA.</p>
</div>

![](/post/2024-02-14-rasterizer/bunnie.png){: w="400px"}
_Preview: Render Result of Stanford Bunny_

<!-- ================================================================================================================================================ -->



<div style="height: 30px;"></div>

[saving space]

<div style="height: 30px;"></div>

<div style="height: 25px;"></div>

---

## Personality

<div style="height: 20px;"></div>

See &nbsp;<a href="{{site.baseurl}}/posts/personalities/">[Link]</a>&nbsp; for my detailed report.

&nbsp;

---


<style>
    .course-title {
        text-align:left;
        font-weight: bold;
        color:DimGray;
        font-size: 17px;
    }

    .course-sub {
        display:grid; 
        grid-template-columns: 120px auto;
        font-size: 14px;
        color:DimGray;
        gap: 10px;
    }

    .course-sub-title {
        margin-left: 30px;
        font-weight: 700;
    }

    .course-sub-content {
        align-items: center;
        justify-content: start;
        color:DimGray;
        font-size: 15px;
    }

    .proj-sub {
        display:grid; 
        grid-template-columns: 100px auto;
        font-size: 16px;
        gap: 10px;
    }

    .proj-title {
        font-weight: 700;
        font-size: 18px;
    }

    .proj-sub-title {
        margin-left: 30px;
        font-weight: 700;
    }

    .proj-sub-content {
        align-items: center;
        justify-content: start;
        color:DimGray;
        font-size: 15px;
    }

</style>