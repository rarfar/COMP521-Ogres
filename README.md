# Ogre Treasure Heist (Unity WebGL)

Play the game here:  
https://rarfar.github.io/COMP521-Ogres/

This project was created for **COMP 521 – Modern Computer Games** at McGill University.  
The goal of the assignment was to design enemy behavior using a **Hierarchical Task Network (HTN)** planner.

In the game, the player must **sneak past two ogres guarding caves, steal the treasure, and return safely to the spawn point**. The ogres are controlled by an HTN planner that decides how they behave based on the current world state.

---

## Gameplay Overview

The game takes place in a simple arena with two caves at one end and the player spawn at the other.

Your objective is to:

1. Reach one of the caves
2. Capture the treasure
3. Escape back to the spawn point

The challenge is avoiding the ogres guarding the caves. They can chase the player, attack in melee, or throw boulders depending on the situation.

You have **two lives**, so mistakes are costly.

---

## Controls

| Action | Control |
|------|------|
| Move | WASD |
| Look | Mouse |
| Toggle Invisibility | Space |

---

## Core Mechanics

### Invisibility

The player has a **10-second invisibility ability** that can be toggled using the space bar.

While invisible:
- Ogres cannot detect the player
- The invisibility meter slowly drains
- Once the meter reaches zero, the player becomes visible again

This ability allows the player to slip past enemies or escape when spotted.

---

### Lives and Combat

The player starts with **two lives**.

Damage can come from:
- Ogre melee attacks
- Boulders thrown by ogres

If both lives are lost, the game ends.

---

### Treasure Objective

Treasure is located inside the caves.

When the player enters the cave trigger:
- The treasure is captured
- A message appears telling the player to run

The player must then **return to the spawn location** to win.

---

## Environment Design

The level is built using a **60 × 120 unit plane** surrounded by walls.  

The environment includes:

- **Boulders** – physical obstacles that block movement and navigation
- **Mushrooms** – decorative objects that ogres can forage for
- **Two caves** – guarded by ogres
- **Spawn point** – the location the treasure must be returned to

Objects like boulders and mushrooms are spawned procedurally at runtime using an environment spawner script.

The script randomly places objects while ensuring:
- Caves remain unobstructed
- Objects do not overlap
- Navigation paths remain valid.

---

## Ogre AI – HTN Planner

The main focus of the project is the **Hierarchical Task Network (HTN)** used to control the ogres.

Each ogre continuously senses the world and builds a **plan of primitive actions** based on the current situation.

### World State

The planner tracks several pieces of information such as:

- Whether the player is visible
- Whether the player has stolen the treasure
- Whether the ogre is hungry
- Whether a boulder is available
- Whether the ogre is near its cave

These values are updated every frame through perception checks like distance tests, vision cones, and raycasts.

---

## HTN Structure

The planner begins with a top-level decision and then selects a method that expands into a sequence of primitive tasks.

![HTN Structure](images/Capture.png)

The high-level behavior is:

1. **Attack** if the player is visible  
2. **Attack** if the player has the treasure  
3. **Forage** if the ogre is hungry  
4. Otherwise **Idle**

Each of these options expands into smaller actions.

---

### Idle Behavior

When nothing important is happening, the ogres remain near their cave and perform idle actions.

Possible idle behaviors include:

- Pacing back and forth near the cave entrance
- Jumping in place

This keeps the enemies visually active even when the player is far away.

---

### Foraging

Ogres occasionally become hungry.

When this happens they:

1. Walk to a nearby mushroom
2. Eat the mushroom
3. Return to their cave

This creates some variation in their behavior and temporarily pulls them away from the cave entrance.

---

### Attack Behavior

If the player is detected, the ogres choose between several attack strategies.

**Melee attack**

If the player is close:
- The ogre charges forward
- Performs a melee strike

**Ranged attack**

If the player is further away and a boulder is nearby:
1. The ogre finds a boulder
2. Picks it up
3. Throws it toward the player's last known position

If no boulders are available, the ogre falls back to chasing the player.

---

## Planner Implementation

The HTN planner builds a **total-order list of primitive tasks** based on the current world state.

Examples of primitive tasks include:

- `PaceNearCave`
- `JumpIdle`
- `GoToMushroom`
- `EatMushroom`
- `ReturnHome`
- `FindAndGoToBoulder`
- `PickUpBoulder`
- `ThrowBoulderAtPlayer`
- `ChargePlayerAndStrike`

The ogre executes these tasks sequentially using a coroutine system.

Replanning occurs whenever something important changes, such as:

- The player becoming visible
- The player stealing the treasure
- The current plan finishing

This allows the AI to react dynamically to the player's actions.

---

## AI Debug Visualization

Each ogre displays debugging information above its head showing:

- The current world state
- The current plan
- Which task is currently executing

The UI is attached to a small canvas that always faces the player camera so the information is readable during gameplay.

---

## Technologies Used

- **Unity Engine**
- **Unity NavMesh** for pathfinding
- **C# scripts**
- **TextMeshPro UI**
- **Unity WebGL build**

The project is deployed using **GitHub Pages** so the game can be played directly in the browser.

---

## Author

Ryan McGregor  
McGill University – Electrical & Computer Engineering  
COMP 521 – Modern Computer Games

---

Thanks for playing!
