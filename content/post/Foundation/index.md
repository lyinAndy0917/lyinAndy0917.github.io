---
title: Foundation
description: An introduction to the foundation of control theory.
categories:
    - Control
---

## Controllability
## Observability

## Stability

### with a control gain K, how to check stability?
- **Eigenvalues** of the closed-loop system
1. get the closed-loop system matrix $A_{cl} = A - BK$
2. check the eigenvalues of $A_{cl}$
   - if the system is in **continuous time**, check the real part of the eigenvalues. If all the real parts are negative, the system is stable.
   - if the system is in **discrete time**, check the magnitude of the eigenvalues. If all the magnitudes are less than 1, the system is stable.

- **Lyapunov** function
1. find a positive definite function V(x) that satisfies the following conditions
   - $V(0) = 0$
   - $V(x) > 0$ for all $x \neq 0$
   - $\dot{V}(x) < 0$ for all $x \neq 0$
2. if such a function exists, the system is stable
- **Routh-Hurwitz** criterion
1. check the coefficients of the characteristic polynomial. Characteristic polynomial is the determinant of $sI - A_{cl}$
2. if all the coefficients are positive, the system is stable
3. if any of the coefficients are negative, the system is unstable

- **BIBO** stability
1. check the transfer function of the system
2. if the transfer function is stable, the system is stable. The transfer function is stable if the poles are in the left half of the complex plane

- **Additional Information**
what is the difference between Lyapunove stable, BIBO stable, norm stable, and asymptotically stable?
1. **Lyapunov** stable: the system is stable if the system is bounded.
2. **BIBO** stable: the system is stable if the input is bounded
3. **norm** stable: the system is stable if the norm of the system is bounded
4. **asymptotically** stable: the system is stable if the system converges to a point
5. what does bounded mean? it means the system does not go to infinity.


