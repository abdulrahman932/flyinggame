# flyinggame

A from-scratch 3D renderer built with **C++** and **OpenGL**, featuring procedurally generated terrain and a loaded 3D model, lit with a Phong shading model. This project was built to understand the graphics pipeline from the ground up — no game engine, just raw OpenGL calls wrapped in a small custom abstraction layer.

## Features

- **Procedural terrain generation** — a 50x50 vertex grid with heights driven by combined Simplex and Perlin noise (`GLM`'s noise functions), so the landscape is generated at runtime rather than loaded from a heightmap.
- **Height-based terrain coloring** — the vertex shader colors terrain as soil, grass, or snow depending on vertex height, giving the landscape a natural, layered look without textures.
- **Phong lighting** — ambient, diffuse, and specular lighting computed per-fragment in the shader.
- **3D model loading** — arbitrary `.obj` models are imported via **Assimp** (a jet model is loaded by default) and rendered alongside the terrain.
- **Texture loading** — images are loaded with `stb_image` and uploaded as OpenGL textures.
- **Custom OpenGL abstraction layer** — small wrapper classes around VAOs, VBOs, and index buffers (`VertexArrayB`, `VertexBufferO`, `IndexBufferO`, `VertexBufferLayout`) instead of raw GL calls scattered through the codebase.
- **Live debug UI** — built with **Dear ImGui**, exposing sliders to move the camera (eye/position) and transform the loaded model in real time.

## Tech Stack

| Purpose | Library |
|---|---|
| Windowing / input | [GLFW](https://www.glfw.org/) |
| OpenGL function loading | [GLEW](http://glew.sourceforge.net/) |
| Math (vectors, matrices, noise) | [GLM](https://github.com/g-truc/glm) |
| Model loading | [Assimp](https://github.com/assimp/assimp) |
| Image loading | [stb_image](https://github.com/nothings/stb) |
| Debug UI | [Dear ImGui](https://github.com/ocornut/imgui) |
| Language / API | C++17, OpenGL 3.3 Core Profile |

## Project Structure

```
main/
├── main.sln                   # Visual Studio solution
└── main/
    ├── main.vcxproj            # Visual Studio project
    ├── shader/
    │   └── shader.shader       # Combined vertex + fragment shader
    └── src/
        ├── main.cpp             # Entry point / render loop
        ├── core/
        │   ├── renderer.cpp/h   # Window init, clear, view/projection, draw calls
        │   └── Gui.cpp/h        # ImGui setup + slider widgets
        ├── renderer/
        │   ├── VertexArrayB.cpp/h
        │   ├── VertexBufferO.cpp/h
        │   ├── VertexBufferLayout.h
        │   ├── IndexBufferO.cpp/h
        │   ├── shader.cpp/h
        │   └── texture.cpp/h
        ├── scene/
        │   ├── GenerateGrid.cpp/h   # Procedural terrain mesh generation
        │   └── object.cpp/h         # Renderable object (grid or loaded model)
        └── utils/
            └── load_model.cpp/h     # Assimp import wrapper
```

## Getting Started

### Prerequisites

- Windows with **Visual Studio 2022** (project targets the `v143` toolset)
- The following libraries, built/available as static or import libraries:
  - `assimp.lib`
  - `glfw3.lib`
  - `glew32.lib`
  - `opengl32.lib` (ships with Windows)
  - `zlibstaticd.lib`

> The project currently references a fixed `dependencies` folder on the original author's machine. To build it yourself, either recreate a `main/dependencies` folder next to the project containing the libraries above (plus their headers), or update the **Additional Include Directories** / **Additional Library Directories** in the project properties to point at wherever you keep GLFW, GLEW, GLM, Assimp, and Dear ImGui on your machine.

### Assets

`main.cpp` loads a model from `assets/jet/jet.obj` relative to the working directory. Add your own `.obj` model at that path (or edit the path in `main.cpp`) before running.

### Build & Run

1. Open `main/main.sln` in Visual Studio.
2. Make sure include/library paths point to your local copies of GLFW, GLEW, GLM, Assimp, and Dear ImGui (see above).
3. Set the build configuration (Debug/Release, x64).
4. Build and run — a window opens showing the generated terrain with the loaded model on top of it.

## Controls

There's no camera-fly controls yet — instead, an ImGui panel lets you drag sliders to adjust:

- **Camera Eye** and **Camera Position** — move and re-aim the view.
- **Model Position** and **Model Rotation** — transform the loaded model in real time.

## How the Terrain Works

Each vertex's height is computed as `simplex(pos) * perlin(pos)`, sampled at a fixed frequency across a 50x50 grid, then triangulated into a standard quad-strip index buffer. The vertex shader reads each vertex's height (`aPos.z`) and assigns a color band — soil below 0.5, grass up to 3.1, snow above that — which is then passed to the fragment shader for Phong lighting.


No license has been specified for this project yet.
