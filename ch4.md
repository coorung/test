# Chapter 4. State-Space Solutions and Realizations

## 4.1 Introduction

- 다음과 같이 유리수로 된 Transfer function으로 되어있는 시스템을 생각해보자. 
$$\hat{g}(s)=\frac{s^2-2s+10}{(s+1)(s+2)(s+3)}=\frac{s^2-2s+10}{s^3+6s^2+11s+6}$$
- 2장에선 여기에 MATLAB으로 *n = [ 1 -2 10 ] ; d =[ 1 6 11 6 ] ; step(n, d)*를 입력하면 이 시스템의 Step response를 구할 수 있었다. 하지만 비록 우리가 이 Transfer function의 coefficient들을 입력해줬어도 프로그램 안에서 실제 이 response에 대한 연산은 $\hat{y}(s)=\hat{g}(s)\hat{u}(s)$로부터 계산되지 않는다.

- Transfer function, $\hat{y}(s)=\hat{g}(s)\hat{u}(s)$ 에서 입력 $\hat{u}(s)$가 많은 실제의 상황처럼 유리수가 아닐 경우에는 이 Equation으로 response를 분석적으로 계산할 수 없다.

- $\hat{y}(s)=\hat{g}(s)\hat{u}(s)$를 풀기 위해선 partial fraction expansion과 table lookup을 수행하는 $\hat{g}(s)$의 poles를 계산하는 것이 필요하다.

- State-space equation을 세우기 위해 Transfer function을 사용하는 것은 컴퓨팅 상으로 거의 불가능하다. parameter들의 아주 조그마한 variation만으로도 roots가 크게 바뀌기 때문이다. 

- Trasfer function으로 어떻게 state-space equations를 세울지에 관한 문제를 Realization Problem이라 하는데, 이번 장에서 우리는 이 방법으로 디지털적으로 계산하는 방법과 혹은 op-amp circuit을 사용하는 방법을 배우게 될 것이다.

---
## 4.2 Getneral Solution of CT LTI State-Space Equations

- continuous-time(CT)에서의 linear time-invariant(LTI) state-space equation을 생각해보자.
$$\dot\mathbf x(t) = \mathbf A \mathbf x(t) + \mathbf B \mathbf u(t)\qquad\qquad (4.2)$$
$$\mathbf y(t) = \mathbf C \mathbf x(t) + \mathbf D \mathbf u(t) \qquad\qquad (4.3)$$
$$where\; \mathbf{A, B, C}, and\; \mathbf {D} \text{는 각각 (n x n), (n x p), (q x n), (q x p)의 상수 행렬}$$

- 여기서의 문제는 $t\ge0$에서 초기 조건 $\mathbf x(0)$와 input $\mathbf u(t)$가  주어졌을 때의 solution을 찾는 것이다. 그리고 이 solution은 전적으로 Section 3.6에서 본 $\mathbf A$의 exponential function에 달려있다.

- 특히 solution을 찾기 위해선 (3.55)의 성질이 필요하다. 
$$\frac{d}{dt}e^{\mathbf{A}t}=\mathbf A e^{\mathbf{A}t}=e^{\mathbf{A}t}\mathbf A\qquad\qquad (3.55)$$
  

(4.2)의 양변에 $e^{-\mathbf A t}$를 곱해보면,
$$e^{-\mathbf A t}\dot{\mathbf x}(t) - e^{-\mathbf A t}\mathbf{Ax}(t)=e^{-\mathbf A t}\mathbf{Bu}(t)$$
이고 이는 
$$\frac{d}{dt}\left(e^{-\mathbf A t}\mathbf x (t) \right)=e^{-\mathbf A t}\mathbf{Bu}(t)$$
를 의미한다.  
0부터 t까지 이것들을 적분하면
$$e^{-\mathbf A t}\mathbf x (t)\bigg |_{\tau=0}^{t}=\int_{0}^{t}e^{-\mathbf{A}\tau}\mathbf{Bu}(\tau)d\tau$$
가 나온다.  
그러므로 우리는
$$e^{-\mathbf{A}t}\mathbf{x}(t)-e^0\mathbf{x}(0)=\int_{0}^{t}e^{-\mathbf{A}\tau}\mathbf{Bu}(\tau)d\tau \qquad\qquad (4.4)$$
를 갖게 된다.
(3.54)와 (3.52)에서 보았듯이 $e^{-\mathbf{A}t}$의 역수가 $e^{\mathbf{A}t}$이고, $e^0=\mathbf{I}$이므로 (4.4)는 곧
$$\mathbf{x}(t)= e^{\mathbf{A}t} \mathbf{x}(0) \int_{0}^{t}e^{\mathbf{A}(t-\tau)} \mathbf{Bu}(\tau) d\tau \qquad\qquad (4.5)$$
를 의미하고 이것이 (4.2)의 solution이 된다.

정말 그렇게 되는지 검증해보자.  (4.5)가 (4.2)의 solution이 되기 위해선,
1) 초기조건 t = 0 에서의 $\mathbf{x}(t)=\mathbf{x}(0)$를 만족해야 한다.
2) (4.5)가 (4.2)를 만족해야 한다.

>### 1. 초기조건 만족여부  

실제로 t = 0 에서 (4.5)는 다음과 같이
$$\mathbf{x}(0)=e^{\mathbf{A} \cdot 0}\mathbf{x}(0)=e^{0}\mathbf{x}(0)=\mathbf{I}\mathbf{x}(0)=\mathbf{x}(0)$$
이 되므로 초기조건을 만족한다.  

>### 2. (4.2)를 만족하는 지 여부  

(4.5)가 (4.2)를 만족하는지 보여주기 위해선 다음과 같은 equation이 필요하다.

$$\frac{\partial}{\partial t} \int_{t_0}^{t} f(t,\tau)d\tau = \int_{t_0}^{t}\bigg ( \frac{\partial}{\partial t} f(t,\tau) d\tau \bigg ) + f(t,\tau) \big |_{\tau=t} \qquad\qquad (4.6)$$

(4.5)를 미분하고 중간과정에 (4.6)을 사용하면,

$$
\begin{array}{lcl}
\dot{\mathbf{x}}(t) & = & \frac{d}{dt}\bigg[e^{\mathbf{A}(t)}\mathbf{x}(0)+\int_{0}^{t}e^{\mathbf{A}(t-\tau)}\mathbf{Bu}(\tau)d\tau\bigg] \\ \\
& = & \mathbf{A}e^{\mathbf{A}t}\mathbf{x}(0) + \int_{0}^{t}\mathbf{A}e^{\mathbf{A}(t-\tau)}\mathbf{Bu}(\tau)d\tau + e^{\mathbf{A}(t-\tau)}\mathbf{Bu}(\tau)\bigg|_{\tau=t} \\ \\
& = & \mathbf{A}\bigg[e^{\mathbf{A}t}\mathbf{x}(0) + \int_{0}^{t}e^{\mathbf{A}(t-\tau)}\mathbf{Bu}(\tau)d\tau\bigg]+e^{\mathbf{A}\cdot0}\mathbf{Bu}(t)
\end{array}
$$
가 되고 (4.5)를 대입하면
$$\dot\mathbf x(t) = \mathbf A \mathbf x(t) + \mathbf B \mathbf u(t)$$
가 나오는 걸 볼 수 있으므로 (4.5)가 (4.2)의 solution이 됨을 볼 수 있다.

이에 따라 (4.3)의 solution도 (4.5)를 (4.3)에 대입함에 따라 다음과 같이 산출된다.
$$\mathbf{y}(t)=\mathbf{C}e^{\mathbf{A}t}\mathbf{x}(0) + \mathbf{C}\int_{0}^{t}e^{\mathbf{A}(t-\tau)}\mathbf{Bu}(\tau)d\tau + \mathbf{Du}(t) \qquad\qquad (4.7)$$