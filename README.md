# MA1805-Mini-Game

1. Why I Chose This Project
I chose to build an endless runner game because it sits at a perfect intersection of creativity and technical challenge. Games are one of the most engaging forms of software — they require real-time logic, responsive controls, smooth animation, and a feedback loop that keeps a user hooked. Building one from scratch forces you to think about physics, collision detection, game state management, and visual design all at once.
I also wanted to work with p5.js, a JavaScript library designed for creative coding. Rather than relying on a game engine like Unity or Phaser that abstracts away the difficult parts, p5.js gives me full control while still providing a clean drawing API. Every frame of animation, every physics calculation, and every visual effect in this project is written by hand — which makes it a much richer learning experience.
The endless runner genre specifically appealed to me because it is deceptively simple on the surface — run, jump, avoid — yet surprisingly deep to implement properly. Features like difficulty scaling, double jumping, particle effects, and a polished HUD all need to be built from the ground up, making it an ideal project to demonstrate a range of programming concepts.

2. Project Overview
Endless Run is a browser-based arcade game where the player controls a neon-styled character that automatically moves forward through a scrolling landscape. The objective is to survive as long as possible by jumping over randomly generated obstacles. The game tracks the player's score in real time and increases the speed as the score climbs, making it progressively harder.
The entire project is a single self-contained HTML file. No build tools, no frameworks, no server — just open it in a browser and play.

3. Technologies Used
p5.js (v1.9.4)
p5.js is the primary library powering the game. It is a JavaScript library inspired by Processing, designed to make coding accessible for artists, designers, and beginners while remaining powerful enough for complex interactive applications.
p5.js provides:
setup() — runs once at startup to initialise the canvas and game state
draw() — called 60 times per second, acting as the game loop
Primitive drawing functions: rect(), ellipse(), fill(), stroke(), text()
Input hooks: keyPressed(), mousePressed(), touchStarted()
Math helpers: random(), sin(), floor(), TWO_PI
p5.js is loaded from the Cloudflare CDN — no installation required.
HTML5 Canvas (via p5.js)
p5.js renders everything onto an HTML5 <canvas> element. The canvas gives pixel-level control over every visual on screen. The game accesses the underlying canvas rendering context directly through drawingContext whenever p5.js does not expose a feature natively, such as glow effects using shadowBlur.
Vanilla JavaScript (ES6+)
All game logic is written in modern JavaScript. Key language features used include:
Arrow functions and array methods (.map, .filter, .forEach) for clean, readable code
Destructuring and spread syntax for state management
const / let block scoping
Template literals and Array.from() for generating initial data

4. Game Features
Double Jump
The player gets two jumps before they must land again. This is tracked with a jumps counter on the player object. Each time the jump function fires, it checks jumps < maxJumps before applying an upward velocity. When the player lands (y >= GROUND_Y), jumps resets to zero.
if (p.jumps < p.maxJumps) { p.vy = -13.5; p.jumps++; }
Physics Simulation
Gravity is simulated by adding a constant downward acceleration to the player's vertical velocity every frame. The player's y-position is then updated by that velocity. When the player hits the ground, their velocity is zeroed and their jump counter resets.
p.vy += 0.7;   // gravity
p.y  += p.vy;  // apply velocity
if (p.y >= GROUND_Y) { p.y = GROUND_Y; p.vy = 0; p.jumps = 0; }
Progressive Difficulty
The game speed starts at 4.5 pixels per frame and increases by 0.4 for every 300 points scored. Obstacle spawn intervals also tighten as the score grows, ensuring the game always feels challenging without becoming instantly impossible.
state.speed = 4.5 + floor(state.score / 300) * 0.4;
Randomised Obstacles
Four distinct obstacle shapes are defined (tall-narrow, short-wide, very tall, very wide). Each time an obstacle spawns, one is chosen at random. This prevents the player from memorising a pattern and forces them to react in the moment.
Particle Effects
Particles are spawned when the player jumps (burst from feet) and when the player dies (explosion). Each particle has its own position, velocity, size, colour, and lifetime. Every frame, particles move, fall due to simulated gravity, and fade out. Dead particles are removed from the array with .filter().
Motion Trail
The last 8 positions of the player are stored each frame. When drawing, these past positions are rendered as faded, shrinking ghost rectangles behind the player. The further back in history, the more transparent the ghost — giving a sense of speed and momentum.
Twinkling Star Field
60 stars are randomly placed in the sky on startup. Each star has an independent twinkle phase driven by sin(), so they shimmer at different rates without any timer management.
Glow Effects
The player and obstacles emit a soft glow using the HTML5 Canvas shadowBlur and shadowColor properties, accessed via p5.js's drawingContext. The player glows purple; obstacles glow red.
Idle Bob Animation
While standing on the ground, the player gently bobs up and down using sin() applied to the draw position. This gives the character life even when nothing else is happening.
const bobY = p.y >= GROUND_Y ? sin(state.frame * 0.25) * 1.5 : 0;
Heads-Up Display (HUD)
The current score and the all-time best score are rendered directly onto the canvas every frame using p5.js's text() function. The best score persists across rounds for the duration of the browser session.
Touch / Mobile Support
The game is fully playable on touch devices. The touchStarted() hook fires the same jump function as the spacebar, so the game works on phones and tablets without any extra configuration.

5. Code Structure
The code is organised into clearly separated sections:
Constants & colours — all magic numbers and hex colour values defined at the top
initState() — returns a fresh game state object; called at startup and on restart
doJump() — handles input, triggers jump or restarts the game if dead
spawnParticles() — adds new particle objects to the state
spawnObstacle() — picks a random obstacle type and pushes it into the array
gameUpdate() — all physics, spawning, collision detection, and cleanup; called once per draw()
draw() — all rendering; called by p5.js at ~60fps
drawHUD(), drawStartScreen(), drawGameOver() — isolated drawing helpers for UI overlays
Keeping update and draw completely separate is a standard game architecture pattern (sometimes called the game loop pattern). It makes the code easier to reason about and debug — rendering never changes state, and logic never draws anything.

6. Collision Detection
Collision uses Axis-Aligned Bounding Box (AABB) detection — the simplest and fastest method for rectangular objects. The player and each obstacle are treated as rectangles. If the rectangles overlap on both axes, a collision is registered.
Small inward offsets (6px and 4px) are applied to both rectangles before checking. This creates a slight forgiveness zone so the game does not feel unfair — a very near-miss does not kill the player.
if (p.x + p.w - 6 > o.x + 4 && p.x + 6 < o.x + o.w - 4 &&
    p.y + p.h - 4 > o.y + 4 && p.y + 4 < o.y + o.h) { ... }

7. How to Run
The project is a single HTML file with no dependencies to install.
Open the file in any modern web browser (Chrome, Firefox, Safari, Edge)
Or open the project folder in VS Code, install the Live Server extension, right-click the file and select Open with Live Server
Controls:
Space bar or Up Arrow — jump (press twice for double jump)
Mouse click or screen tap — jump
After dying, press Space or tap to restart

8. Reflection
This project taught me how a real-time game loop works in practice — the difference between a simulation that runs at a fixed logical speed versus one tied to frame rate, how to manage arrays of moving objects efficiently, and how small design decisions like a forgiveness hitbox or a motion trail dramatically affect how a game feels to play.
Building it in p5.js rather than a full game engine meant I had to implement everything myself: gravity, collision, spawning logic, particle systems, and UI. That constraint made it a far more educational experience than dropping prefab components into a scene.
If I were to extend this project further, I would add: a sound system using the Web Audio API, animated sprite sheets for the character, a high-score leaderboard stored in localStorage, and power-ups such as a shield or slow-motion that spawn randomly alongside obstacles.
