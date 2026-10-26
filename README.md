# IKProject

A third-person game prototype built with **Godot 4.6**, focused on **procedural IK animation** for the character: aiming, holding a weapon with both hands, rotating the torso to follow the camera, and enemies tracking the player with their heads. The project shows how to build such a system with Godot's built-in IK modifiers (`TwoBoneIK3D`, `CCDIK3D`, `CopyTransformModifier3D`) and connect it to a state machine and an `AnimationTree`.

![Gameplay Demo](/docs/demos/Gameplay.gif)

## Features

- **Aiming IK.** When the player aims, the character's spine bends toward the target (`CCDIK3D`), and both hands reach for the grip points on the weapon (`TwoBoneIK3D`). The IK influence is blended in and out smoothly, without jerks.
- **Weapon switching on the fly.** Each weapon has its own grip points for the right and left hand. When the weapon changes, the IK targets are automatically re-bound to the new weapon.
- **Four types of weapons:** primary, secondary and tertiary blasters, plus dual blasters. Weapons have procedural recoil (the model is displaced and rotated, then smoothly returns).
- **Player state machine** with state priorities: stand, crouch, sprint, aim, shoot, reload, standing jump, sprinting jump, mid-air, landing, ledge climb-up, emote.
- **Parkour.** Raycasts (`RayCast3D`) detect whether there is a wall in front of the player and whether it can be climbed or hopped onto. Movement during the climb-up comes from the animation's root motion.
- **Ground detection.** A downward ray checks the distance to the surface, which determines mid-air and landing behavior.
- **Torso rotation toward the camera.** When the angle between the body and camera directions exceeds a threshold, the character smoothly turns.
- **Enemies with their own state machine:** wandering, chasing the player, and attacking. A detection zone (`Area3D`) triggers the chase, and the enemy's head smoothly turns toward the player.
- **Targets.** They are destroyed when hit.
- **Debug HUD** with the flags "near wall", "can climb" and "can hop up".

![IK Demo](/docs/demos/DemoFixedCamera.gif)

## Controls

| Action           | Key                |
| ---------------- | ------------------ |
| Move             | `W` `A` `S` `D`    |
| Jump             | `Space`            |
| Sprint           | `Shift`            |
| Crouch           | `C`                |
| Aim              | Right mouse button |
| Shoot            | Left mouse button  |
| Reload           | `R`                |
| Primary weapon   | `1`                |
| Secondary weapon | `2`                |
| Tertiary weapon  | `3`                |
| Dual blasters    | `4`                |
| Emote            | `T`                |

## Getting Started

1. Install [Godot 4.6](https://godotengine.org/download). The project uses the new IK modifiers, so older versions will not work.
2. Clone the repository:
   ```bash
   git clone https://github.com/ArtemNik997/GodotIKProject.git
   ```
3. Open Godot, click **Import** and select the `project.godot` file from the project folder.
4. Wait for the assets to import and run the project with `F5`. The main scene is `scenes/levels/level.tscn`.

Renderer: Forward+. Physics: Jolt Physics. On Windows the Direct3D 12 driver is used.

## Project Structure

```
├── graphics/                 Models, textures, sounds and UI assets
│   ├── 3DCharacter/          Character model and animations
│   ├── weapons/              Blasters, projectiles, targets and shot sounds
│   ├── targets/              Target models
│   ├── prototype-textures/   Textures for level prototyping
│   └── ui/crosshair/         Crosshair
├── resources/                Shared materials and resources
└── scenes/
    ├── entities/
    │   ├── player/           Player: camera, input, model, skeleton with IK, states
    │   ├── weapons/          Weapons and their grip points
    │   ├── projectiles/      Projectiles and lasers
    │   └── target_holder/    Targets
    ├── hud/                  Interface
    └── levels/               Test level and global events
```

## How It Works

**Input and states.** `InputGatherer` collects the player's input into an `InputPackage`. The player model passes it to the current state, and each state decides in `check_relevance()` whether to stay in it or switch to another one. If several actions are possible, the one with the higher priority is chosen.

**Events.** Game systems communicate through the `PlayerEvents` and `GameEvents` autoloads (signals), while shared state is stored in `PlayerGlobals` and `GameGlobals`. Because of this, the skeleton, weapons, camera and states barely know about each other directly.

**Player IK.** The `skeleton.gd` script manages a set of modifiers: `CCDIK3D` for the spine, `TwoBoneIK3D` for each arm, and several `CopyTransformModifier3D` nodes for orienting the hands and upper body. When the player aims, the influence of all modifiers smoothly rises to 1, and when the player stops aiming, it returns to 0. The hand targets are taken from the `rh_target` and `lh_target` markers of the active weapon.

## Credits

- Character model and animations: [Universal Animation Library by Quaternius (Standard)](https://quaternius.itch.io/universal-animation-library) (files `UAL1_Standard`, `UAL2_Standard`).
- Blaster and target models, and the crosshair: [Kenney](https://kenney.nl/) asset packs.
- Prototyping textures: [Kenney Prototype Textures](https://kenney.nl) (CC0).

The licenses of third-party assets are located next to them in the `graphics` folder.

## Author

[ArtemNik997](https://github.com/ArtemNik997)
