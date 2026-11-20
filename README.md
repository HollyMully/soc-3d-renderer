# SOC 3D Network Renderer

A 3D visualizer of a simulated network attack chain, written in **raw WebGL** (no Three.js or other libraries). It shows a small enterprise network from a SOC analyst's point of view: healthy nodes, compromised nodes pulsing red, and attack packets moving laterally along the links, with IDS alerts mapped to MITRE ATT&CK.

Live demo: [https://HollyMully.github.io/soc-3d-renderer/](https://hollymully.github.io/soc-3d-renderer/)


## Features

- Raw WebGL pipeline: GLSL vertex and fragment shaders, VBO/IBO buffers, hand-written 4x4 matrix math
- Procedurally generated meshes (sphere, cube, cylinder)
- Blinn-Phong lighting (ambient, diffuse, specular) plus rim light on compromised hosts
- Free-roaming camera (yaw/pitch, WASD movement)
- 12-node network topology: Internet, firewall, core router, two switches, servers, workstations, SIEM/IDS
- Scripted attack simulation with a live alert log and a node state machine (healthy, compromised, alerting), driven to the GPU through uniforms

## Run locally

No build step and no dependencies.

Option 1: open `index.html` in Chrome or Firefox.

Option 2: serve it locally:

```bash
python -m http.server 8000
```

Then open http://localhost:8000

## Controls

| Input | Action |
|---|---|
| Mouse drag | Look around |
| W A S D | Move |
| Q / E | Down / up |
| Shift | Move faster |
| Mouse wheel | Dolly forward / back |

## Attack scenario

The scenario is scripted and simulated. See [docs/scenario.md](docs/scenario.md) for log sources and detection ideas for each step.

| Step | MITRE ATT&CK |
|---|---|
| C2 beacon reaches the perimeter | T1071 |
| Web shell on the Web Server | T1505.003 |
| Credential dumping on WS-01 | T1003 |
| SMB lateral movement to the File Server | T1021.002 |
| Data exfiltration from the DB Server | T1041 |

## How it works

1. JavaScript prepares data (node positions, states, matrices) every frame.
2. Model, View and Projection matrices transform each vertex: `Local -> World -> Eye -> Clip -> NDC -> Screen`.
3. The vertex shader applies the transforms; the fragment shader computes Blinn-Phong lighting.
4. A node's state is sent as a uniform, and the red pulse is computed on the GPU with `sin(time)`.
5. Labels are HTML elements positioned by projecting each node's world position to screen space.

## Limitations and roadmap

- [*] Host isolation mode (blue-team response)
- [*] Ray picking to click a node and inspect it
- [ ] Thicker links (cylinders instead of 1px lines)
- [*] Shadow mapping and bloom
- [ ] Read real Suricata `eve.json` alerts instead of a scripted scenario

Known limitations: link width is fixed at 1px; the normal matrix is approximate for non-uniformly scaled meshes; labels are not occluded by geometry.

## Development notes

## Development notes

The initial version of this renderer was generated with AI assistance
(Claude, by Anthropic). I studied the code and extended it myself:
- Added a host isolation mode (blue-team response, new shader state)
- Replaced the topology and attack path with my own scenario 
- Fixed the normal-matrix calculation for non-uniform scaling
- Wrote docs/scenario.md (log sources and detection ideas)
- First it was supposed to be my university project.

