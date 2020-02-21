---
layout: post
title: Game Loop in HTML5 Canvas
date: 2020-02-21 21:57 +0800
categories: gamedev html5 canvas gameloop javascript
---

One of my development goals for 2020 is to start learning game development. Instead of diving straight into popular game development platforms/engines such as [Unity](https://unity.com/), I decided to start small and try my hand at HTML5 Canvas. The Canvas APIs are a set of relatively simple and straightforward APIs, suitable for beginners to program simple 2D games. I think it is a good platform for beginners, and also has potential to grow into a big project, given that browser technology is available on almost all modern devices. Instead of using a library/framework such as [Phaser](http://phaser.io/) or [PixiJS](https://www.pixijs.com/), I decided to start learning the core concepts by writing from scratch!

The topic for today is one of the most basic but also one of the most important concepts of game development, which is the **game loop**.

### What is a game loop

The game loop can be thought of simply as a infinite while loop that is always running when your game is running. The most basic game loop can be broken down into three main steps:

```
while(game is running) {
    accept and process user inputs;
    update game state;
    render game onto display;
}
```

At each iteration:

-   **accept and process user inputs**: anything from registering keyboard presses, mouse clicks, mouse movements, etc. are all recorded and process here as inputs to control/manipulate the game state
-   **update game state**: at this stage, we are updating the game state based on user inputs (or could also be just a continuation of the game on its own), for instance if the spacebar was pressed, we update the state of the player entity to make him jump.
-   **render game onto display**: finally, we render (or draw) the state of the game onto the display to show it to the user, and then get ready to do it all over again!

### FPS???

**Frames per second**, or FPS, is a term that is regularly thrown around amongst gamers to _"benchmark"_ the ability of their PCs to play certain games. Usually, the higher the FPS, the smoother the gameplay, and thus the better the gaming experience for the user. And this is why many gamers nowadays are always looking to upgrade their machine to squeeze as much FPS as possible out of it for their favourite games.

In game development terms, each iteration of the game loop can be thought of as a **frame**. The number of times that your game loop iterates in 1 second, is exactly the number of FPS that you get out of your game. Because each frame is essentially a still image of the game state at that point in time, the more FPS you put out, the smoother your game can appear to the user. The absolute minimum FPS for the human eye to perceive as motion is about 10-12 FPS. Most games tend to aim for a stable 60 FPS, which is the widely accepted minimal ideal frame rate for most modern AAA titles. The more dedicated gamers will try to go for 100 FPS or higher, which proves to be a much smoother experience for gaming.

> Sidenote: Even if your game and PC is able to render at high framerates, it is also important to keep in mind that the **refresh rate** of your monitor matters just as much. A monitor with a 60 Hz refresh rate will only display the game at 60 FPS even if your PC is spitting out 200 frames every second. Most modern basic monitors and displays now support a minimum 60 Hz refresh rate, and in fact we see many 100 Hz and above (typically 144 Hz) going for decent prices in recent years.

60 frames per second translates to about 16.6ms per frame, or per iteration of the game loop. Every 16.6ms, your game program has to process inputs, update game state and draw the game on the screen. That sounds like a lot of work in only 16.6ms, doesn't it! This is why it is important that in game programming, your code is efficient, you update the game state fast, so that you are able to render out the game in time.

### The game loop in JavaScript

For HTML5 Canvas, we are fortunate that we are provided with a function by the browser to help us with the game loop. This function is `requestAnimationFrame`, which accepts a callback function as an argument that will be executed the next time the browser is ready to display the next frame to the screen. For most modern browsers on desktop/mobile that support this API, we should expect this to be called every 16.6ms or so, which corresponds to 60 FPS.

You can test this by writing a few lines of JavaScript:

```javascript
let lastTime = 0;

function gameLoop(timestamp) {
    const deltaTime = timestamp - lastTime;
    lastTime = timestamp;
    console.log(deltaTime);

    requestAnimationFrame(gameLoop);
}

requestAnimationFrame(gameLoop);
```

You should see that `deltaTime` is approximately 17~16ms, which tells us the amount of time that has passed before the next frame is requested.

Here's an example of a simple game loop code that uses `requestAnimationFrame`:

```javascript
function gameLoop() {
    getAndProcessUserInputs();
    updateGameState();
    render();

    requestAnimationFrame(gameLoop);
}

requestAnimationFrame(gameLoop);
```

The game loop is the core of every game ever developed and it is important for game developers to understand and grasp this concept deeply in order to produce quality code for quality games.