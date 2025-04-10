# Evolutionary Steps

## Overview
Initially solving this issue in **2D** will be simpler than in **3D**, and it's easier to conceptualize. Once the 2D design is developed, we can evolve it to 3D. All of this assumes an empty room. The last stage will be to consider obstructions inside the room.

### Mock Scenario
2D — Noise can only propagate from one edge.

![Propogation Possibilites](./images/2-1-propogation-possibilites.png)

## Key Assumptions
- 2D room
- Empty room
- 0° ≤ θ < 180°
- θ is the direction the wavefront is propagating
- Noise is periodic

---

## How Can We Find the Direction of the Wave?

### Phased Microphone Array
- We have an array of microphones.
- Depending on the angle, the wavefront will hit one mic first.
- **Goal**: Choose a delay for each mic so the wave can be summed into a much larger signal.

![Steering Angle](./images/understanding-steering-angle.png)


### Delay 

Phase Delay was used in the development for a system for radio frequencies so I will implement that first.

$$
\text{Phase Shift between mics}=\Delta \phi = \omega \cdot \Delta t 
$$
$$
\text{Time Delay Between Mics}=\Delta t = \frac{L}{v}
$$`
$$
  \text{Distance Delay}= L = d\sin(\theta)
$$



$$
\text{Angular Frequency}=\omega = 2 \pi f
$$
$$
\Delta \phi = 2 \pi f \cdot \frac{d\sin(\theta)}{v}
$$

We can see here that phase delay $\propto$
1. frequency of the intruding sound wave
2. distance between mics
3. Incident angle

### Solving for Angle of Arrival (θ)

For our algorithm we need $\theta$ so if we use algebra we can see that:

$$
 v  
$$
---
## Experiment 1:
Each incident angle has a corresponding phase de

Given $f$ and $d$ we can iterate through 180 degrees of phase shift 
## ADALM-PLUTO (Software-Defined Radio Platform)

### Receiver Side:
- 2 receive ports → Low Noise Amplifier (LNA)
- LNA amplifies the signal while keeping a low noise floor
- Signal is sent to a mixer for 0 Intermediate Frequency (0 IF) processing using a 2.3 GHz Local Oscillator (LO)

### Transmit Side:
- Baseband signal is converted to analog via DAC
- Analog signal is upconverted to RF using the same 2.3 GHz LO
- Signal is transmitted via antenna

---

## Sinusilence — Acoustic Cancellation System

![System Archeticture](./images/hardware-arch.png)

### Receiver Side:
- Two microphones 
- LNA filters out frequencies outside of human hearing and very quiet noises

### Transmit Side:
- Transmit a 50 Hz sine wave

---

## Given:
- Frequency = 50 Hz  
- Wavelength λ = v / f = 343 m/s / 50 Hz = 6.86 m  
- ½ λ = 3.43 m  
- System works best when mic spacing = ½ λ

---

### Phase & Delay Table

| θ (degrees) | ΔΦ (radians) | Δt = d × sin(θ) / 343 (s) | ΔΦ = 2πfΔt (degrees) |
|-------------|----------------|-----------------------------|------------------------|
| 0°          | 0              | 0                           | 0                      |
| 20°         | ≈ 0.34         | (calculate)                 | (calculate)            |
| 30°         |                |                             |                        |
| 45°         |                |                             |                        |
| 60°         |                |                             |                        |
| 75°         |                |                             |                        |
| 90°         |                |                             |                        |

---

## 2D Room — Optimal Microphone Locations

### Objective
Minimize the area affected by intruding noises in a quadrilateral room.

![Phased Array on Vertex](./images/optimal-mic-positions.png)

- Arrows show sound propagation direction.
- Black dots = microphones.
- Sound waves moving **perpendicular to an edge** won't enter the room no matter the orientation of microphones on a particular edge closes to source.
- When propagation angle shifts from 90 degrees to a more acute angle, some sound leaks in before the system reacts.

---

![Phased Array on Vertex](./images/optimal-mic-positions2.png)

### Observations
- We only consider a two element phased array to be closest to right vertex
- The red wavefront came from the left and the highlighted pink area shows how much area it travels over before it can be detected by current system.
- Adding mics on the opposite side (left) can prevent leakage.

---


![Phased Array on Vertex](./images/optimal-mic-pos3.png)

### Key Insight
- When sound comes from the same quadrant, no new mics are needed.
- **45° angle** from a vertex causes the most leakage.
- Use this to guide testing.

### Note on Flat Wavefront Assumption:
- Gray area beyond the purple box = zone where flat wavefronts are expected.
- Closer sources may have spherical wavefronts, which cause more leakage.
- Using Fraunhofer Distance (D = 2a² / λ), we see the source must be very close to break flat assumption.
- Convention in acoustics assumes **urban noise = flat wavefront**.

---

## Problem from Other Quadrants

- Brown wavefront from another quadrant shows major leakage.
- If no mics are placed in that quadrant, system won’t respond in time.

![Phased Array on Vertex](./images/optimal-mic-pos4.png)


**Conclusion:** You need **two mics per corner** of a 2D room.

---

### Question:
If the wavefront comes from a specific quadrant, how do we activate **only** the correct phased mic array?
- Directional Mics can help remove noise from behind

---

## Context Model

### System Boundaries

All systems are part of **Sinusilence**.

- **Adaptive Beam Forming (ABF)**  
  - Input: Intruding noise  
  - Output: Direction of noise source

- **Beamforming System**  
  - Uses:  
    - Direction from ABF  
    - Wall location and emission delay from User Position System  
  - Output: Targeted anti-noise signal sent toward user for optimal cancellation

---

## Use Case

The user can interact through a knob that adjusts values from 0–100:

- 0 = system off  
- 100 = full cancellation  
- In-between = partial noise reduction

---
