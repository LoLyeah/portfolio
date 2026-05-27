---
title: "Building Mandelbulb 3D in TypeScript"
summary: "Exploring 3D fractal graphics on the web using optimized raymarching algorithms."
date: "May 27 2026"
draft: false
tags:
- Math
- Graphics
- TypeScript
---

Fractals are infinitely complex patterns that are self-similar across different scales. While the 2D Mandelbrot set is famous, its 3D counterpart, the **Mandelbulb**, offers a gorgeous landscape of spatial complexity.

In this post, we'll dive into how to build a high-performance Mandelbulb generator directly inside the browser using TypeScript and the HTML5 Canvas API.

### Raymarching in 3D Space

Unlike standard polygons, fractals are defined by mathematical formulas. To render them in 3D, we use an algorithm called **Raymarching**:

1. Cast a ray from the camera through each pixel.
2. Step along the ray using a Signed Distance Function (SDF).
3. If the distance to the fractal boundary is smaller than a tiny threshold, we hit the surface!
4. Calculate lighting, shadows, and ambient occlusion.

### The Mandelbulb Formula

The Mandelbulb is defined by mapping a 3D point $(x, y, z)$ into spherical coordinates $(r, \theta, \phi)$, raising the radius $r$ to the power $n$, adding the original coordinate, and repeating.

Let's check our typescript implementation for coordinate transformation:

```typescript
function sphericalCoords(x: number, y: number, z: number) {
  const r = Math.sqrt(x*x + y*y + z*z);
  const theta = Math.atan2(Math.sqrt(x*x + y*y), z);
  const phi = Math.atan2(y, x);
  return { r, theta, phi };
}
```

By tweaking the iteration depth and the power variable (traditionally $n = 8$), we can generate beautiful, bulbous formations.

### Interactive Tuning

By leveraging TypeScript's high-speed mathematical operations, we can dynamically change parameters on the fly, allowing users to zoom in and customize color palettes in real-time.
