# Offset with three.js
Create an offset set of points or an offset mesh from an STL file using the [three.js](https://threejs.org/) library.

### Demo
https://threejs-offset.netlify.app/

![Offset](src/assets/img/offset-mesh-point.jpg)

## Table of Contents
- [Offset with three.js](#offset-with-threejs)
    - [Demo](#demo)
  - [Table of Contents](#table-of-contents)
  - [Installation](#installation)
  - [Usage](#usage)
    - [Offset a mesh](#offset-a-mesh)
    - [Offset geometry points](#offset-geometry-points)
  - [Notes](#notes)
  - [How it works](#how-it-works)
  - [Limitations](#limitations)
  - [Development](#development)
  - [Contributing](#contributing)

## Installation

```
npm install threejs-offset
```
or
```
yarn add threejs-offset
```

## Usage

This library provides two algorithms for offsetting STL geometry and one helper to build a mesh:

- `processGeometry` → offset points
- `createOffsetMesh` → offset a full mesh
- `createMeshFromObject` → convert an offset mesh object into a Three.js Mesh

### Offset a mesh

```ts
import { createOffsetMesh, createMeshFromObject } from "threejs-offset"

// Example: parse STL data and apply an offset
const stlData = "... your STL file contents ..."
const offset = 2.0

// Create an offset mesh object
const objects = createOffsetMesh(stlData, offset)

// Convert the object into a Three.js Mesh
const mesh = createMeshFromObject(objects)
scene.add(mesh)
```

### Offset geometry points

```javascript
import { STLLoader } from "three/examples/jsm/loaders/STLLoader.js";
import { processGeometry } from "threejs-offset";

// If you have an STL file (ArrayBuffer):
const geometry = new STLLoader().parse(arrayBuffer); // THREE.BufferGeometry
const offset = 2;

// Ensure normals are present (STLLoader usually provides them; otherwise compute)
if (!geometry.attributes.normal) geometry.computeVertexNormals?.();

const points = processGeometry(geometry, offset); // returns THREE.Points
scene.add(points);
```

## Notes

- `processGeometry` returns a `THREE.Points` instance with material `PointsMaterial({ color: 0xff0000, size: 0.7 })` and `name = "offset"`.

- `createOffsetMesh` expects an ASCII STL string (not a `BufferGeometry`). In practice the workflow in the demo is: export a mesh to ASCII (via `STLExporter`), call `createOffsetMesh`, then `await createMeshFromObject(...)`.

- If your geometry lacks normals, call `geometry.computeVertexNormals()` before using `processGeometry`.

## How it works

The library provides two algorithms:

- Point offset – creates a new set of vertices displaced by the given offset.
- Mesh offset – recalculates faces, normals, and vertices to generate a new offset mesh.

The meshed offset is still experimental: results are not perfect, but it’s a good starting point for further refinement.

## Limitations

- Performance may degrade with very large meshes.
- Some geometries (e.g. spheres) are not handled correctly yet.
- For heavy processing, consider moving parts of the algorithm (offsetObjectHash, hashTable) to a Node.js backend.

## Development

If you want to start the application locally:

1. Clone the project `git clone git@github.com:AngyDev/threejs-offset.git`
2. With your terminal go to the folder where you cloned the project
3. Run the command `npm install` to install the project's dependencies
4. Run the command `npm run build` that creates the dist folder
5. Run the server with the command `npm run start` 
6. If all is correct the browser will be open to the `localhost:9000` url

## Contributing

Contributions are welcome! If you have suggestions or bug reports, please open an issue.