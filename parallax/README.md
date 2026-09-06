# 🌄 Parallax Scrolling Backgrounds with HTML5 Canvas

A hands-on learning project demonstrating the **parallax scrolling effect** — the illusion of depth in 2D games where background layers move at different speeds. Built with vanilla JavaScript and the HTML5 `<canvas>` API, with **5 stacked layers** drawn each frame and a live slider to control scroll speed.

**🎮 Live Demo:** [https://zaheerahmedkhan65.github.io/html5-canvas-journey/parallax/](https://zaheerahmedkhan65.github.io/html5-canvas-journey/parallax/)

> 🗂️ Part of the [HTML5 Canvas Journey](https://github.com/ZaheerAhmedkhan65/html5-canvas-journey) — a collection of mini projects for learning Canvas game development.

---

## 📖 What Is Parallax Scrolling?

**Parallax** is the visual effect where objects farther away appear to move more slowly than objects nearby. Games fake depth by stacking several full-width background images (**layers**) — from far sky to near foreground — and scrolling each one at a *different fraction* of the game speed:

```
Layer 5 (foreground)  ████████████████  speed × 1.0  ← fastest, closest
Layer 4               ████████████████  speed × 0.8
Layer 3               ████████████████  speed × 0.6
Layer 2               ████████████████  speed × 0.4
Layer 1 (far sky)     ████████████████  speed × 0.2  ← slowest, farthest
```

Each layer's image is wider than the canvas (2400px image on an 800px canvas), and it's drawn **twice side by side**. When a layer scrolls fully off-screen to the left, its `x` resets to `0` — the duplicate copy makes the loop seamless, so the scrolling never visibly "jumps".

## 🧠 Key Concepts Learned

| Concept | Where | Why it matters |
|---|---|---|
| Layered rendering order | `gameObjects` array | Layers are updated/drawn **back to front** (layer 1 first) — canvas paints over, so draw order = visual stacking order. |
| Speed modifiers | `speedModifier: 0.2 → 1` | Each layer's actual speed = `gameSpeed * speedModifier`. Small values = distant/slow, large = close/fast. This single number creates the depth illusion. |
| Seamless infinite scroll | `Layer.update()` + `draw()` | Draw the image at `x` and at `x + width`; reset `x` to 0 once it reaches `-width`. Two draw calls = perfectly looping background. |
| Recomputing speed per frame | `this.speed = gameSpeed * ...` inside `update()` | Lets the slider change `gameSpeed` **live** mid-animation — all layers react instantly. |
| OOP with ES6 classes | `class Layer` | One reusable class encapsulates position, image, speed, update & draw — instantiated 5 times with different configs. |
| DOM ↔ game state | `slider` listener | The `<input type="range">` writes to `gameSpeed`; the render loop reads it. Clean decoupling of UI and rendering. |

## 🗂️ Project Structure

```
parallax/
├── index.html    # Canvas + game-speed slider UI
├── style.css     # Centering the canvas, slider styling
├── script.js     # The Layer class + animation loop (~80 lines)
├── layer-1.png   # Farthest layer (sky / mountains)  — × 0.2 speed
├── layer-2.png   #                                     — × 0.4
├── layer-3.png   #                                     — × 0.6
├── layer-4.png   #                                     — × 0.8
└── layer-5.png   # Foreground (trees, etc.)          — × 1.0
```


## 🚀 Getting Started

No build step needed — it's pure HTML/CSS/JS.

**Option 1: Open directly** — just open `index.html` in your browser.

**Option 2: Run a local server** (recommended, avoids any file:// quirks):

```bash
# Python
python3 -m http.server 8080

# or Node
npx serve
```

Then visit `http://localhost:8080/parallax/`.

**Controls:** drag the **Game Speed** slider (0–20) to speed up, slow down, or stop the scrolling in real time.

## 💻 How It Works — Code Walkthrough

**1. Load the 5 layer images and set up the canvas:**

```js
const canvas = document.getElementById('canvas1');
const ctx = canvas.getContext('2d');
const CANVAS_WIDTH = canvas.width = 800;
const CANVAS_HEIGHT = canvas.height = 700;
let gameSpeed = 4; // base scroll speed, changed by the slider
```

**2. One `Layer` class — each instance holds one background image:**

```js
class Layer {
    constructor(image, speedModifier) {
        this.x = 0;
        this.y = 0;
        this.width = 2400;   // image is wider than the 800px canvas
        this.height = 700;
        this.image = image;
        this.speedModifier = speedModifier;
        this.speed = gameSpeed * this.speedModifier;
    }
    update() {
        this.speed = gameSpeed * this.speedModifier; // react to slider live
        if (this.x <= -this.width) this.x = 0;       // seamless loop reset
        this.x = Math.floor(this.x - this.speed);
    }
    draw() {
        // draw the image twice, side by side, for an endless strip
        ctx.drawImage(this.image, this.x, this.y, this.width, this.height);
        ctx.drawImage(this.image, this.x + this.width, this.y, this.width, this.height);
    }
}
```

**3. Stack the layers back-to-front and animate:**

```js
const layer1 = new Layer(backgroundLayer1, 0.2); // far sky
const layer2 = new Layer(backgroundLayer2, 0.4);
const layer3 = new Layer(backgroundLayer3, 0.6);
const layer4 = new Layer(backgroundLayer4, 0.8);
const layer5 = new Layer(backgroundLayer5, 1);   // foreground

const gameObjects = [layer1, layer2, layer3, layer4, layer5];

function animate() {
    ctx.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT);
    gameObjects.forEach(object => {
        object.update();
        object.draw();
    });
    requestAnimationFrame(animate);
}
animate();
```

## 🔧 Things to Try (Learning Exercises)

1. **Reverse direction** — change `this.x = Math.floor(this.x - this.speed)` to `+ this.speed` and adjust the reset condition so layers scroll rightward.
2. **Mouse-controlled speed** — replace the slider with a `mousemove` listener that sets `gameSpeed` from cursor position.
3. **Vertical parallax** — modify `update()`/`draw()` to scroll layers vertically (e.g. for a falling/flappy-style game).
4. **More layers** — add a 6th foreground layer with `speedModifier: 1.2` — faster than the "closest" layer for extra depth.
5. **Pause key** — toggle `gameSpeed` between 0 and its previous value when pressing Space.
6. **Fix the seam** — zoom in while running; if you notice a 1px flicker at the loop point, it's the `Math.floor()` rounding — try removing it and compare.

## 🛠️ Tech Stack

- **HTML5 Canvas** — 2D rendering context
- **Vanilla JavaScript** — ES6 classes, no dependencies
- **CSS** — absolute positioning + range slider styling

## 📚 Resources

- [MDN: Canvas Tutorial](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial)
- [MDN: requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/Window/requestAnimationFrame)
- [Wikipedia: Parallax](https://en.wikipedia.org/wiki/Parallax) — the real-world physics behind the effect

---

Built as part of a hands-on HTML5 games & Canvas learning journey. 🎮

└── layer-5.png   # Foreground (trees, etc.)          — × 1.0
```
