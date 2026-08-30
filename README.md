<div align="center">
  <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript" />
  <img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white" alt="HTML5" />
  <img src="https://img.shields.io/badge/Forensics-000000?style=for-the-badge&logo=spring-security&logoColor=white" alt="Forensics" />
</div>
<br>

# 🐉 Dragon Radar: Advanced DOM & Mouse Behavior Telemetry

A lightweight, dependency-free telemetry module designed for forensic user behavior analysis and UX tracking. Executed via an Immediately Invoked Function Expression (IIFE), it injects a floating, draggable UI panel directly into the DOM. It performs real-time interception of user events and renders geospatial coordinate data onto an HTML5 Canvas using a "Dragon Radar" aesthetic.

## 🛠 Core Architecture & Techniques

| Layer | Technique / Technology | Application in Project |
| :--- | :--- | :--- |
| **Injection & Scope** | Vanilla JS (ES6+) IIFE | Encapsulates variables to prevent global scope pollution while injecting the tracker. |
| **Event Delegation** | `document.addEventListener` | Hooks into `mousemove`, `mousedown`, `click`, `keydown`, `keyup`, and `change` events. |
| **Visual Rendering** | HTML5 `<canvas>` API | Renders concentric radar grids, dynamic radial gradients for clicks, and mouse trail vectors. |
| **Dynamic UI/UX** | CSS-in-JS & Glassmorphism | Generates a draggable overlay with `backdrop-filter: blur(8px)` and modular layout. |
| **System Fingerprinting** | `navigator.userAgent` | Extracts OS, Browser type, Language, and screen resolution parameters natively. |
| **Data Extraction** | Blob API & Base64 URI | Compiles in-memory arrays into downloadable `.csv` and `.jpg` payload files. |

## 📊 Telemetry & Data Dictionary

The module captures micro-interactions and calculates latency between node interactions, exporting the compiled dataset as a CSV. 

* **Time Elapsed (ms):** Absolute time since the tracking session was initiated.
* **Latency (ms):** Delta time between the current and the immediately preceding event.
* **X / Y Coordinates:** Cartesian coordinates mapping the exact cursor position on the viewport.
* **Action Type:** Categorical mapping of the event trigger (e.g., `mousemove`, `mousedown`, `keydown`).
* **Target Details:** Deep DOM node extraction, capturing tag names, IDs, classes, keypress values, and button text truncations.

## 🚀 Execution Flow

1. **Initialization:** The script builds the floating UI and evaluates the client's system environment (OS, Browser, Res).
2. **Tracking State:** Upon clicking "Start Scan", the canvas initializes a dark green background and plots a concentric radar layout. Event listeners are attached to the `document` root.
3. **Data Plotting:** 
   * Movements generate a continuous vector line (`lineTo`, `stroke`).
   * Clicks render as radial gradient bursts (Yellow/Orange).
   * Keystrokes render as randomized HSL colored nodes.
4. **Termination & Export:** Clicking "Stop Scan" detaches event listeners, removes the UI temporarily, and triggers automatic downloads of the `dragon_radar_data.csv` and `dragon_radar_map.jpg` files.
