---
title: "LQR Control"
description: "An introduction to LQR control theory."
slug: "Control/LQR"
---

It's a state feedback just like pole placement, but it's optimal. It's optimal because it minimizes a cost function. 
**why**
- **Linear:** because the system is linear (which means needs to be linearized)
- **Quadratic:** because the cost function is quadratic
- **Regulator:** because the goal is to stabilize the system

# Finite Horizon LQR
- Why "**finite horizon**"? **A:** because the cost function is defined over a **finite time horizon**
- The goal is to reach a desired goal $x_g$ in finite time
- When the desired goal is constant, the controller is a regulator
- When the desired goal is time-varying, the controller is a tracker
- In regulation problems, **feedback** is enough to stabilize the system
- In tracking problems, **feedforward** is needed to stabilize the system
- The **cost (cost-to-go)** function is the sum of the state and control cost

$$
J = \frac{1}{2} \sum_{k=0}^{N-1} [(x_k - x_g)^T Q (x_k - x_g) + u_k^T R u_k] + \frac{1}{2} (x_N - x_g)^T Q_f (x_N - x_g)
$$
$J$: cost function  
$N$: time horizon   
$Q$: state cost matrix  
$R$: control cost matrix$  
$Q_f$: terminal state cost matrix   
$u_k$: control at time k  
$x_k$: state at time k  
$x_g$: desired goal     



## Dynamic Programming
Known
- The system dynamics A, B
- The cost function J
- The terminal cost $Q_f$
- The time horizon N

### Bruteforce
- Compute the cost-to-go for all possible states and controls
- Choose the control that minimizes the cost-to-go = optimize the cost function
- **Computational complex** needs a lot of tuning
### Learning
- gradient descent the cost function
- Flexibility in the cost function


### Riccati
- Riccati is used to compute the optimal feedback gain matrix $K$
1. introduce P. what is this P? P is solution to the Riccati equation, it's added to simplify the cost function 
2. now $J = x_0^T P x_0 - x_0^T P x_0 + \int_0^N (x^T Q x + u^T R u) dt$
3. By doing a trick called **completing the square**, now we have 
  $$
    J = x_0^T P x_0 + \int_0^N (x^T(A^T P + PA + Q - PBR^{-1}B^T P)x + (u+R^{-1}B^T P x)^T R (u+R^{-1}B^T P x)) dt
    $$
4. so now it's easy to see that the optimal control is $u = -R^{-1}B^T P x$
5. Since $u = -Kx$, we can find K by $K = R^{-1}B^T P$
6. Now we can find P by solving the Riccati equation
7. The Riccati equation is $A^T P + PA - PBR^{-1}B^T P + Q = 0$  
   $P = Q + A^T P A - A^T P (A-BK)$
8. Since this is a quadratic equation, there would be two possible solutions, one is stable and the other is unstable
   The stable solution is the one we are looking for.

**PS:** or we could take the derivative of the cost function with respect to the control and set it to zero, then we can find the optimal control
$$
\frac{\partial J}{\partial u} = 0  \\
u^* = -[R+B^TPB]^{-1}B^TPA
$$
take the hessian of the cost function with respect to the control and set it to positive definite, then we can find the optimal control
$$
R + B^TPB > 0
$$
global minimum

**How it actually works**
```julia
P = zeros(n,n,N)
K = zeros(m,n,N-1)

P[:,:,N] .= Qn

# Backward Riccati recursion
for k = (N-1):-1:1
    K[:,:,k] .= (R + B'*P[:,:,k+1]*B)\(B'*P[:,:,k+1]*A)
    P[:,:,k] .= Q + A'*P[:,:,k+1]*(A-B*K[:,:,k])
end
```
1. Starting from the last time step, compute the optimal feedback gain matrix K and the solution to the Riccati equation P
