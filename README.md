# The Top-Down Cascade Method for Stick–Slip Analysis


---

## Overview
The **Top-Down Cascade Method (SLIP-SPLIT-CALCULATE Algorithm)** is a deterministic analytical framework designed to calculate the instantaneous acceleration of rigid bodies in a vertical stack coupled by dry friction. 

In classical mechanics, analyzing an $n$-block frictionally coupled stack traditionally requires proposing dynamic stick-slip configurations across all $n$ interfaces and solving complex global equations. This method eliminates global guessing and matrix operations by evaluating the system top-down layer by layer, isolating local sub-problems, and verifying internal shear stability through an automated fail-safe mechanism.

---

## System Model & Assumptions

Consider $n$ rigid blocks arranged vertically, numbered $1$ (top) through $n$ (bottom, resting on a fixed surface):
* **Masses:** Block $i$ has mass $m_i$.
* **External Drives:** Each block $i$ can carry an independent, horizontal external force $F_i$.
* **Interface Friction Limits:** Interface $i$ (between block $i$ and $i+1$) has a limiting friction capacity $f_i > 0$ derived from normal force transmission.
* **Friction Equivalence:** Static and kinetic friction coefficients are equal ($\mu_s = \mu_k$) across all interfaces. Consequently, $f_i$ represents both the maximum static threshold and the exact kinetic friction exerted during sliding.

---

## The Four-Phase Computational Logic

    [Phase 1: System Initialization]
                  │
                  ▼
    [Phase 2: Top-Down Boundary Evaluation] ──(Interface Holds)──► [Merge Block into Group]
                  │                                                         │
           (Interface Yields)                                               │
                  │                                                         │
                  ▼                                                         │
    [Phase 4: Internal Shear Verification] ◄────────────────────────────────┘
                  │
        ┌─────────┴─────────┐
        ▼                   ▼
    [No Fracture]       [Fracture Identified]
        │                   │
        │                   ▼
        │               [Split Group into Sub-Fragments]
        │                   │
        └─────────┬─────────┘
                  │
                  ▼
    [Phase 3: Load Transmission (Cascade)] ──► [Repeat for Remaining Stack]


### Phase 1: System Initialization
1. Establish a single global horizontal coordinate axis (rightward positive).
2. Calculate the limiting friction force $f_i$ for every interface $i = 1, \dots, n$ based on total vertical mass supported.

### Phase 2: Top-Down Boundary Evaluation
1. Begin at the top of the stack ($p = 1$) with zero carried force ($C = 0$).
2. Form a candidate group spanning blocks $p$ through $q$ (initially $p = q$).
3. Compute the total net drive force $D$ acting on the group:
   $$D = C + \sum_{i=p}^{q} F_i$$
4. Compare $|D|$ with $f_q$ (limiting friction of the boundary interface beneath block $q$):
   * **If $|D| \le f_q$:** Interface $q$ holds. Block $q+1$ is appended to the group ($q \to q+1$), and evaluation repeats.
   * **If $|D| > f_q$:** Interface $q$ yields. If the group consists of a single block ($p = q$), compute acceleration directly. If $p < q$, proceed to **Phase 4**.

### Phase 3: Load Transmission (The Cascade)
When an interface $q$ yields under drive $D$, kinetic friction acts on the lower block $q+1$. The transmitted force is:
$$C_{\text{next}} = f_q \cdot \operatorname{sgn}(D)$$

This force acts as the known input drive $C$ carried into the next unresolved segment of the stack, decoupling lower calculations from upper motion history.

### Phase 4: Internal Shear Verification (The Fail-Safe)
When a multi-block candidate group ($p$ through $q$) yields at interface $q$, internal shear forces must be checked to confirm if internal friction can maintain rigid adherence.

1. **Calculate Provisional Group Acceleration:**
   $$a^{(0)} = \frac{D - f_q \cdot \operatorname{sgn}(D)}{\sum_{i=p}^{q} m_i}$$

2. **Upward Internal Shear Check:**
   Scan internal interfaces upward from $j = q-1$ down to $p$. Calculate required internal force $F_{\text{req}}(j)$ at interface $j$:
   $$F_{\text{req}}(j) = \left(\sum_{i=p}^{j} m_i\right) a^{(0)} - \left(C + \sum_{i=p}^{j} F_i\right)$$

3. **Evaluation & Fracturing:**
   * **If $|F_{\text{req}}(j)| \le f_j$ for all $j$:** Group moves as a single rigid body with acceleration $a^{(0)}$.
   * **If $|F_{\text{req}}(j)| > f_j$ at any interface:** The group fractures at the first failing interface $j^*$ encountered during the upward scan. 
   * **Fracture Split:**
     * **Upper Segment ($p \dots j^*$):** Solved under initial carried load $C$.
     * **Lower Segment ($j^*+1 \dots q$):** Driven from above by the kinetic friction transmitted across fractured interface $j^*$.

---

## Analytical Summary Formulae

* **Single Block Yield Acceleration:**
  $$a = \frac{D - f \cdot \operatorname{sgn}(D)}{m}$$

* **Verified Group Rigid Acceleration:**
  $$a_{\text{group}} = \frac{\left(C + \sum_{i=p}^{q} F_i\right) - f_q \cdot \operatorname{sgn}(D)}{\sum_{i=p}^{q} m_i}$$

---

## Core Advantages
1. **Deterministic Execution:** Replaces $2^n$ state-space searches with a linear sequential sweep.
2. **Local Decoupling:** Transmitted kinetic friction isolates dynamic blocks into independent local subsystems.
3. **Internal Shear Integrity:** Upward scanning guarantees no rigid group is assigned an impossible state that its internal contact surfaces cannot physically sustain.
   

**Developed by: Aryan Kumar Singh,
INDIAN HIGH SCHOOL STUDENT**
