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

In general, what happens in the hiddent neurons is not interpretable and the only thing we know is that they are trying minimize the error.

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

$$ a_{1,1} = \sum_{i=1}^{n} w_{1,i}x_{0,i}$$
It's simply a dot product of the weights and the inputs.


## Forward Propagation
**Process:**
$$
\begin{align}
   \vec{a_1} &= \vec{W_1} \cdot \vec{x_0} \\
   \vec{x_1} &= f(\vec{a_1}) \\
   \vec{a_2} &= \vec{W_2} \cdot \vec{x_1} \\
   \vec{x_2} &= f(\vec{a_2}) = f(\vec{W_2} \cdot \vec{x_1}) \\
    \vec{y} &= f(\vec{W_3} \cdot \vec{x_2}) \\
\end{align}
$$
where $f$ is the activation function.
The chaining of perceptrons is constructed by this chainning effect (chain rule).

At the end, you would manually calculate the error and adjust the weights and biases so that the result best matches the ground truth.

## Backpropagation
The key idea of neural network is to minimize a loss function through backpropagation.

## Softmax
For a classification problem, we would use the softmax function to convert the output to a probability distribution.
$$ \text{softmax}(x_i) = \frac{e^{x_i}}{\sum_{j=1}^{n} e^{x_j}}$$

## Define a neural network in PyTorch
```python
import torch
import torch.nn as nn
class MLP(nn.Module):
    def __init__(self):
        super(MLP, self).__init__()
        self.layers = nn.Sequential(
            nn.Linear(3, 4),
            nn.ReLU(),
            nn.Linear(4, 2),
            nn.ReLU(),
            nn.Linear(2, 1)
        )
    def forward(self, x):
        return self.layers(x)

model = MLP()
```
Even though in the code the bias term is not presented explicitly, it is still there by default (taken into account).

## For example
$$
f(x) = \frac{1}{1+e^{-k \cdot x}}
$$
where $k$ determines the shape or slope of the sigmoid function.

## Activation Functions
- Sigmoid
- Tanh
- Step
- Softplus
- swish
- ReLU
- sinc
- softsign


- Linear (identity): it takes the input and returns the input. This is basically no activation function.
- Step: Squashes the input to 0 or 1. It is not used in hidden layers.

### Sigmod
$$ f(x) = \frac{1}{1+e^{-x}}$$
$$ f'(x) = f(x)(1-f(x))$$

When the gradient goes to zero, it becomes problematic. This is called the **vanishing gradient problem.**  
This is Undesirable for hidden layers.**WHY?**

Why do these gradients matter?
- The only thing we trying to find is the weights and biases that minimize the error.
- weak gradient slows the learning process.
- A zero gradient would kill a neuron.

$$ \frac{\partial y}{\partial x} = \frac{\partial y}{\partial z} \frac{\partial z}{\partial x}$$


### Tanh
$$ f(x) = \frac{e^x - e^{-x}}{e^x + e^{-x}}$$
$$ f'(x) = 1 - f(x)^2$$
Data is centered around 0. It is zero centered. It is better than the sigmoid function. Less "positive" and "negative" bias.
Undesired for middle layers.


**ResNet:** Residual Network
- It uses skip connections to avoid the vanishing gradient problem.

Q:
- Positive Bias
- zero centered
- why gradient matters
- hwo does gradient contribute to the result
- the chain rule?
- What does the activation function do?



## ReLu
- Nowadays, you want to always start with ReLU. It is the most popular activation function.
- But what if the input is negative? The gradient is zero. A solution is to use the leaky ReLU.
- We are keep the negative part have a minimal slope but not zero.
- In addition to that, instead of having the slope in the negative part to be a constant, we can make it a learnable parameter. This is called the Parametric ReLU.

