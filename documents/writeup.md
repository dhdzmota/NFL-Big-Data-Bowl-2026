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
## Dominance in Action: Community-Level Narratives (Understanding the plots)

Once players are grouped into communities through their spatial interactions, dominance ceases to be an abstract surface and becomes a story. Each community defines a localized arena where influence is negotiated, exchanged, amplified, or extinguished. What follows is a qualitative reading of dominance as it unfolds within each detected interaction cluster of the play.

### Community I (First Interaction Graph): Ringo & Morrow vs. Waller & Barkley

The first community emerges around a defensive pair: Kelee Ringo (CB) and Nicholas Morrow (ILB), matched against Darren Waller (TE) and Saquon Barkley (RB) on offense.

From the outset, Saquon Barkley establishes clear dominance within this interaction. His movement maintains a safe and controlled distance from Kelee Ringo, allowing him to project influence without direct confrontation. This spatial patience results in sustained offensive dominance, as Barkley reshapes the nearby playable space while avoiding defensive collapse.

Simultaneously, Darren Waller begins with high attacking dominance, but this advantage is quickly contested. Nicholas Morrow closes the distance and applies pressure, sharply reducing Waller’s dominance footprint. What follows is not a clean takeover, but a rhythmic exchange: dominance oscillates between the two as each adjustment provokes a counter-adjustment. This interaction behaves less like a collision and more like a dance, with neither player fully surrendering control.

During this exchange, Kelee Ringo subtly drifts toward Barkley’s trajectory, notably without direct visual engagement. His movement carries low dominance, suggesting reactive positioning rather than proactive control. While spatially relevant, Ringo’s influence remains secondary to Barkley’s sustained command of the interaction.

### Community II (Second Interaction Graph): Blankenship vs. Bellinger

The second community is a tighter duel between Reed Blankenship (FS) and Daniel Bellinger (TE).

Early in the play, Blankenship experiences a significant loss of dominance as Bellinger approaches with speed and intent, attempting to infiltrate space decisively. Bellinger’s forward momentum temporarily bends the influence landscape in his favor, compressing Blankenship’s control zone.

However, as the interaction progresses, Blankenship adapts. Through positional correction and improved alignment, he gradually recovers dominance, culminating in a late-stage reversal. The result is a clear exchange: initial offensive dominance gives way to defensive control. This community exemplifies dominance as a fluid quantity, responsive to timing, proximity, and adjustment rather than raw speed alone.

### Community III (Third Interaction Graph): Reddick, Bradberry, Byard & Brown vs. Hodgins & Slayton

The third community is the most complex, involving Hasson Reddick (OLB), James Bradberry (CB), Kevin Byard (FS), and Sydney Brown (SS) on defense against Isaiah Hodgins (WR) and Darius Slayton (WR) on offense.

Within this dense interaction field, Darius Slayton emerges as the most dominant individual. He achieves the highest overall dominance of the play, briefly sacrificing control only to reclaim it decisively moments later. This temporary concession functions as a strategic trade: Slayton yields space to reposition, then reasserts influence with greater amplitude. His dominance curve reflects autonomy: he generates control largely on his own terms.

In contrast, Isaiah Hodgins is gradually neutralized. Initially, James Bradberry does not exert strong dominance, but his influence builds steadily through alignment and proximity. When Bradberry finally engages Hodgins directly, dominance shifts into an exchange, ending with Hodgins constrained and Bradberry asserting defensive control.

Another notable interaction involves Kevin Byard, who briefly contests Slayton’s dominance. However, Byard abandons this engagement to cover another receiver, and in doing so, relinquishes his influence over Slayton entirely. This moment highlights a key insight: dominance is not only gained through action, but also lost through reassignment.

Sydney Brown presents a special case. He records the highest average dominance within the community, but this dominance is largely uncontested. Brown spends much of the play running alone, unchallenged, accruing influence without friction. This raises a counterfactual question: had Brown redirected his attention toward Slayton, the dominance landscape, and potentially the play’s outcome, might have been entirely different.

### Community IV (Fourth and last Interaction Graph): Tyrod Taylor in Isolation

The final community consists solely of Tyrod Taylor (QB). For much of the play, Taylor remains isolated from direct interaction, which grants him substantial early dominance. With no immediate defensive pressure, his dominance reflects temporal control rather than spatial conflict. This isolation provides the cognitive and physical bandwidth required to scan the field and delay commitment. In this sense, Taylor’s dominance is not about overpowering an opponent, but about preserving optionality. His influence lies in the freedom to choose.

### Main Community takeaway: 

_This community-level reading demonstrates how dominance is not uniformly distributed, nor strictly tied to positions or outcomes. Instead, it emerges from localized negotiations of space, attention, and timing. The play is decided by how dominance is exchanged, deferred, or abandoned across interacting systems._

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