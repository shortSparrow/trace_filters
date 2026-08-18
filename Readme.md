# KernelScope 🎯

An interactive playground demonstrating real-time image processing, matrix convolutions, and optical effects. The project is built with pure JavaScript using the Canvas 2D API and **WebGPU (WGSL)**.

The core concept: there is an image and a specific area (a circle) attached to the cursor. Within this area, a dynamic image transformation takes place (such as blur, a lens effect, or a sepia filter). As the cursor moves, the interactive area follows it—applying the effect to everything inside the radius while restoring everything outside to its original state.

[Check effects you can here](https://shortsparrow.github.io/trace_filters/)

## 🚀 Modules & Features

### 1. CPU-Based Filters (Canvas 2D)
* **`sepia_effect.html` (Color Matrix Overlay):** Color space transformation. Each pixel within the cursor radius is multiplied by a custom matrix to enhance red tones and generate a sepia effect.
* **`blur.html` (Gaussian Blur 5x5):** Classic single-pass blur using a Gaussian matrix. Operates directly on the `ImageData` array within a circular radius around the cursor.
* **`blur_with_iterations.html` (Multi-pass Blur):** An experiment with deep blurring. The algorithm applies a convolution kernel to a local buffer multiple times sequentially, achieving a strong blur effect without a noticeable FPS drop.
* **`lens_base.html` (Base Lens):** A fundamental lens effect with in-code explanations of its mechanics. Exhibits aliasing artifacts (pixelation).
* **`lens_base_gradient.html` (Base Gradient Lens):** An enhanced base lens effect with smooth transitions instead of pixelation (achieved by computing the weighted average color of four neighboring pixels).

### 2. Hardware-Accelerated Shaders (WebGPU & WGSL)
* **`blur_web_gpu.html` (Hardware Blur):** Gaussian blur ported to a fragment shader. Utilizes GPU-accelerated texture samplers for instantaneous pixel processing.
* **`lens_web_gpu.html` (Refraction Lens):** A complex, physically-based glass lens simulation:
    * **Spherical Distortion (Refraction):** Calculates refraction vectors based on the distance to the lens center.
    * **Chromatic Aberration:** Splits red and blue color channels to imitate optical glass behavior.
    * **Specular Highlights:** Simulates a top-left light source using vector dot products to give the lens a glossy 3D appearance.

---

## 🛠️ Mathematical Foundations

Most blur effects in this project rely on a **2D Convolution Matrix (Kernel)**. For each target pixel $(x, y)$, the new color is calculated by multiplying the color values of neighboring pixels by the weight matrix coefficients and summing the results:

$$NewColor(x,y) = \sum_{i=-2}^{2} \sum_{j=-2}^{2} Image(x+i, y+j) \times Kernel(i+2, j+2)$$

For the **Refraction Lens**, standard texture coordinates ($UV$) are dynamically distorted using a spherical approximation:

$$Distortion = 1.0 - \sqrt{1.0 - \left(\frac{Distance}{Radius}\right)^2}$$

In **`sepia_effect.html`**, color transformation is achieved through linear channel transformation: each pixel's color is multiplied by a $3 \times 3$ coefficient matrix:

$$\begin{bmatrix} R_{new} \\ G_{new} \\ B_{new} \end{bmatrix} = \begin{bmatrix} 1.5 & 0.2 & 0.2 \\ 0.1 & 1.0 & 0.1 \\ 0.1 & 0.1 & 0.8 \end{bmatrix} \times \begin{bmatrix} R_{old} \\ G_{old} \\ B_{old} \end{bmatrix}$$

