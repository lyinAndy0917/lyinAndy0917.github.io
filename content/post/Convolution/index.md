---
title: "Convolution"
description: "An introduction to convolution."
slug: "Control/Convolution"
categories:
    - ML and DL
---

## Convolution
- **What** is convolution?
  - Convolution is a mathematical operation that combines two signals to produce a third signal.

### Understanding:
- 卷积 
  - 何为“卷“： 一个信号在另一个信号上作反向平移
  - 何为“积“： 两个信号的乘积  

$$y(t) = \int_{-\infty}^{\infty} f(\tau)g(t-\tau)d\tau $$

### In terms of Matrix
1. Lets say we have a 3x3 matrix A as a kernel
2. And We have a 5x5 matrix B as an image
3. With this kernel, we can slide it over the image
4. At each position, we multiply the kernel and then you would get a new matrix C which has the dimension of 4x4
5. But now what about the edges? We can pad the image with zeros to make the output matrix the same size as the input matrix
6. This is called **zero-padding**


### Image processing
- f is the image
- g is the kernel
- y is the output image
- In this application, Convolution is like telling how the near points would have an effect on the current point
- In feature extraction, the kernel is the feature detector. It would compare the kernel with the image and by from the result you could tell how relevant the feature is to that part of the image.
  
