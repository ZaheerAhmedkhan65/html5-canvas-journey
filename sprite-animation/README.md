# Sprite Animation

A hands-on learning project that demonstrates how **sprite sheet animations** work on the web — built with vanilla JavaScript, the HTML5 `<canvas>` API, and `requestAnimationFrame()`. No libraries, no frameworks — just the fundamentals of 2D game animation.

**🎮 Live Demo:** [https://zaheerahmedkhan65.github.io/html5-canvas-journey/sprite-animation/](https://zaheerahmedkhan65.github.io/html5-canvas-journey/sprite-animation/)

> 🗂️ Part of the [HTML5 Canvas Journey](https://github.com/ZaheerAhmedkhan65/html5-canvas-journey) — a collection of mini projects for learning Canvas game development.

---

## 📖 What Is Sprite Animation?

In classic 2D games, a character's every movement (idle, run, jump, etc.) is drawn as a sequence of individual images called **frames**. Instead of storing each frame as a separate file, all frames are packed into a single image called a **sprite sheet**.

```
┌──────┬──────┬──────┬──────┬──────┐
│ f1   │ f2   │ f3   │ f4   │ f5   │   ← one animation row
├──────┼──────┼──────┼──────┼──────┤
│ f1   │ f2   │ ...                 │   ← another animation row
└──────┴──────┴──────┴──────┴──────┘
```

To animate, the code:
1. Shows **one frame at a time** by "cutting" a small rectangle out of the sheet (`ctx.drawImage` with source coordinates).
2. **Cycles through frames** quickly (controlled by `requestAnimationFrame` + a frame-stagger counter) — fast frame swapping tricks the eye into seeing smooth motion, just like a flipbook.

This project uses the famous **Shadow Dog** sprite sheet — a 12-row × 7-column grid where each row is a different animation (idle, jump, run, dizzy...), each cell being 575×523 px.

## 🧠 Key Concepts Learned

| Concept | Where | Why it matters |
|---|---|---|
| Canvas 2D rendering | `script.js` | Drawing images with `ctx.drawImage(img, sx, sy, sw, sh, dx, dy, dw, dh)` — the 9-argument version lets you crop a sub-rectangle from the source image. |
| `requestAnimationFrame()` | `animate()` | The browser-native game loop, synced to the display refresh rate (~60 FPS). |
| Frame staggering | `staggerFrames = 5` | We don't advance the frame on *every* tick (that would be 60 FPS — way too fast). Instead we advance once every 5 ticks: `Math.floor(gameFrame / staggerFrames)`. |
| Modulo looping | `% frames.length` | `position = ... % length` makes the animation loop seamlessly back to frame 0. |
| Data-driven design | `animationStates[]` | Each animation's frame count is declared in one config array; frame coordinates are precomputed into a lookup map (`spriteAnimations`). |
| DOM ↔ game state | dropdown listener | The `<select>` simply changes the `playerState` variable — the animation loop reads it on every tick. Clean decoupling. |

## 🗂️ Project Structure

```
sprite-animation/
├── index.html      # Canvas + animation dropdown UI
├── style.css       # Centering the canvas & styling controls
├── script.js       # The animation engine (~90 lines)
├── shadow_dog.png  # The sprite sheet (12 animations × 7 frames)
└── docs.txt        # Study notes
```

## 🎬 Available Animations

The dropdown lets you switch between all 10 animations extracted from the sheet:

| Animation | Frames |
|---|---|
| Idle | 7 |
| Jump / Fall | 7 each |
| Run | 9 |
| Dizzy | 11 |
| Sit | 5 |
| Roll / Bite | 7 each |
| KO | 12 |
| Get Hit | 4 |

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

Then visit `http://localhost:8080`.

## 💻 How It Works — Code Walkthrough

**1. Set up the canvas and load the sprite sheet:**

```js
const canvas = document.getElementById("canvas1");
const ctx = canvas.getContext("2d");
const playerImage = new Image();
playerImage.src = "shadow_dog.png";
const spriteWidth = 575;   // width of one frame in the sheet
const spriteHeight = 523;  // height of one frame in the sheet
```

**2. Precompute frame coordinates for every animation row:**

```js
animationStates.forEach((state, index) => {
    let frames = { loc: [] };
    for (let j = 0; j < state.frames; j++) {
        frames.loc.push({ x: j * spriteWidth, y: index * spriteHeight });
    }
    spriteAnimations[state.name] = frames;
});
// e.g. spriteAnimations["run"].loc → [{x:0,y:1746}, {x:575,y:1746}, ...]
```

**3. The game loop — crop one frame, draw it, repeat:**

```js
function animate() {
    ctx.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT);
    let position = Math.floor(gameFrame / staggerFrames)
                 % spriteAnimations[playerState].loc.length;
    ctx.drawImage(playerImage,
        position * spriteWidth, spriteAnimations[playerState].loc[position].y,
        spriteWidth, spriteHeight,      // source rect (crop from sheet)
        0, 0, spriteWidth, spriteHeight // destination rect (on canvas)
    );
    gameFrame++;
    requestAnimationFrame(animate);
}
animate();
```

## 🔧 Things to Try (Learning Exercises)

1. **Change the speed** — set `staggerFrames` to `2` (faster) or `10` (slower) and watch the effect.
2. **Add a new animation** — count the frames in a new row of the sheet, add an entry to `animationStates` and an `<option>` in `index.html`.
3. **Random animation** — replace the dropdown logic with code that randomly switches states every few seconds.
4. **Adjustable speed UI** — add a range `<input>` slider that sets `staggerFrames` live.
5. **Pixel-perfect cropping** — some sprite sheets have padding/offsets; try tweaking the source `x`/`y` and see what happens.

## 🛠️ Tech Stack

- **HTML5 Canvas** — 2D rendering context
- **Vanilla JavaScript** — no dependencies
- **CSS** — simple absolute positioning

## 📚 Resources

- [MDN: Canvas Tutorial](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial)
- [MDN: requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/Window/requestAnimationFrame)
- Sprite sheet by [bevouliin.com](https://bevouliin.com/) (free Shadow Dog character)

---

Built as part of a hands-on HTML5 games & Canvas learning journey. 🎮

