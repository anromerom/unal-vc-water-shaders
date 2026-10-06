# Water Shaders

Final project for **Visual Computing** at Universidad Nacional de Colombia (2020).
It simulates two aspects of water in Processing with GLSL shaders and the [nub](https://github.com/VisualComputing/nub) scene-graph library:

- **Water texturing:** a lake that reflects and refracts the scene, with animated ripples
- **Water geometry:** fake liquid inside a container that wobbles when the container moves

![Water texturing in Processing](resources/5.gif)

<details>
<summary><b>Demo</b></summary>

| | Reference | Ours (Processing) |
|---|---|---|
| **Texturing** | ThinMatrix, Java/OpenGL ![](resources/6.gif) | ![](resources/5.gif) |
| **Geometry** | Minions Art, Unity ![](resources/3.gif) | ![](resources/4.gif) |

| Skybox | Perlin terrain | DuDv map |
|---|---|---|
| ![](resources/skybox.png) | ![](resources/terrain.png) | ![](resources/dudv.png) |

</details>

<details>
<summary><b>How it works</b></summary>

**Water texturing** (based on ThinMatrix's [OpenGL water tutorial](https://www.youtube.com/watch?v=HusvGeEDU_U))
- A nub scene graph holds a skybox, terrain generated with Perlin noise, and `.obj` models (palm tree, moon, UFO).
- Each frame the scene is rendered three times: from a camera mirrored below the water (reflection), from the normal camera (refraction), and the final view.
- A DuDv map scrolling over time distorts the texture coordinates of both renders to make ripples.
- A Fresnel term (view direction · surface normal) blends reflection and refraction, plus a blue tint.

**Water geometry** (based on [Minions Art's liquid shader](https://www.patreon.com/posts/18245226))
- The fragment shader drops everything above a fill height. `gl_FrontFacing` picks a separate color for the inside surface.
- The node's linear and angular velocity feed a damped sine wave, which tilts the liquid surface through `_WobbleX` and `_WobbleZ`.

</details>

<details>
<summary><b>Project layout</b></summary>

| Path | What it is |
|---|---|
| `WaterTexturing/` | Reflection/refraction demo (IntelliJ Java project, `com.supermegadinamita.Main`) |
| `WaterTexturing/shaders` | Clipping and water shaders |
| `WaterTexturing/{data,models}` | Skyboxes and `.obj` models |
| `WaterGeometry/` | Liquid-in-a-container demo (Processing sketch) |
| `resources/` | Images and GIFs for this README |

</details>

<details>
<summary><b>Running it</b></summary>

Tested in Oct 2026 on Ubuntu 26.04. Both demos need **Processing 3.5.4**; newer versions change the nub or Processing APIs these demos depend on.

**WaterTexturing** (Java)
- Classpath: `lib/processing/core.jar` (Processing 3.5.4), `lib/nub/nub.jar` (nub 0.7.0), plus the JOGL jars.
- Working directory: `WaterTexturing/`.
- On recent Linux/Mesa, JOGL 2.3.2 segfaults. Use the JOGL 2.4.0 jars instead.

| Input | Action |
|---|---|
| Left drag / right drag | Turn / tilt the camera |
| Mouse wheel | Move forward/back |
| `s` | Next skybox |
| `t` | New random terrain |
| `f` | Fit the scene in view |

**WaterGeometry** (sketch)
- Needs **nub 0.6.0** in the sketchbook; it doesn't compile with nub 0.7+.
- Hover over the sphere to select it. Left drag spins it, right drag moves it, middle drag zooms.

</details>

<details>
<summary><b>Known issues</b></summary>

- **Clipping planes never worked.** `clipVert.glsl` reads a `model` uniform that is never set (the Java code sends `worldMatrix`), and `GL_CLIP_DISTANCE0` is not enabled.
- **Liquid level depends on the camera.** The fill height is computed from `inverse(view) × world`, so the liquid moves or disappears when the camera or the object rotates.
- **Colors are sent to the shader in the 0–255 range**, so they saturate. For example, `_TopColor (10,10,10)`, meant to be dark, renders white.
- **The reflection pass allocates a new `PGraphics` every frame,** which is slow (~7 fps with software rendering).
- **The two demos were never merged** into one scene.

</details>

## Team

| Member | GitHub |
|---|---|
| Nicolai Romero | [@anromerom](https://github.com/anromerom) |
| Julián Rodríguez | [@jdrodriguezrui](https://github.com/jdrodriguezrui) |
| Edder Hernández | [@Heldeg](https://github.com/Heldeg) |
