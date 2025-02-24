---
title: Neural Networks
description: "An introduction to neural networks."
slug: "NeuralNetwork"
categories:
    - Machine Learning
    - Optimization
---

# Neural Networks
- Regression
- Classification
  - could also be done with SVMs
  - could also be done with decision trees
  - could also be done with logistic regression

So what we had done before (SVMs, decision trees, logistic regression), these are basically a single layer neural network with one percetron.

The essential goal in neural networks is **optimize the error** by adjusting the weights and biases.

At the heart of neural network is the **perceptron.** A neural network is essentially a collection of perceptrons put in series and parallel. Noted that we do not draw the bias in the diagram.  

With a proper size of neural network, we can approximate any function.

**Example:**  
we have 3 features $x_1,x_2,x_3$ and we want to predict $y$, there for 3 weights $w_1,w_2,w_3$ and a bias $b$. Then we would have a activation function. 

XOR problem:
- $x_1,x_2$ are the inputs
- $y$ is the output
- if both inputs are the same, then the output is 1
- if the inputs are different, then the output is 0

## Perceptron
- The perceptron takes some inputs, weights them and fire a result(0/1).  
- It performs a dot product of the weights and the inputs, adds the bias, and then applies an activation function (such as a sigmoid function) to the result.
$$ y = f(a) = f(W \cdot X)$$





## Backpropagation
The key idea of neural network is to minimize a loss function through backpropagation.

