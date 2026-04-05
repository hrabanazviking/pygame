# TASK: pygame Viking Edition — Full Transformation Plan

**Project:** pygame Viking Edition (hrabanazviking/pygame fork)  
**Session Start:** 2026-04-05  
**Collaborators:** Volmarr Wyrd + Runa Gridweaver Freyjasdottir  
**Branch:** development  
**Local Path:** `C:/Users/volma/runa/pygame`

---

## Vision Statement

Take the pygame library — the backbone of thousands of Python games and Raspberry Pi projects — and transform it into a modern, AI-native, 3D/VR-capable, vibe-coding-ready game engine foundation. Build the layer beneath H.E.R.E.T.I.C. and future NorseSagaEngine 3D expansion. Leave the code better, more robust, more capable, and more clearly structured than any version that came before it.

---

## Codebase Snapshot (as of 2026-04-05)

| Area | Files | Notes |
|---|---|---|
| `src_c/` | 154 | C core — SDL2 wrappers, blitters, math, surface, event, display, etc. |
| `src_py/` | 28 | Python layer — sprite, camera, midi, surfarray, sysfont, etc. |
| `test/` | 122 | Test suite |
| `docs/` | 249 | RST documentation |
| `examples/` | 88 | Example scripts |
| `buildconfig/` | 160 | Build system, deps config |

**Key C Modules:**
- `base.c` — pygame init/quit, error handling, module base
- `display.c` — window management, display modes
- `surface.c` / `surface.h` — Surface object, core pixel buffer
- `event.c` — event queue, pump, custom events
- `draw.c` — 2D drawing primitives
- `transform.c` — scale, rotate, flip
- `rect.c` — Rect object
- `color.c` — Color object
- `image.c` / `imageext.c` — image load/save
- `font.c` — bitmap font
- `_freetype.c` / `freetype/` — FreeType font engine
- `mixer.c` / `music.c` — audio
- `joystick.c` — gamepad/joystick
- `key.c` / `mouse.c` — input
- `mask.c` — collision masks
- `math.c` — Vector2, Vector3, Vector4
- `pixelarray.c` / `pixelcopy.c` — pixel-level access
- `simd_blitters_sse2.c` / `simd_blitters_avx2.c` — SIMD-accelerated blitting
- `scale2x.c` / `rotozoom.c` — image scaling algorithms
- `scrap_*.c` — clipboard (platform-specific: win/x11/mac/sdl2/qnx)
- `camera.c` / `camera_v4l2.c` — camera input
- `_sdl2/` — Cython wrappers for SDL2 audio, controller, video, mixer
- `newbuffer.c` / `bufferproxy.c` — Python buffer protocol
- `bitmask.c` — bitmask collision detection

**Key Python Modules:**
- `sprite.py` — Sprite and Group classes
- `camera.py` — camera abstraction
- `midi.py` — MIDI I/O
- `surfarray.py` / `sndarray.py` — numpy array integration
- `sysfont.py` — system font discovery
- `cursors.py` — cursor shapes
- `colordict.py` — named color database
- `draw_py.py` — pure-Python draw fallback
- `ftfont.py` / `freetype.py` — FreeType Python interface
- `locals.py` — constants re-export
- `macosx.py` — macOS-specific init

---

## Master Phase Plan

### PHASE 0 — Vibe Coding Foundation (Documentation & Structure)
> Goal: Make every part of this codebase legible to a vibe coding session. No file should be a black box.

**0A — Master Architecture Document**
- [ ] `ARCHITECTURE.md` — complete systems overview
  - Module dependency graph (mermaid)
  - C/Python boundary map (mermaid)
  - SDL2 integration layer (mermaid)
  - Build system flow (mermaid)
  - Data flow: event pump → game loop → render → display (mermaid)
  - Memory model and buffer protocol
  - Thread model
  - All module descriptions in plain language

**0B — Per-File Structure Documents**
- [ ] `docs/structure/` directory
- [ ] One `.md` per source file (all `.c`, `.h`, `.py` files)
- [ ] Each contains:
  - Purpose / responsibility
  - Public API (functions, types, constants)
  - Internal functions / static helpers
  - Data structures used
  - Dependencies (what it calls, what calls it)
  - Mermaid flowchart of main logic flow
  - Known quirks, edge cases, platform notes
  - Cross-references to related files

**0C — Test Coverage Map**
- [ ] `docs/TEST_MAP.md` — which tests cover which modules
- [ ] Gap analysis — what is not tested

**0D — Build System Docs**
- [ ] `docs/BUILD_SYSTEM.md` — how buildconfig works, how to add new C modules
- [ ] Dependency matrix for all platforms

**0E — API Surface Reference**
- [ ] `docs/API_SURFACE.md` — every public Python-facing symbol, with type stubs cross-reference

---

### PHASE 1 — Bug Audit & Hardening
> Goal: Make the code trustworthy. Every crash a past coder accepted as "just how it is" gets fixed.

**1A — Static Analysis Pass**
- [ ] Run `cppcheck` over all `src_c/` files, document all findings
- [ ] Run `clang-tidy` — flag all warnings
- [ ] Run `mypy` on `src_py/` with strict mode
- [ ] Run `pylint` on `src_py/`
- [ ] Document all findings in `docs/AUDIT_REPORT.md`

**1B — C Safety Audit**
- [ ] Null pointer dereference audit — every SDL call return value checked
- [ ] Integer overflow audit — especially in blitters and rect math
- [ ] Buffer overflow audit — pixel buffer access, string handling
- [ ] Use-after-free audit — SDL surface/texture lifecycle
- [ ] Uninitialized variable audit

**1C — Python Layer Safety**
- [ ] Type annotation completeness — all public functions annotated
- [ ] Error propagation — ensure C errors surface correctly as Python exceptions
- [ ] Argument validation — edge cases (negative sizes, None where object expected, etc.)

**1D — Memory Management Audit**
- [ ] SDL2 resource leak check — all SDL_CreateX matched with SDL_DestroyX
- [ ] GIL handling in threading contexts
- [ ] Refcount correctness in CPython extension code

**1E — Thread Safety**
- [ ] Identify all non-thread-safe paths
- [ ] Add guards where needed or document clearly

**1F — Self-Healing Patterns**
- [ ] Graceful degradation when hardware features unavailable
- [ ] Fallback paths for missing fonts, audio, camera
- [ ] Retry logic for transient SDL failures

---

### PHASE 2 — Cross-Platform & Platform Agnostic
> Goal: True parity. If it runs on Windows, it runs identically on RPi, Mac, and Linux.

**2A — Platform Abstraction Layer**
- [ ] Audit all `#ifdef` platform guards — map all platform-specific paths
- [ ] Create `pgplatform.h` extension with unified abstractions
- [ ] Platform capability detection at runtime vs compile time

**2B — Platform Parity Audit**
- [ ] Windows — full feature check
- [ ] macOS — full feature check (M1/M2 native + Rosetta)
- [ ] Linux x86_64 — full feature check
- [ ] Linux ARM (Raspberry Pi 4/5) — full feature check
- [ ] Document gaps per platform in `docs/PLATFORM_MATRIX.md`

**2C — ARM / Raspberry Pi Optimization**
- [ ] NEON SIMD blitters (ARM equivalent of SSE2/AVX2)
- [ ] Memory bandwidth optimization for RPi's unified memory
- [ ] GPU acceleration via OpenGL ES 2.0/3.0 on RPi

**2D — WebAssembly Target**
- [ ] Emscripten build configuration
- [ ] Pyodide integration path
- [ ] SDL2/Emscripten event loop adaptation

**2E — Android/iOS Groundwork**
- [ ] SDL2 mobile backend research and integration map
- [ ] Touch input normalization
- [ ] Mobile asset loading patterns

---

### PHASE 3 — AI Integration Layer
> Goal: Make pygame the natural substrate for AI-driven games. Python IS the AI language; pygame should feel that way.

**3A — AI Agent Interface**
- [ ] `pygame.ai` module — base classes for AI agents
- [ ] `Agent` — perceives game state, produces actions
- [ ] `AgentEnvironment` — wraps a pygame scene as an RL environment
- [ ] OpenAI Gym / Gymnasium compatible interface
- [ ] Observable game state serialization

**3B — Neural Network Hooks**
- [ ] Input preprocessing — screen → tensor (numpy bridge)
- [ ] Output postprocessing — tensor → action
- [ ] Frame buffer → model input pipeline (zero-copy where possible)
- [ ] Support: ONNX runtime, PyTorch, TensorFlow input formats

**3C — Procedural Content Generation API**
- [ ] `pygame.pcg` module
- [ ] Noise generators (Perlin, Simplex, Worley)
- [ ] Terrain generation hooks
- [ ] L-system support for flora/structures
- [ ] LLM prompt → asset description pipeline hook

**3D — LLM Narrative Engine Hooks**
- [ ] `pygame.narrative` module
- [ ] NPC dialogue state machine interface
- [ ] Event-driven narrative triggers
- [ ] WYRD Protocol world model bridge (integrate with WYRD ECS)
- [ ] Async LLM call management (non-blocking narrative updates)

**3E — Computer Vision Integration**
- [ ] Camera frame → numpy → model pipeline
- [ ] Face detection hooks (mediapipe/OpenCV bridge)
- [ ] Gesture recognition interface
- [ ] AR overlay support

**3F — Reinforcement Learning Environment**
- [ ] Full `gymnasium.Env` implementation wrapper
- [ ] Step/reset/render interface
- [ ] Observation space auto-detection from game state
- [ ] Action space mapping (keyboard/mouse/gamepad)
- [ ] Reward signal hook system

**3G — Emotion & Affect System**
- [ ] `pygame.affect` module
- [ ] Emotional state model for game entities (links to WYRD Wyrd Matrix concepts)
- [ ] Affect → visual feedback mappings (color, animation speed, particle behavior)
- [ ] Hume AI / local affect model bridge

**3H — WYRD Protocol Bridge**
- [ ] `pygame.wyrd` bridge module
- [ ] World model state → pygame scene sync
- [ ] pygame events → WYRD world model updates
- [ ] Character sheet → sprite/NPC binding

---

### PHASE 4 — Advanced 3D Support
> Goal: pygame can render 3D. Not just "you can use OpenGL" — actually support it as a first-class citizen.

**4A — OpenGL Scene Graph**
- [ ] `pygame.gl` module
- [ ] Scene graph: Node, MeshNode, LightNode, CameraNode
- [ ] Shader program management
- [ ] VAO/VBO abstraction
- [ ] Texture management (2D, cubemap, array)

**4B — Vulkan Renderer (Optional Backend)**
- [ ] Research: SDL2 Vulkan integration path
- [ ] `pygame.vulkan` optional module
- [ ] Command buffer abstraction
- [ ] Render pass management

**4C — 3D Math Library Extension**
- [ ] Extend existing `math.c` Vector2/3/4
- [ ] Matrix4x4 class (transform, projection, view)
- [ ] Quaternion class (rotation)
- [ ] Frustum / AABB / OBB classes
- [ ] Ray / plane intersection math

**4D — 3D Asset Loading**
- [ ] OBJ file loader
- [ ] GLTF/GLB file loader
- [ ] Material system (PBR properties)
- [ ] Mesh optimizer and normal computation

**4E — Lighting System**
- [ ] Physically Based Rendering (PBR) shader library
- [ ] Directional, point, spot light types
- [ ] IBL (Image Based Lighting) support
- [ ] HDR rendering pipeline

**4F — Shadow Rendering**
- [ ] Shadow mapping (directional)
- [ ] Omnidirectional shadow maps (point lights)
- [ ] Cascade Shadow Maps for large scenes

**4G — 3D Physics Integration**
- [ ] Bullet physics Python bridge
- [ ] Rigid body dynamics
- [ ] Collision shapes: box, sphere, capsule, convex hull, mesh
- [ ] Raycast API
- [ ] Character controller

**4H — Skeletal Animation**
- [ ] Bone/armature data structure
- [ ] Animation clip system
- [ ] Blend tree (interpolation between clips)
- [ ] Inverse Kinematics (FABRIK)

---

### PHASE 5 — VR Support
> Goal: pygame scenes can be experienced in VR. H.E.R.E.T.I.C. will run in a headset.

**5A — OpenXR Integration**
- [ ] `pygame.xr` module
- [ ] OpenXR session management
- [ ] Swapchain and framebuffer management
- [ ] Stereoscopic rendering pipeline (left/right eye)

**5B — HMD Management**
- [ ] Device enumeration (SteamVR, Oculus, WMR, standalone)
- [ ] Display refresh rate negotiation
- [ ] Foveated rendering support

**5C — Motion Controller Input**
- [ ] Controller pose tracking (position + orientation)
- [ ] Button/trigger/grip/thumbstick input
- [ ] Haptic feedback output
- [ ] Controller model rendering

**5D — Spatial Audio**
- [ ] HRTF (Head-Related Transfer Function) audio
- [ ] 3D sound positioning (relative to HMD)
- [ ] Room acoustics simulation
- [ ] Ambisonics decode support

**5E — Hand Tracking**
- [ ] OpenXR hand tracking extension
- [ ] 26-joint hand pose data
- [ ] Gesture recognition from joint data
- [ ] Hand mesh rendering

**5F — Room-Scale Support**
- [ ] Boundary/guardian visualization
- [ ] Standing, seated, room-scale mode detection
- [ ] Comfort/locomotion system (teleport, smooth turn)

---

### PHASE 6 — Internal API Architecture (Location Agnostic)
> Goal: Every pygame module communicates through a defined internal API. Code can move, split, or distribute without rewiring.

**6A — Module Communication Bus**
- [ ] Internal event bus (not SDL events — internal pygame signals)
- [ ] Message passing interface between C and Python layers
- [ ] Async message queue for non-blocking module communication

**6B — Plugin / Extension System**
- [ ] `pygame.plugin` module
- [ ] Plugin discovery and loading
- [ ] Versioned plugin API contracts
- [ ] Hot-swap of non-critical plugins

**6C — Hot-Reload Support**
- [ ] Game logic Python module hot-reload without restart
- [ ] Asset hot-reload (textures, audio, shaders)
- [ ] State preservation across reload

**6D — Distributed / Networked Mode**
- [ ] Optional: render process separate from game logic process
- [ ] Network protocol for remote game state → local rendering
- [ ] WebSocket and TCP transport options
- [ ] Headless server mode (AI training without display)

---

### PHASE 7 — Modern Game Engine Features
> Goal: Match and exceed the feature set of any contemporary Python game engine.

**7A — Entity Component System (ECS)**
- [ ] `pygame.ecs` module
- [ ] World, Entity, Component, System abstractions
- [ ] Archetype-based storage (cache-friendly)
- [ ] System dependency/ordering graph
- [ ] WYRD Protocol ECS bridge (leverage existing WYRD work)

**7B — Advanced Physics**
- [ ] 2D physics (Box2D integration — native Python)
- [ ] 3D physics (Phase 4G extended)
- [ ] Particle physics system
- [ ] Soft body / cloth simulation hooks

**7C — Shader System**
- [ ] GLSL shader hot-reload
- [ ] Shader graph (node-based, outputs GLSL)
- [ ] Post-processing stack (bloom, SSAO, DOF, motion blur, color grading)
- [ ] Compute shader support

**7D — Asset Pipeline & Streaming**
- [ ] Asset manifest and registry
- [ ] Async background loading
- [ ] Level-of-detail (LOD) management
- [ ] Asset compression and packing

**7E — Networking & Multiplayer**
- [ ] `pygame.net` module
- [ ] Reliable UDP (ENet or KCP)
- [ ] Client-server and P2P topologies
- [ ] State synchronization with delta compression
- [ ] Lag compensation / rollback netcode

**7F — Advanced Audio DSP**
- [ ] Effects chain (reverb, delay, chorus, EQ, compressor)
- [ ] Procedural audio generation
- [ ] Adaptive music system (state-driven soundtrack)
- [ ] Voice activity detection hook

**7G — Immediate Mode UI**
- [ ] `pygame.imgui` module (Dear ImGui Python bridge)
- [ ] In-game debug overlay
- [ ] Editor-mode UI panels
- [ ] Theming system

**7H — Scene Management**
- [ ] Scene graph (2D and 3D unified)
- [ ] Scene transition system
- [ ] Additive scene loading
- [ ] Scene serialization / save-load

**7I — Profiling & Debugging Tools**
- [ ] Frame time graph overlay
- [ ] GPU query timer integration
- [ ] Memory allocator tracker
- [ ] Event heatmap visualizer
- [ ] Python cProfile integration overlay

**7J — Extended Scripting**
- [ ] Lua scripting integration (lupa)
- [ ] REPL / console in running game
- [ ] Live code execution from in-game terminal

---

## Progress Tracker

| Phase | Status | Notes |
|---|---|---|
| 0A Master Architecture | **COMPLETE** | ARCHITECTURE.md — 29 sections, all major systems with mermaid |
| 0B Per-File Structure Docs | **COMPLETE** | 23 docs in docs/structure/ covering all C + Python modules |
| 0C Test Coverage Map | **COMPLETE** | docs/TEST_MAP.md |
| 0D Build System Docs | **COMPLETE** | docs/BUILD_SYSTEM.md |
| 0E API Surface Reference | NOT STARTED | Next phase work |
| 1A Static Analysis | NOT STARTED | |
| 1B C Safety Audit | NOT STARTED | |
| 1C Python Layer Safety | NOT STARTED | |
| 1D Memory Management | NOT STARTED | |
| 1E Thread Safety | NOT STARTED | |
| 1F Self-Healing Patterns | NOT STARTED | |
| 2A Platform Abstraction | NOT STARTED | |
| 2B Platform Parity Audit | NOT STARTED | |
| 2C ARM/RPi Optimization | NOT STARTED | |
| 2D WebAssembly Target | NOT STARTED | |
| 2E Android/iOS Groundwork | NOT STARTED | |
| 3A AI Agent Interface | NOT STARTED | |
| 3B Neural Network Hooks | NOT STARTED | |
| 3C PCG API | NOT STARTED | |
| 3D LLM Narrative Hooks | NOT STARTED | |
| 3E Computer Vision | NOT STARTED | |
| 3F RL Environment | NOT STARTED | |
| 3G Emotion/Affect System | NOT STARTED | |
| 3H WYRD Protocol Bridge | NOT STARTED | |
| 4A OpenGL Scene Graph | NOT STARTED | |
| 4B Vulkan Renderer | NOT STARTED | |
| 4C 3D Math Library | NOT STARTED | |
| 4D 3D Asset Loading | NOT STARTED | |
| 4E Lighting System | NOT STARTED | |
| 4F Shadow Rendering | NOT STARTED | |
| 4G 3D Physics | NOT STARTED | |
| 4H Skeletal Animation | NOT STARTED | |
| 5A OpenXR Integration | NOT STARTED | |
| 5B HMD Management | NOT STARTED | |
| 5C Motion Controller Input | NOT STARTED | |
| 5D Spatial Audio | NOT STARTED | |
| 5E Hand Tracking | NOT STARTED | |
| 5F Room-Scale Support | NOT STARTED | |
| 6A Module Communication Bus | NOT STARTED | |
| 6B Plugin System | NOT STARTED | |
| 6C Hot-Reload | NOT STARTED | |
| 6D Distributed Mode | NOT STARTED | |
| 7A ECS | NOT STARTED | |
| 7B Advanced Physics | NOT STARTED | |
| 7C Shader System | NOT STARTED | |
| 7D Asset Pipeline | NOT STARTED | |
| 7E Networking | NOT STARTED | |
| 7F Audio DSP | NOT STARTED | |
| 7G Immediate Mode UI | NOT STARTED | |
| 7H Scene Management | NOT STARTED | |
| 7I Profiling Tools | NOT STARTED | |
| 7J Extended Scripting | NOT STARTED | |

---

## Immediate Next Steps (Phase 0 Start)

1. **Write `ARCHITECTURE.md`** — master systems overview with mermaid diagrams
2. **Create `docs/structure/` directory** — per-file doc home
3. **Write per-file structure docs** — start with the most critical files:
   - Priority 1: `base.c`, `display.c`, `surface.c`, `event.c`, `draw.c`
   - Priority 2: `transform.c`, `rect.c`, `color.c`, `image.c`, `mixer.c`
   - Priority 3: `font.c`, `_freetype.c`, `joystick.c`, `key.c`, `mouse.c`
   - Priority 4: `mask.c`, `math.c`, `pixelarray.c`, `pixelcopy.c`
   - Priority 5: All SIMD blitters, scrap_*, freetype/* subsystem
   - Priority 6: All `src_py/` Python modules
   - Priority 7: All `.h` header files
4. **Write `TEST_MAP.md`** — test coverage analysis
5. **Write `BUILD_SYSTEM.md`** — build system documentation

---

## Key Design Decisions

- **This fork diverges from upstream.** If upstream pygame becomes active again, a diff/merge strategy will be needed. For now: our own version.
- **Backward compatibility is maintained through Phase 1-2.** Breaking changes (if any) are deferred to Phase 3+.
- **Phase 3 (AI) and Phase 4 (3D) can proceed in parallel** once Phase 0-2 are solid.
- **WYRD Protocol is the world model layer.** pygame Viking Edition renders it; WYRD owns the state.
- **H.E.R.E.T.I.C. is the first major target** for the complete stack (Phases 0-5 minimum before ship).

---

## Related Projects

| Project | Relationship |
|---|---|
| `WYRD-Protocol` | World model + ECS layer that pygame VE will render |
| `NorseSagaEngine` | Will eventually migrate to pygame VE 3D backend |
| `H.E.R.E.T.I.C.` | First major game built on pygame VE full stack |
| `Viking_Girlfriend_Skill_for_OpenClaw` | AI persona layer — will use 3H WYRD bridge |
