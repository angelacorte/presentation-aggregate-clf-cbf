+++

title = "Integration of Control Lyapunov and Control Barrier Functions for Safety-Critical Guarantees in Aggregate Computing"
description = "Presentation slides for talk on CLF and CBF in Aggregate Computing."
outputs = ["Reveal"]
[params.reveal_hugo.katex]
enable = true
[params.reveal_hugo]
custom_theme = "custom-theme-light.scss"
custom_theme_compile = true

+++

# Integration of Control Lyapunov and Control Barrier Functions for **Safety-Critical Guarantees** in Aggregate Computing

### [**Angela Cortecchia**](mailto:angela.cortecchia@unibo.it)

<div style="text-align: center; width: 100%;">
<img src="example-background.svg" style="width: 20%" />
</div>

---

# How will the drones avoid the obstacle?

![Drones in formation](./images/drone_formation.svg)

---

# Current approach

![Drones avoiding an obstacle](./images/drones_formation.png)

{{% fragment %}}
Potential issues:
- *eventual* consistency;
- loss of formation.
{{% /fragment %}}

---

# Eventual consistency

How much time will it take to re-form the formation?

![Drones losing formation](./images/drones_eventual_consistency.png)

---

# Eventual consistency

Potential issues on the transient behavior

![eventual consistency](./images/eventual_consistency.mov)

---

# Possible consequences of losing the formation

![Drones losing connection](images/drones_changing-formation.png)

- Lost connection
- New leaders
- Sub-formations going in different directions

---

# How we would like it to behave

![Drones avoiding obstacle while keeping formation](./images/drones_avoiding_formation.png)

Did not lose formation, neither connection, avoided the obstacle safely and keep going towards their goal.

---

# Our goal

Ensure guarantees on the transient behavior of the system, not only on the eventual one.

---

# How can we achieve it?

In control theory, there exist formal methods to specify both stability and safety conditions:
- *Control Lyapunov Functions* (CLF) for **fast convergence** and **stability**;
- *Control Barrier Functions* (CBF) for **safety** in the transient behavior.

---

# Preliminaries: **Control Theory**

*Control Theory* is a branch of engineering and mathematics that deals with the behavior of dynamical systems with inputs (*controls*). \
The main goal is to develop *control strategies* that *modify the system's behavior to achieve a desired state*,
while minimizing delays or errors, while ensuring safety and control stability.  

Control can only be applied with respect to the system’s *temporal evolution*.

A *Control Loop* is a feedback-driven mechanism that measures the current state of a system, 
*compares* it to a desired set-point, 
and *automatically adjusts the control input* to minimize the error between the two.

---

# Preliminaries: **Open and closed loop controls**

An *automatic control system* can operate in two ways: as an *open-loop* control or as a *feedback (closed-loop) control*.

{{% multicol %}}
{{% col %}}

#### Open-Loop Control

The control *input* is determined *without considering the current state* of the system. \
It relies on predefined control actions based on a model of the system.

{{% /col %}}
{{% col %}}
#### Feedback / Closed-Loop Control

The control input is *continuously adjusted* based on the current state of the system. \
It uses feedback from the system to correct deviations from the desired state.

{{% /col %}}
{{% /multicol %}}

{{% multicol %}}
{{% col %}}

\
\
![Open-loop control](./images/open-loop.png)

{{% /col %}}
{{% col %}}

![Closed-loop control](./images/closed-loop.png)

{{% /col %}}
{{% /multicol %}}

---

# Preliminaries: **Lyapunov Theory**

{{% multicol %}}
{{% col %}}

*Lyapunov Theory* provides tools to analyze the *stability* property of dynamical systems. \
An autonomous dynamical system without a control input is described by the equation \
$\dot{x} = f(x)$

Starting from an initial state $x_0$, 
there exist some trajectory from there, and we want to verify whether the system converges to a desired equilibrium point $x_e$.

{{% /col %}}
{{% col %}}

![trajectory](./images/trajectory-function.png)

{{% /col %}}
{{% /multicol %}}

---

# Preliminaries: **Lyapunov Theory**

{{< multicol >}}
{{< col class="col-7">}}

If we can devise a Lyapunov function $V(x)$ that satisfies: 
<ul>
    <li>$s.t. V(x_e) = 0, V(x) > 0 for x \neq x_e$,</li>
    <li>$\dot{V}(x) = \frac{\partial V}{\partial x} f(x) < 0$ for $x \neq x_e$,</li>
</ul>

<p class="fragment" data-fragment-index="1">
The evolution of the Lyapunov function over time will decrease towards $x_e$. </br>
This implies that the system is stable and will converge to the desired equilibrium point.
</p>

<p class="fragment" data-fragment-index="2">
Every positive level set of the Lyapunov function is an <em>invariant set</em>
{{< tab times="3">}}$\Omega = \left\{ x \mid V(x) \le c \right\}$. </br>
If you start within that set, your trajectory will remain inside it for all future time.
</p>

{{< /col >}}
{{< col >}}

<div class="r-stack">
  <img
    class="current-visible"
    src="images/trajectory-function02.png"
  />
  <img
    class="fragment current-visible"
    data-fragment-index="1"
    src="images/trajectory-function03.png"
  />
    <img
    class="fragment current-visible"
    data-fragment-index="2"
    src="images/lyapunov-function.png"
  />
</div>

{{< /col >}}
{{< /multicol >}}

---

# Preliminaries: **Nagumo's Invariance Theorem**

Given a different function $\dot{x} = f(x)$ and a different trajectory:

<img alt="trajectory" src="./images/nagumo-invariance.png" width="30%"/>

Our goal is to ensure that the trajectory remains within a region of interest.

---

# Preliminaries: **Nagumo's Invariance Theorem**

{{< multicol >}}
{{< col class="col-7">}}

Given a function $h(x)$, the safe region (space of states we want to remain in) is:
<p>
$\mathcal{C} = \{ x \mid h(x) \geq 0 \}$ → <em>super zero level set of $h$</em>
</p>

<p class="fragment" data-fragment-index="1">
To guarantee that trajectories never leave $\mathcal{C}$ (safety), it is enough to ensure that, on its boundary: <br>
$\dot{h}(x) \geq 0 \forall x \in \partial \mathcal{C}$ <br>

If the condition holds, $\mathcal{C}$ is <em>forward invariant</em>: 
once inside, the system will always remain inside the safe region.
</p>

{{< /col >}}
{{< col >}}

<div class="r-stack">
  <img
    class="current-visible"
    src="images/nagumo-invariance02.png"
  />
  <img
    class="fragment current-visible"
    data-fragment-index="1"
    src="images/nagumo-invariance03.png"
  />
</div>

{{< /col >}}
{{< /multicol >}}

---

# Preliminaries: **Control-Affine Systems**

A **Control-Affine System** is a *dynamical* system described by the equation:

$\dot{x} = f(x) + g(x)u$

where:
- $x \in \mathbb{R}^n$ is the *state vector* (position, velocity, etc.),
- $u \in \mathcal{U} \subset \mathbb{R}^m$ is the *control input* (input, actuator commands, etc.),
- $f: \mathbb{R}^n \to \mathbb{R}^n$ is the *drift vector field* (the natural evolution of the system without control),
- $g: \mathbb{R}^n \to \mathbb{R}^{n \times m}$ is the *control input matrix* (how the control input affects the system).

<img class="fragment" data-fragment-index=0 src="./images/closed-loop-function.png" width="40%"/>

---

# Preliminaries: **Lie Derivatives**

The *Lie Derivative* of a differential scalar function $h: \mathbb{R}^n \to \mathbb{R}$ along a vector field $f: \mathbb{R}^n \to \mathbb{R}^n$ is defined as:

$L_f h(x) = \frac{\partial h}{\partial x} f(x) = \nabla h(x) \cdot f(x)$

It represents how $h(x)$ changes in time as the state evolves according to the system dynamics.

<div class="fragment" style="margin-top: 5rem;">

For a Control-Affine System $\dot{x} = f(x) + g(x)u$, the time derivative of $h(x)$ is:

$\dot{h}(x, u) = L_f h(x) + L_g h(x) u$

where:
- $L_f h(x)$ is the Lie Derivative of $h$ along $f$ (drift term),
- $L_g h(x)$ is the Lie Derivative of $h$ along $g$ (control term),
- $u$ is the control input.

*This notation captures how $h(x)$ evolves due to both natural dynamics and control input over time.*
</div>

---

# Control **Lyapunov** Functions (CLF)

A continuously differentiable function $V: \mathbb{R}^n \to \mathbb{R}_{\geq 0}$ \
is a *Control Lyapunov Function* for the target set $\mathcal{X}_d \subseteq \mathbb{R}^n$ if:
1. $V(x) = 0$ for all $x \in \mathcal{X}_d$ and $V(x) > 0$ for all $x \notin \mathcal{X}_d$ (positive definiteness);
2. For all $x \notin \mathcal{X}_d$, there exists a control input $u \in \mathcal{U}$ such that for some constant $c > 0$ *:

$L_f V(x) + L_g V(x) u \leq -cV(x)$

{{% spacer %}}

This condition ensures that $V(x(t))$ keeps decreasing over time, so the state $x(t)$ moves closer and closer to the desired target set $\mathcal{X}_d$.

In practice, this means we can drive the system toward its equilibrium, 
proving that the system is stabilizable through feedback control.

<div>
<small style="text-align: left">
* The constant $c > 0$ determines the rate of convergence; larger values lead to faster convergence.
</small>
</div>

---

# CLF Example: point stabilization

For a system {{< tab times="2">}}$\dot{p} = u${{< tab times="2">}}
where we want to stabilize the position $p$ of a point at a desired location $p_d$.

We want to design a control input $u$ that drives $p$ towards $p_d$.

We then define:
- the CLF: {{< tab >}}$V(p) = \|\| p - p_d \|\|^2$
- the Lie Derivatives of $V$ along $f$ and $g$ respectively:
{{< tab >}}$L_f V(p)=0${{< tab times="8">}}$L_g V(p) = 2(p - p_d)$

Thus,{{< tab >}} $\dot{V}(p,u)=2(p-p_d)^\top u${{< tab >}}which links the control input $u$ to the rate of change of $V$.

Choosing a control input $u$ such that:

$u=-k(p-p_d)$ for some $k > 0$ ensures that{{< tab >}}
$\dot{V} = -2k \|\| p - p_d \|\|^2 \leq 0$

Which guarantees that $V(p)$ decreases exponentially over time, 
driving $p$ towards $p_d$ and stabilizing the system at the desired point.

---

# Control **Barrier** Functions (CBF)

A continuously differentiable function $h: \mathbb{R}^n \to \mathbb{R}$ is a *Control Barrier Function* for the safe set 
<p>
$\mathcal{C} = \{ x \in \mathbb{R}^n \mid h(x) \geq 0 \}$
</p>

We want $\mathcal{C}$ to be forward invariant, i.e., if the system starts or enters in $\mathcal{C}$, it remains in $\mathcal{C}$ for all future time.

$h$ is a CBF if:
1. $h(x) > 0$ for all $x$ in the interior of $\mathcal{C}$, $h(x) = 0$ for all $x$ on the boundary of $\mathcal{C}$, and $h(x) < 0$ for all $x$ outside $\mathcal{C}$ (set definition);
2. For all $x$ in $\mathcal{C}$, there exists a control input $u \in \mathcal{U}$ such that for some constant $k > 0$ *:
3. $L_f h(x) + L_g h(x) u \geq -k h(x)$

{{% spacer %}}
This condition ensures that the system's state remains within the safe set $\mathcal{C}$ over time,
preventing it from entering unsafe regions. 

---

# CLF-CBF-**Quadratic Program**

---

# Research question(s)

- How to integrate CLF and CBF in Aggregate Computing?
- How to specify safety-critical requirements both at the single and the collective level?
- How to enforce safety and stability guarantees during the transient behavior of distributed adaptive systems?

---

# How to integrate CLF and CBF in Aggregate Computing?


---

# Aggregate Computing + CLF + CBF use cases

Formal mechanism to guarantee safety properties, e.g.:
- Obstacle avoidance while maintaining formation;
- Collision avoidance between agents;
- Safe navigation in dynamic environments;
- Staying inside a designated area;
- Maintaining sufficient network connectivity;
- Respecting density limits in regions of interest.

<!--
In control theory,
there exist formal methods that allow us to specify conditions for both achieving a desired objective (stability) and
ensuring that the system remains within safe operating regions (safety).
These conditions can be expressed through well-defined mathematical functions that act as certificates in the assumption that the model captures the reality well.

Aggregate Computing already provides formal tools and stability guarantees,
for instance through self-stabilizing constructs and Lyapunov-based analysis.
However, these guarantees are eventual: they ensure convergence, but do not control the transient behavior.
As a result, in safety-critical scenarios (e.g., robotics), the system may temporarily enter unsafe regions during adaptation.

Control Lyapunov Functions and Control Barrier Functions provide a principled way to express stability and safety requirements
through mathematical conditions that can be verified and enforced at runtime.
By integrating those requirements into Aggregate Computing,
we introduce a layer in which safety-critical conditions can be specified,
checked, and maintained directly at the collective level, enabling predictable and robust execution of distributed adaptive systems.


In control theory, stability and safety can be formally specified through conditions and constraints.
Aggregate Computing provides eventual stability but cannot prevent transient unsafe behavior.
Control Lyapunov and Control Barrier Functions allow enforcing stability and safety at runtime.
Integrating them into Aggregate Computing would enable collective-level safety guarantees.
The talk will outline motivation, applications, and a possible integration path.

--- 

Domanda:

Se avessimo ad esempio uno sciame di droni in formazione a V e un'ostacolo esattamente nella loro traiettoria,
come eviteranno l'ostacolo?

**risposte dal pubblico**

potrebbe anche succedere che si scollega la connessione tra di loro e ognuno vada per conto suo

---
-->