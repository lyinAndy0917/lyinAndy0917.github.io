---
title: "Discretization"
description: "An introduction to discretization."
slug: "Discretization"
categories:
    - Control
---

# Discretization

## Motivation
- **Q:** Why do we need to discretize a continuous system?
- **A:** Because computers are discrete. They can only process discrete data.
- Discretized systems have some benefits that continuous systems don't have:
  - **Robustness:** Discretized systems are more robust to noise and disturbances.

## Explicit Form
$X_{k+1} = f(X_k, U_k)$ 
**Q:** Why is it called explicit form?  
**A:** Because the next state is **explicitly determined by the current state** and the control input. 
  

$f$ is the system dynamics.

### Methods    
#### Simplest Discretization - Forward Euler

$X_{k+1} = X_k + h \cdot f(X_k, U_k)$
$$
h = \text{time step}
$$
- This method takes the slope at the current point and uses it to predict the next point. This method always overshoots.

- This method is so bad because this method adds energy to the system. The energy is not conserved. Should not use this method.

#### Runge-Kutta Method
In most case, we use the Runge-Kutta method to discretize the system.
```python
def runge_kutta(f, x, u, h):
    k1 = f(x, u)
    k2 = f(x + h/2 * k1, u)
    k3 = f(x + h/2 * k2, u)
    k4 = f(x + h * k3, u)
    return x + h/6 * (k1 + 2*k2 + 2*k3 + k4)
```

## Implicit Form
**Q:** Why is it called implicit form?  
**A:** Because the next state is implicitly determined by the current state and the control input. The equation is not directly solvable, and it would contain the next state.

$f_{explicit}(X_{k+1}, X_k, U_k) = 0$  
Since the equation is not directly solvable, we need to use numerical methods to solve it.
Q: How do we know if we have come to a solution?
A: We would define a residual function. If the residual function is zero (or very close to zero), we have come to a solution.

### Methods
#### Backward Euler 

$$residual = x_k + \Delta t \cdot \dot{x}_{k+1} - x_{k+1} = 0 \quad \quad \text{Backward Euler}$$  
Q: How is this different from the forward Euler?
A: In forward euler, we have the dynamics $f(x_k, u_k)$ at the current time step. In backward euler, we would make use of the dynamics $f(x_{k+1}, u_k)$ at the next time step.

In this method, the error is always proportional to the time step.

Q: Why is this method better than the forward Euler?
A: While the forward Euler method always **overshoots**, the backward Euler method always **undershoots**. The backward Euler method is more stable than the forward Euler method.

```juila
function implicit_integrator_solve(params::NamedTuple, dynamics::Function, implicit_integrator::Function, x1::Vector, dt::Real;tol = 1e-13, max_iters = 10)::Vector

    # initialize guess
    x2 = 1*x1

    for i = 1:max_iters
        residual = implicit_integrator(params, dynamics, x1, x2, dt)
        
        
        # TODO: return x2 when the norm of the residual is below tol 
        if norm(residual) < tol
            return x2
        end
        # Newton's method
        J = FD.jacobian(x -> implicit_integrator(params, dynamics, x1, x, dt), x2)
        x2 = x2 - J\residual
    end
    error("implicit integrator solve failed")
end    
```
**Explanation:**
1. This function takes in the parameters, the dynamics, the implicit integrator (backward euler, RK4), the initial state, the time step, the tolerance, and the maximum number of iterations.
   
    **Q:** What does the maximum number of iterations affects?  
    **A:** In my opinion, with a easy solution, the result would converge quickly. However, with a more complex system, the result would take more iterations to converge.

2. The function initializes the guess of the next state `x2` to be the same as the current state `x1`.

3. At each iteration, it would check the residual of the implicit integrator. If the residual is below the tolerance, it would return the next state `x2`. And this is the solution of that particular time step.

4. Quick Recap on Newton's Method:
    - Newton's method is an iterative method to find the root of a function. It uses the tangent line to approximate the root of the function. The tangent line is the derivative of the function.
    - The formula is $x_{n+1} = x_n - \frac{f(x_n)}{f'(x_n)}$
    - The method would converge to the root if the initial guess is close to the root and the function is well-behaved.
    - Potential Problems
      - you can't control how far the convergence moves at each time step because it only depends on the function.
      - To help with this, could ultilize the line search method to find the optimal step size.
      $$ x_{n+1} = x_n - \alpha \frac{f(x_n)}{f'(x_n)}$$
       - The $\alpha$ is the step size. It could be found by the line search method.

### Simulation
To simulate the whole system/curve.
```julia
function simulate_implicit(params::NamedTuple,dynamics::Function,implicit_integrator::Function,x0::Vector,dt::Real,tf::Real; tol = 1e-13)
    t_vec = 0:dt:tf
    N = length(t_vec)
    X = [zeros(length(x0)) for i = 1:N]
    X[1] = x0
    
    # TODO: do a forward simulation with the selected implicit integrator 
    # hint: use your `implicit_integrator_solve` function
    for i in 2:N
        X[i] = implicit_integrator_solve(params, dynamics, implicit_integrator, X[i-1], dt)
    end
    return X
end
```
Now this would simulate each point in the curve. The `implicit_integrator_solve` function would be used to solve the implicit equation at each time step. 
**Keep in mind this is still discrete.**


