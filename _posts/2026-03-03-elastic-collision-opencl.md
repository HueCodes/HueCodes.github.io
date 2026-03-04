---
layout: post
title: "Elastic Particle Collision Engine: OpenCL GPU Compute + Spatial Hashing"
date: 2026-03-03
category: projects
---

A 2D elastic collision simulation for 1000 particles using OpenCL GPU compute and a spatial hash grid for broad-phase collision detection, written in C++ with SDL2 rendering.

![Elastic Particle Collision Simulation](/assets/images/projects/particle-sim-screenshot.png)

The naive approach to collision detection checks every pair of particles against each other. With 1000 particles that means 499,500 pair checks per frame. At 60 FPS, that is 29.9 million checks per second just for broad-phase detection before any physics is computed. The problem compounds with particle count: doubling particles quadruples the work. Spatial hashing breaks this by partitioning the world into a uniform grid and only checking particles against neighbors in adjacent cells. Each particle has at most 9 cells to check regardless of total particle count, reducing the problem from O(n²) to O(n * k) where k is average particles per cell.

![Spatial Hash Grid — 9-Cell Neighborhood](/assets/images/projects/particle-sim-spatial-grid.svg)

## CPU/GPU Split

The simulation splits work between CPU and GPU based on data dependencies. The spatial grid is built CPU-side each frame: particles are assigned to cells, cells are sorted by index, and the resulting `GridCell` array (start offset + count) and `sortedIndices` buffer are uploaded to the GPU. Collision resolution then runs entirely on the GPU with one OpenCL work item per particle. This split exists because building the spatial grid requires knowing all particle positions before any work item can proceed, making it inherently sequential. Collision resolution is embarrassingly parallel once the grid is built.

The `SpatialGrid` class manages this CPU-side pass. It iterates through all particles, hashes each position to a cell index, counts particles per cell, then builds the sorted index array using those counts as offsets. The result is a compact array of particle indices sorted by cell, with the `GridCell` array providing O(1) lookup into the correct range for any cell coordinate. Both buffers are uploaded to OpenCL device memory before the collision kernel launches.

![CPU / GPU Pipeline](/assets/images/projects/particle-sim-pipeline.svg)

## Kernel Design

Two OpenCL kernels handle the physics. The `resolveCollisions` kernel handles broad-phase detection, narrow-phase overlap testing, elastic response, and positional correction. The `integrate` kernel advances positions using Euler integration and handles boundary reflection. Separating them matters because position integration depends on the final velocity after all collisions are resolved. Running integration inside the collision kernel would create a race condition where one work item's position update affects another's collision check within the same frame.

The `resolveCollisions` kernel reads the particle's cell coordinates, then iterates over the 9-cell neighborhood (3x3 grid centered on the particle's cell). For each neighbor particle found via the sorted index buffer, it computes squared distance against the sum of radii and skips the square root unless there is actually a collision. Only approaching particles trigger the elastic response check, avoiding the velocity sign flip that occurs when two particles are separating but still overlapping.

## Elastic Collision Response

The elastic collision formula for two particles derives from conservation of momentum and kinetic energy:

```
v1' = v1 - (2*m2 / (m1+m2)) * dot(v1-v2, x1-x2) / |x1-x2|² * (x1-x2)
```

The scalar `dot(v1-v2, x1-x2) / |x1-x2|²` is the relative velocity projected onto the collision normal, which gives the component of relative motion along the line connecting the centers. Multiplying by `(x1-x2)` converts back to a velocity vector. The mass coefficient `2*m2 / (m1+m2)` ensures momentum conservation. For equal masses this reduces to a complete velocity exchange along the collision axis. For large mass ratios, the lighter particle receives most of the velocity change.

The kernel accumulates velocity changes from all collisions in the 9-cell neighborhood before writing back. This is an approximation. In a single simulation step a particle could be involved in multiple simultaneous collisions, and accumulating the responses independently introduces error compared to solving the full constraint system. For a real-time simulation the error is acceptable and imperceptible at the particle densities used.

## Positional Correction

Velocity response alone does not prevent particle overlap when frame time is long enough for particles to interpenetrate significantly. The kernel applies a mass-weighted positional correction alongside the velocity update. When two particles overlap, the correction vector pushes the lighter particle proportionally further than the heavier one: `correctionFactor = other.mass / totalMass`. This keeps the center of mass stationary under correction, which is physically consistent. Multiple corrections per particle within a frame are accumulated and applied as a sum rather than averaged, since each correction addresses a different overlapping neighbor.

## Memory Layout

The `Particle` struct is 32-byte aligned for GPU memory access:

```c
typedef struct __attribute__((aligned(32))) {
    float2 position;   // 8 bytes
    float2 velocity;   // 8 bytes
    float  mass;       // 4 bytes
    float  radius;     // 4 bytes
    float2 padding;    // 8 bytes
} Particle;
```

GPU memory accesses coalesce efficiently when adjacent work items access adjacent memory locations and data is aligned to the native vector width. The 32-byte alignment matches the float8 width on most GPU architectures. The explicit padding prevents the compiler from inserting unpredictable padding that would misalign the struct between the C++ host code and the OpenCL kernel.

## Boundary Handling

The `integrate` kernel applies reflective boundary conditions after position update. When a particle exits the world bounds, its position is clamped to keep the particle inside and the relevant velocity component is negated. The position clamp places the particle exactly at the boundary plus its radius, preventing wall penetration from accumulating over multiple frames. This runs after collision resolution, so a particle bouncing off a wall in the same frame as a particle collision will have both responses applied correctly since integration is the final step.

The simulation runs at stable 60+ FPS with 1000 particles on Apple Silicon. The dominant cost at this particle count is the CPU-side spatial grid rebuild each frame rather than the GPU kernels, since 1000 particles is well below the threshold where GPU parallelism saturates. Scaling to 10,000 particles shifts the bottleneck back to the GPU and would expose whether the 9-cell neighborhood lookup scales as expected.

---

[View on GitHub](https://github.com/HueCodes/Cpp-Particle-Sim)
