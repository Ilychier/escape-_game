# Escape!: 3D Survival Horror Maze & Ballistic Mechanics
**Visual Computing - Assignment No. 2**  
**Author:** Julián Camilo Herrera Ahumada  

Play the game directly in your browser without installation:  
👉 **[Play Escape! on GitHub Pages](https://ilychier.github.io/escape-_game/)**

---

## Game Overview
**Escape!** is a first-person 3D survival horror game developed in Unity, centered around tactical navigation through a procedurally generated labyrinth and dynamic threat management via a physics-based projectile system. The core objective is to escape the maze while utilizing environmental audio manipulation and ballistic stuns to evade patrolling adversaries.

---

## Controls & Debug Cheats

### Player Controls
| Action | Key / Input | Description |
| :--- | :--- | :--- |
| **Movement** | `W`, `A`, `S`, `D` | Navigate through the maze corridors |
| **Sprint** | `Left Shift` (Hold) | Increases movement speed (generates higher acoustic noise for enemies) |
| **Look / Aim** | `Mouse` | Rotate first-person perspective and aim the trajectory arc |
| **Aim Rock** | `Left Mouse Button` (Hold) | Activates real-time ballistic prediction and arc visualization |
| **Adjust Force** | `Mouse Scroll Wheel` | Modifies the initial throw force magnitude dynamically |
| **Throw Rock** | `Left Mouse Button` (Release) | Launches the projectile following the calculated parabola |
| **Pause Menu** | `Escape` or `P` | Opens or closes the pause menu |

### Debug & Testing Cheats (Special Delivery Features)
| Key | Function | Description |
| :--- | :--- | :--- |
| **`V`** | **Show Exit Path** | Instantly highlights or reveals the route toward the maze exit for testing and evaluation purposes. |
| **`B`** | **Disable Enemies** | Toggles enemy AI states off, allowing safe exploration and debugging of the labyrinth layout. |

---

## Physics and Ballistic Engine: `ParabolicRockThrower`

The core gameplay relies on a custom ballistic simulation implemented within `ParabolicRockThrower.cs` and `HeavyRockGravity.cs`, substituting basic raycasting with true 3D projectile motion under heavy gravity.

### 1. Kinematic Trajectory and Predictive Arc
The aiming trajectory rendered on screen is a real-time discrete integration of the classical Uniformly Accelerated Rectilinear Motion (MRUA) equations in 3D space:

$$\vec{P}(t) = \vec{P}_0 + \vec{V}_0 \cdot t + \frac{1}{2} \vec{g}_{custom} \cdot t^2$$

* **Spawn Position ($\vec{P}_0$):** Calculated dynamically offset from the camera's origin (`cameraTransform.position + forward * 0.75f + right * 0.25f - up * 0.15f`) to prevent self-collision with the player.
* **Initial Velocity Vector ($\vec{V}_0$):** Composed of the camera's forward look vector scaled by the user-adjusted throw force plus an upward arc boost:
  $$\vec{V}_0 = (\text{camera.forward} \cdot \text{throwForce}) + (\text{Vector3.up} \cdot \text{upwardArcBoost})$$
* **Custom Heavy Gravity ($\vec{g}_{custom}$):** Amplifies standard gravity by a multiplier ($2.5\times$), forcing steep, heavily curved trajectories that require tactical calculation over long distances.
* **Discrete Sampling:** Evaluated across $28$ trajectory points ($maxTrajectoryDots$) separated by regular time steps ($\Delta t = 0.055\text{ s}$).

### 2. Preventive Collision and Visual Feedback (`SphereCast`)
To ensure accuracy without clipping through maze walls, each temporal segment is tested using volumetric sweep tests:
```csharp
Physics.SphereCast(previousPos, 0.12f, segment.normalized, out RaycastHit hit, segmentLength)

```

* If a surface is intercepted, the trajectory cuts off and an impact marker aligns with the surface normal ($\vec{n}$).
* The system checks if any active enemy falls within the splash stun radius (`rockStunRadius`), shifting the trajectory arc color dynamically to indicate a **guaranteed stun lock**.

### 3. Continuous Proximity Stun & Acoustic Decoy System

Due to browser WebAssembly constraints in WebGL, collision events are backed up by a continuous frame-by-frame proximity scan (`Update` loop in `HeavyRockGravity`):

* **Direct & Splash Stun:** When a rock lands within $1.65\text{ m}$ of an enemy (or hits them directly), it triggers an immediate `Stun()` state, freezing their navigation behavior.
* **Acoustic Decoy:** If a thrown rock misses all enemies and impacts a wall or floor, it emits a noise notification (`NotifyNoiseAtPosition`) across a $15\text{ m}$ radius, drawing patrolling creatures away from the player's true location and turning missed shots into tactical distractions.

---

## Enemy AI Behavior System (Pac-Man Inspired Roles)

The labyrinth is patrolled by a coordinated multi-agent AI director managing four distinct behavioral archetypes:

1. **The Ambusher (Pinky Archetype):**
* **Behavior:** Calculates predictive vectors ahead of the player's current movement trajectory to cut off escape routes at corridor intersections.


2. **The Stalker (Blinky Archetype):**
* **Behavior:** Maintains continuous pressure by actively tracking and closing the distance toward the player in medium-to-close ranges.


3. **The Sentinel (Inky Archetype):**
* **Behavior:** Patrols lateral corridors and flanking zones, preventing the player from safely looping around obstacles.


4. **The Acoustic Prowler (Clyde Archetype):**
* **Behavior:** Possesses a narrower direct line-of-sight cone but exhibits heightened auditory sensitivity, reacting instantly to loud player actions such as sprinting or missed rock impacts.



---

## Technical Architecture & Tools

* **Engine:** Unity 3D (LTS 2022.3)
* **Scripting Language:** C# (.NET / Mono)
* **Physics System:** Unity PhysX (Rigidbodies, Custom Gravity, Continuous Dynamic Collision Detection)
* **Target Build Platform:** WebGL / HTML5 deployed via GitHub Pages.

---

## Copyright & Credits

© 2026 **Julián Camilo Herrera Ahumada**. All rights reserved.

* **Game Development & Original Music:** by Julián Camilo Herrera Ahumada.
* **External Assets:** Environmental sound effects and 3D models were obtained from free-to-use and open-source asset repositories for academic purposes.

```

```
