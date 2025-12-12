# Dominance Index: Decoding the Play From Pass to Outcome

## A tracking-based analytical breakdown that quantifies spatial control using our Dominance Index from pass release to play resolution.

Some plays are won long before the ball touches a receiver’s hands. They are won in the invisible exchanges: the shifts of balance, the micro-duels, the territories claimed and surrendered across the field. Yet, for all the sophistication of modern tracking data, these invisible dynamics remain largely unmeasured.

We can quantify speed, acceleration, and expected yards. We can measure separation or predict completion probability. But we still cannot answer with clarity: Who controlled the field? Who shaped the play before the final outcome was materialized?

This project was born from those questions.

What if it were possible to quantify a player's presence as it unfolds? What if the field could be described as an interacting system in constant negotiation; where players continuously rewrite each other’s opportunities? The **[Dominance Index](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#dominance-index)** is our attempt to answer those questions.

Traditional metrics rarely capture the spatial control dynamics that precede and often dictate the outcome.

The **[Dominance Index](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#dominance-index)** is not a statistic about players in isolation; it is a metric about their interactions. It measures how each player reshapes the playable space around them, frame by frame, from pass release to final resolution. In other words, it quantifies the influence landscape of a play.

---

## How the System Works: From Frames to Influence Landscapes

Every play is decomposed into its constituent frames. At each frame, every player contributes a _[dominance field](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#dominance-field)_ which is a measurable imprint of how their physical state, positioning, and interactions affect the surrounding space.

![Overview image](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/assets/figures/Overview.png?raw=true)

To construct this, we used the following attributes from the tracking data:
- (x, y) position
- Speed and acceleration
- Direction and orientation
- Height and weight
- Team designation (offense/defense)

From these, we compute additional quantities:
- Mechanical properties derived from physics (such as [Kinetic Energy](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#kinetic-energy), [Momentum](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#momentum),  [Force](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#force) and [Action](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#action)).
- Community structure extracted via proximity networks.

![Attributes and features](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/assets/figures/attributes-and-features.png?raw=true)

Individually, these metrics describe motion. Together, they describe presence.

### Step 1: Proximity Networks & the Birth of Communities

The first layer of the analysis examines how players naturally form _clusters of interaction_ within each frame.

1. A [fully connected graph](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#fully-connected-graph) is created among all players.
2. Edges are weighted by inverse distance (1/distance): Closer players exert stronger mutual influence.
3. A dynamic threshold filters to the top 20% strongest edges. These represent meaningful spatial relationships. 
4. In each frame, we apply _[Louvain community detection](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#louvain-community-detection)_ to uncover emergent clusters.
5. From these evolving communities, we identify the longest-lasting community assignments across the play, ensuring stable player-group identities.

These communities emerge organically from player movement. They can be understood as pockets of interaction where dominance exchanges might be most intense.

### Step 2: Computing Dominance: From Physics to Influence

To quantify a player’s dominance at a given moment, we combine three essential components:

A. **Sign**: Team Sign (Offense = +1, Defense = –1)

This captures the asymmetry of intent: defenders constrain opportunities; offenders expand them. When they interact, the overall dominance tends to neutralize.

![Sign](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/assets/figures/offense_defense.png?raw=true)


B. **Amplitude**: The Mechanical Coefficient Amplitude

A single value that synthesizes multiple physical and behavioral components of a player’s movement. It captures how forcefully, efficiently, and cohesively a player interacts with the play:
- **Mechanical**:  [Kinetic energy](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#kinetic-energy), [momentum](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#momentum),  [force](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#force) and [action](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#action), all normalized to historical reference values to ensure comparability across players and positions.
- **[Stability](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#stability)**: Orientation-to-motion stability, which measures how aligned a player’s body orientation is with their direction of movement.
- **[Cohesion](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#cohesion)**: Team cohesion, describing how well a player remains integrated with the movement of their immediate group or community, rather than drifting into structurally weak or isolated positions.
Players who move with purpose, physical leverage, and synchronized alignment with their unit contribute higher Ampitude. These are the players who subtly shift the geometry of the play, even before the ball arrives.

![Mathematical Amplitude](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/assets/figures/mathematical_amplitude.png?raw=true)


C. **The Gaussian Influence Field (G)**

A spatial density function governed by:
- Residual positions (dx, dy) along longitudinal and lateral axes
- Anisotropic spreads derived from:
    - speed and acceleration (longitudinal reach)
    - height and lateral speed (lateral reach)

![Mathematical Gaussian Influence Field ](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/assets/figures/mathematical_gaussian.png?raw=true)

This Gaussian describes how a player’s influence radiates outward shaped by biomechanics.

D. **Dominance computation**:

Once Sign, Amplitude, and the Gaussian Influence Field are obtained, we combine them into a continuous dominance function defined over the field. At any position, a player’s dominance is expressed as

![Dominance formula](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/assets/figures/dominance_formula.png?raw=true)

This formulation produces a spatial influence surface describing how strongly, and in which direction, each player impacts the surrounding playable space. The [dominance field](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#dominance-field) evolves frame by frame, reflecting real-time changes in mechanics, orientation, and interactions with other players.

---

### Step 3: Dominance Index: The Final Layer of Meaning

Each player’s instantaneous [dominance field](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#dominance-field) is masked onto a 5-yard radius around them. Within this zone, we evaluate how much of the dominance is aligned with the player’s own team and compute a proportion between 0 and 1.

This yields the **Dominance Index** for that moment: A value that reflects both what a player does and what the surrounding space becomes because of them and because of their interactions with other players.

As players accelerate, rotate, engage, disengage, and negotiate space, their Dominance Index fluctuates accordingly. Plays become stories told through curves, peaks, and sudden drops in spatial control.

---

## From Numbers to Meaning: Visualizing the Invisible

We present the full system in a cohesive, frame-by-frame report:

- Tracking visualizations showing each player’s movement (the direction is represented with the black arrow, and the orientation of the player is represented with the purple arrow) with the corresponding [dominance field](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/documents/glossary.md#dominance-field).
- A projection through time of each dominance fields across the field to understand field control and area divisions.
- Game status with the corresponding play context. 
- Players with the highest average dominance through the play.
- Community structures assigning the players into their natural conflict zones.
- Player-level dominance trajectories changing frame by frame showing the complex dynamics and interactions of the play.

![Report](https://github.com/dhdzmota/NFL-Big-Data-Bowl-2026/blob/main/experiments/outputs/output__report__2024010711_55.gif?raw=true)

This visualization lets us watch the play the way the play truly happened.

---

## What the Dominance Index Reveals

The Dominance Index opens new analytical territory:

1. Field Control Is a Dynamic, Not a Moment:
The decisive instant of a play is rarely the one analysts highlight. Dominance shows how opportunities wax and wane over time. 

2. Communities Matter More Than Positions:
Players influence the play through their interaction clusters, not simply through their roles on the depth chart.

3. Defensive Dominance Has Shape:
It is not merely _closing in_ it is the coordinated collapse of space: detectable frames before the outcome.

4. Offense Thrives on Stability, Not Chaos
Well-aligned offensive groups generate sustained positive dominance, creating structured windows for success.

5. The Play Outcome Is Written Early
In many snaps, the key shift in dominance occurs well before the ball reaches its destination. The index transforms film into something measurable: It reveals that influence is a negotiation.

---

## **Why This Matters for the NFL Big Data Bowl**

The NFL is a game of decisions shaped by micro-dynamics. The Big Data Bowl challenges us to unearth insights hidden in those dynamics. The Dominance Index contributes a new perspective:

It offers a physics-informed, community-aware, interaction-driven quantification of spatial control.

This allows coaches, analysts, and models to see:

- Where control is gained or lost
- How clusters may shape outcomes
- Which players subtly bend the space around them
- How defensive and offensive intentions collide
- And ultimately, how the invisible structure of a play determines its fate

Because in football the truth of a moment often lies in what came just before it.

And the Dominance Index illuminates that moment.