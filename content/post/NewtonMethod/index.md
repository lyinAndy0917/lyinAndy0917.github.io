---
title: "Newton's Method"
description: "An introduction to Newton's method."
slug: "Newton's Method"
categories:
    - Optimization
---

# Newton's Method

## Problem Setup
Given a problem:
Here we are going to solve some equality-constrained optimization problems with Newton's method. We are given a problem 

$$ \begin{align} \min_x \quad & f(x) \\ 
 \text{st} \quad & c(x) = 0
 \end{align}$$
 Which has the following Lagrangian:
 $$ \mathcal{L}(x,\lambda) = f(x) + \lambda ^T c(x), $$
and the following KKT conditions for optimality:
$$\begin{align}
\nabla_x \mathcal{L} = \nabla_x f(x) + \bigg[ \frac{\partial c}{\partial x}\bigg] ^T \lambda &= 0 \\ 
c(x) &= 0 
\end{align}$$

Which is just a root-finding problem. To solve this, we are going to solve for a $z = [x^T,\lambda]^T$ that satisfies these KKT conditions.

## Equality Constrained Optimization
### Full Newton's Method
Lets say we have a curve function $r(z)$ that we want to find the root of. We can use Newton's method to solve for when $r(z) = 0$. To do this, we specify `res_fx(z)` as $r(z)$, and `res_jac_fx(z)` as $\partial r/ \partial z$. So the decreasing process is described as follows:

$$
r(z_{k+1}) = r(z_k) + \bigg[ \frac{\partial r}{\partial z} \bigg]
$$

The problem with this is that when the f is approaching zero, the slope or gradient would become really small, leading it to be slow to converge.

So we introduce a new variable $\Delta z$ to the equation, and we have the following:

$$
r(z_{k+1}) = r(z_k) + \bigg[ \frac{\partial r}{\partial z} \bigg] \Delta z
$$

#### Newton's Method with a Linesearch

We use Newton's method to solve for when $r(z) = 0$. To do this, we specify `res_fx(z)` as $r(z)$, and `res_jac_fx(z)` as $\partial r/ \partial z$. To calculate a Newton step, we do the following:

$$\Delta z = -\bigg[ \frac{\partial r}{\partial z} \bigg]^{-1} r(z_k)$$

We then decide the step length with a linesearch that finds the largest $\alpha \leq 1$ such that the following is true:
$$ \phi(z_k + \alpha \Delta z) < \phi(z_k)$$
Where $\phi$ is a "merit function", or `merit_fx(z)` in the code. 

This is called the "Armijo rule", and it is a sufficient decrease condition.  
In my understanding, **the Armiho rule is guranteeing that the line is decreasing.**  

**Q:** What does $\alpha$ really represent? By by decreasing alpha, what is the effect on the line search?
**A:** Basically for newton method, in decreasing, we use the slope of that point to find the next point. So by decreasing alpha, we are decreasing the step size.

```julia
function linesearch(z::Vector, Δz::Vector, merit_fx::Function; max_ls_iters = 10)::Float64
    α = 1.0
    for i = 1:max_ls_iters
        if merit_fx(z + α * Δz) < merit_fx(z)
            return α
        end
        α /= 2.0
    end
    error("linesearch failed")
end
```
This ensure a sufficient decrease in the merit function. If the merit function is not decreasing, we would decrease the step size by half and try again. The function fails when no matter how small the step size is, we can't get a sufficient decrease.

Now we would throw the `linesearch` function into the `newton` function. The `newton` function would calculate the Newton step and then use the `linesearch` function to find the optimal step size.

```julia
function newtons_method(z0::Vector, res_fx::Function, res_jac_fx::Function, merit_fx::Function;
                        tol = 1e-10, max_iters = 50, verbose = false)::Vector{Vector{Float64}}
    # - z0, initial guess 
    # - res_fx, residual function 
    # - res_jac_fx, Jacobian of residual function wrt z 
    # - merit_fx, merit function for use in linesearch 
    
    # optional arguments 
    # - tol, tolerance for convergence. Return when norm(residual)<tol 
    # - max iter, max # of iterations 
    
    # return a vector of vectors containing the iterates 
    # the last vector in this vector of vectors should be the approx. solution 

    # return the history of guesses as a vector
    Z = [z0]  # Initialize with the initial guess
    for i = 1:max_iters
        r = res_fx(Z[end])
        norm_r = norm(r)
        if verbose
            println("iter: $i    |r|: $norm_r")
        end
        if norm_r < tol
            return Z
        end

        J = res_jac_fx(Z[end])
        Δz = -J \ r
        α = linesearch(Z[end], Δz, merit_fx, max_ls_iters = 10)
        Z = vcat(Z, [Z[end] + α * Δz]) # this ensure that it includes the new guess as it also satisfies the merit function
        if verbose
            println("α: $α")
        end
    end
    error("Newton's method did not converge")
end
```

Next we are going to use the newton method to solve the equality-constrained optimization problem. We are going to use the following functions:

First, let's define the cost function and the constraint function. 
```julia
function cost(x::Vector)
    Q = [1.65539  2.89376; 2.89376  6.51521];
    q = [2;-3]
    return 0.5*x'*Q*x + q'*x + exp(-1.3*x[1] + 0.3*x[2]^2)
end
function constraint(x::Vector)
    norm(x) - 0.5 
end
```
Second we would define the constraint Jacobian and the Lagrangian. 
```julia
function constraint_jacobian(x::Vector)::Matrix
    # since `constraint` returns a scalar value, ForwardDiff 
    # will only allow us to compute a gradient of this function 
    J = reshape(FD.gradient(constraint, x), 1, 2)
    #    J = FD.gradient(constraint, x)
    # In general, the gradient is length(x)×1 as a column vector or
    # 1×length(x) as a row vector—depending on how you need it shaped.
    # return reshape(J, 1, length(J)) could do this without loss of generality
    return J
end
```

Now we would work to implement the KKT conditions. 
```julia
function kkt_conditions(z::Vector)::Vector
    # TODO: return the KKT conditions
    x = z[1:2] # x is the first two elements of z
    λ = z[3] # λ is the third element of z
    # Since we are solving a equality-constrained optimization problem, the Z would be [x,λ]
    # TODO: return the stationarity condition for the cost function
    # and the primal feasibility
    grad_f = ForwardDiff.gradient(cost, x)
    grad_g = vec(constraint_jacobian(x))
    stationarity = grad_f + λ * grad_g
    feasibility = constraint(x)
    #error("kkt not implemented")
    return vcat(stationarity, feasibility) 
end
```
Because **those KKT conditions *are* your residual vector \(r(z)\).** In other words:

1. You have set up the problem of finding a point (x,$\lambda$) such that the  
   $$
    \begin{align}
    \nabla_x \mathcal{L} = \nabla_x f(x) + \bigg[ \frac{\partial c}{\partial x}\bigg] ^T \lambda &= 0 \\
    c(x) &= 0
    \end{align}
    $$

2. Newton’s method requires computing the Jacobian of 
   $$
    r(z) = \begin{bmatrix}
    \nabla_x f(x) + \bigg[ \frac{\partial c}{\partial x}\bigg] ^T \lambda \\
    c(x)
    \end{bmatrix} = 0
    $$
    Because in newton's method
    $$
    r(z_{k+1}) = r(z_k) + \bigg[ \frac{\partial r}{\partial z} \bigg] \Delta z \\
    \Delta z = -\bigg[ \frac{\partial r}{\partial z} \bigg]^{-1} r(z_k)
    $$
    So you could see we need the derivative of the residual function to compute the newton step $\Delta z$.

    

3. When you expand that derivative, you get exactly the “block matrix” form:
   $$\begin{bmatrix}
   \underbrace{\tfrac{\partial}{\partial x}\Bigl(\nabla f(x) + \Bigl[\tfrac{\partial c}{\partial x}(x)\Bigr]^T \,\lambda\Bigr)}_{\text{2×2 block, call it }A}
   &
   \underbrace{\tfrac{\partial}{\partial \lambda}\Bigl(\nabla f(x) + \Bigl[\tfrac{\partial c}{\partial x}(x)\Bigr]^T \,\lambda\Bigr)}_{\text{2×1 block, call it }b}
   \\[8pt]
   \underbrace{\tfrac{\partial}{\partial x}\,c(x)}_{\text{1×2 block, call it }c}
   &
   \underbrace{\tfrac{\partial}{\partial \lambda}\,c(x)}_{\text{(1×1) block, typically }0}
   \end{bmatrix}$$

Putting it all together:
```julia
function fn_kkt_jac(z::Vector)::Matrix
    # TODO: return full Newton Jacobian of kkt conditions wrt z
    x = z[1:2]
    λ = z[3]
    
    # TODO: return full Newton jacobian with a 1e-3 regularizer
    # to ensure the matrix is invertible
    # we need the A, b, c, d
    A = ForwardDiff.jacobian(z -> kkt_conditions(z)[1:2], z)
    b = ForwardDiff.jacobian(z -> kkt_conditions(z)[1:2], z)[3]
    c = ForwardDiff.jacobian(z -> kkt_conditions(z)[3], z)[1:2]
    d = ForwardDiff.jacobian(z -> kkt_conditions(z)[3], z)[3]
    J = vcat(hcat(A, b), hcat(c, d) + 1e-3 * I)
    return J

end
```

Now let's make a small modification to the `newtons_method` function to use the `gn_kkt_jac` function. 
```julia
function gn_kkt_jac(z::Vector)::Matrix
    # TODO: return Gauss-Newton Jacobian of kkt conditions wrt z 
    x = z[1:2]
    λ = z[3]
    
    # TODO: return Gauss-Newton jacobian with a 1e-3 regularizer
    #error("gn_kkt_jac not implemented")
    # Compute the Hessian of cost.
    H_f = FD.hessian(cost, x)
    
    # Compute the gradient of the constraint.
    grad_g = vec(constraint_jacobian(x))
    
    # For the Gauss-Newton approximation we use H_f alone.
    A = H_f      # instead of H_f + λ * H_g.
    b = grad_g
    c = grad_g'
    d = 0.0
    
    # Build the approximate Jacobian.
    J = [A   b;
         c   d]
    
    # Add a 1e-3 regularizer to the diagonal.
    J -= 1e-3 * I(3)
    
    return J 
end
```
When the constraints is almost satisfied, the Gauss-Newton method would be more efficient than the full Newton method.











