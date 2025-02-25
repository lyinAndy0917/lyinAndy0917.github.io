---
title: "Model Predictive Control"
description: "An introduction to MPC."
slug: "MPC"
categories:
    - Control
---

# Model Predictive Control

While LQR works really well with a good model, but in reality there are a couple of things that would prevent us from getting good results:
1. imperfect state estimation (sensors are noisy)
2. unmodeled dynamics (What if we don't know the dynamics of the system?)
3. misalignment between the model and the real system (What if the model is not perfect?)
4. actuator constraints
Q: what is an actuator?  
A: An actuator is a component of a machine that is responsible for moving and controlling a mechanism or system, for example by opening a valve. In simple terms, it is a "mover". 

All of these above leads to a "Sim to Real" gap.

## Process
$$ J = \sum_{i=0}^{N-1} (x(k+i|k) - x_{ref}(k+i))^T Q (x(k+i|k) - x_{ref}(k+i)) + u(k+i|k)^T R u(k+i|k) + (x(k+N|k) - x_{ref}(k+N))^T Q_f (x(k+N|k) - x_{ref}(k+N)) $$
1. measure the state X at time k.
2. predict the state X at time k+1, k+2, ..., k+N.
3. calculate the control input U at time k, k+1, ..., k+N-1.
   - $$ x(k+1|k) = Ax(k|k) + Bu(k|k) $$
   - $$ x(k+2|k) = Ax(k+1|k) + Bu(k+1|k) $$
   - $$ x(k+3|k) = Ax(k+2|k) + Bu(k+2|k) $$
   - $$ X_k = [I, A, A^2, ..., A^N] X_0 + [B, AB, A^2B, ..., A^{N-1}B] U_k    \\ = M X_k + C U_k $$
4. Minimize the cost function J in the horzion (k, k+1, ..., k+N).
5. $$ J = X_k^T Q X_k + U_k^T R U_k + X_{N|k}^T Q_f X_{N|k} $$
   - From here, we still have two unknowns: X_k and U_k.
   - Plug in 
    $$ X_K = M X_k + C U_k $$  
    M and C are known based on A and B.  
    At the end we would have
    $$ J = X_K^T G X_K + 2U_k^T E U_k + U_k^T H U_k $$
    The first term is the initial state and the rest is quadratic programming.
    $$
    G = M^T Q M\\
    E = M^T Q C\\
    H = C^T Q C + R\\
    Q = [Q, 0, ..., 0; 0, Q, ..., 0; ..., 0, 0, Q_f]\\
    R = [R, 0, ..., 0; 0, R, ..., 0; ..., 0, 0, R]\\
    $$
6. Solve the optimization problem.

```matlab
% MPC
clear all;
close all;
clc;

pkg load control,optim;

% System dynamics
A = [1 0.1; 0 2];
n = size(A,1);

B = [0; 0.5];
p = size(B,2);

% Cost function
Q = eye(n);
Qf = Q;
R = eye(p);


k = 100; %simulation time
X_K = zeros(n,k);
X_K(:,1) = [1; 1];
U_K = zeros(p,k);

% Prediction horizon
N = 10;
[E,H] = MPC_cost(A,B,Q,Qf,R,N)

for k = 1:k
    % Solve the optimization problem
    U_K(:,k) = prediction(X_K(:,k), E, H, N,p);
    X_K(:,k+1) = A*X_K(:,k) + B*U_K(:,k);
end




```
  





