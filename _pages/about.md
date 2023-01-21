---
permalink: /
title: "Mingxin Zhao"
excerpt: "About me"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

Bio
======
I'm currently a senior software engineer in T-Head Semiconductor Corp and focusing on CPU simulator, compiler, and other toolchains targeting RISC-V architecture. Before this, I achieved my Ph.D. degree at Institute of Semiconductors (CAS), during which my research interests included machine vision on edge computing chips, deep neural network compression, and software/hardware co-design.

Internships
======
- 2021/05 - 2022/06, **Huawei 2012 Lab**, Technology Research Engineer (intern)
  - In 2012 lab, I worked on the auto-tensorization compiler techniques, aiming at compiling fine-grained user code to tensor-level hardware primitives via polyhedral modeling.
  - Additionally, I also worked on dynamic vision sensor (DVS) data processing. DVS has ultra-high dynamic range and frequency but only responses to motions in the view. These properties make it suitable for automotive scenarios with some dedicated optimizations.
- 2020/10 - 2020/07, **SenseTime Research**, Researcher (intern)
  - In SenseTime research, I worked on building a flexible deep learning model quantization framework for bridging various quantization algorithms and hardware backends.

Education
======
- 2017/09 - Present, Institute of Semiconductors (CAS), **Ph.D. Candidate**
  - In ISCAS, I was instructed by Prof. Nanjian Wu who leads a team developing vision chips. The vision chip targets ultra-low power near-sensor visual information processing. I cooperated with my colleagues to deploy convolutional neural networks on the vision chip by low-bitwith quantization and structured pruning.
  - The vision chip I worked on is a SIMD-style image processor controlled by a RISC scalar MPU. All processing instructions are handled by a PE array with 256 PEs. The adjacent pixels covered by a convolution kernel are processed by corresponding PEs. The adjacent PEs (typically 3 to 5) are, of course, have dedicated data paths to fit the accumulation requirement. This data flow is quite different from traditional **im2col** method as it directly consumes image data spatially without any layout reorder.   

- 2013/09 - 2017/07, University of Science and Technology of China, **Bachelor**
  - In USTC, I majored in Applied Physics.


