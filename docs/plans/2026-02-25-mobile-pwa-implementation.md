# Mobile PWA Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Transform the desktop snake game into a mobile-friendly PWA with touch controls and offline support.

**Architecture:** Single-page application with mobile-first CSS. PWA features via manifest.json and Service Worker. Existing game logic preserved, only UI/UX layer modified.

**Tech Stack:** HTML5 Canvas, Vanilla JavaScript, CSS3, Service Worker API, Web App Manifest

---

## Task 1: Create PWA Manifest File

**Files:**
- Create: `manifest.json`

**Step 1: Create manifest.json**

```json
{
  "name": "贪吃蛇游戏",
  "short_name": "贪吃蛇",
  "description": "经典贪吃蛇手机游戏",
  "start_url": "./index.html",
  "display": "standalone",
  "background_color": "#667eea",
  "theme_color": "#764ba2",
  "orientation": "portrait",
  "icons": [
    {
      "src": "icons/icon-192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "any maskable"
    },
    {
      "src": "icons/icon-512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any maskable"
    }
  ]
}
```

**Step 2: Verify file created**

Run: `cat /home/hmj/myopencode/snake-game/manifest.json`
Expected: JSON content displayed

**Step 3: Commit**

```bash
cd /home/hmj/myopencode/snake-game
git add manifest.json
git commit -m "feat: add PWA manifest file"
```

---

## Task 2: Create Service Worker

**Files:**
- Create: `sw.js`

**Step 1: Create sw.js**

```javascript
const CACHE_NAME = 'snake-game-v1';
const ASSETS = [
  './',
  './index.html',
  './manifest.json'
];

// Install event - cache assets
self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then((cache) => cache.addAll(ASSETS))
      .then(() => self.skipWaiting())
  );
});

// Activate event - clean old caches
self.addEventListener('activate', (event) => {
  event.waitUntil(
    caches.keys().then((cacheNames) => {
      return Promise.all(
        cacheNames
          .filter((name) => name !== CACHE_NAME)
          .map((name) => caches.delete(name))
      );
    }).then(() => self.clients.claim())
  );
});

// Fetch event - serve from cache, fallback to network
self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request)
      .then((response) => response || fetch(event.request))
  );
});
```

**Step 2: Verify file created**

Run: `cat /home/hmj/myopencode/snake-game/sw.js`
Expected: Service worker code displayed

**Step 3: Commit**

```bash
cd /home/hmj/myopencode/snake-game
git add sw.js
git commit -m "feat: add Service Worker for offline support"
```

---

## Task 3: Create App Icons

**Files:**
- Create: `icons/icon-192.png`
- Create: `icons/icon-512.png`

**Step 1: Create icons directory**

Run: `mkdir -p /home/hmj/myopencode/snake-game/icons`
Expected: Directory created

**Step 2: Create SVG icon (will be converted or used directly)**

Create `icons/icon.svg`:

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512">
  <defs>
    <linearGradient id="bg" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#667eea"/>
      <stop offset="100%" style="stop-color:#764ba2"/>
    </linearGradient>
  </defs>
  <rect width="512" height="512" rx="80" fill="url(#bg)"/>
  <text x="256" y="320" font-size="280" text-anchor="middle" fill="white">🐍</text>
</svg>
```

**Step 3: Create PNG icons using base64 encoded simple icons**

Since we can't run image conversion tools, create simple placeholder PNGs or use inline SVG data URIs in manifest.

Alternative: Update manifest to use SVG icon (supported in modern browsers):

```json
{
  "icons": [
    {
      "src": "icons/icon.svg",
      "sizes": "any",
      "type": "image/svg+xml",
      "purpose": "any"
    }
  ]
}
```

**Step 4: Commit**

```bash
cd /home/hmj/myopencode/snake-game
git add icons/
git commit -m "feat: add app icons"
```

---

## Task 4: Update index.html - PWA Meta Tags

**Files:**
- Modify: `index.html` (head section)

**Step 1: Add PWA meta tags to head**

In the `<head>` section, after the viewport meta tag, add:

```html
<!-- PWA Meta Tags -->
<link rel="manifest" href="manifest.json">
<meta name="theme-color" content="#764ba2">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="贪吃蛇">
<link rel="apple-touch-icon" href="icons/icon-192.png">
```

**Step 2: Add Service Worker registration**

Before the closing `</script>` tag at the end of the file, add:

```javascript
// Register Service Worker
if ('serviceWorker' in navigator) {
  window.addEventListener('load', () => {
    navigator.serviceWorker.register('./sw.js')
      .then((registration) => {
        console.log('SW registered:', registration.scope);
      })
      .catch((error) => {
        console.log('SW registration failed:', error);
      });
  });
}
```

**Step 3: Commit**

```bash
cd /home/hmj/myopencode/snake-game
git add index.html
git commit -m "feat: add PWA meta tags and service worker registration"
```

---

## Task 5: Rewrite CSS - Mobile-First Base Styles

**Files:**
- Modify: `index.html` (style section)

**Step 1: Replace entire style section with mobile-first CSS**

Replace the `<style>` section with:

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    min-height: 100vh;
    min-height: 100dvh;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Arial, sans-serif;
    padding: 10px;
    overflow-x: hidden;
}

h1 {
    color: white;
    font-size: 20px;
    margin: 10px 0;
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
}

#gameContainer {
    background: white;
    padding: 12px;
    border-radius: 12px;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 360px;
}

#gameCanvas {
    display: block;
    width: 100%;
    max-width: 340px;
    height: auto;
    aspect-ratio: 1;
    margin: 0 auto;
    border: 3px solid #333;
    border-radius: 8px;
    background: #f0f0f0;
    touch-action: none;
}

#gameCanvas.effect-slow {
    border-color: #9b59b6 !important;
    box-shadow: 0 0 15px #9b59b6;
}

#gameCanvas.effect-speed {
    border-color: #00bcd4 !important;
    box-shadow: 0 0 15px #00bcd4;
}

#controls {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-top: 12px;
    justify-content: center;
    align-items: center;
}

button {
    padding: 10px 16px;
    font-size: 14px;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: all 0.2s ease;
    font-weight: bold;
    touch-action: manipulation;
    -webkit-tap-highlight-color: transparent;
}

#startBtn {
    background: #4CAF50;
    color: white;
}

#startBtn:active {
    background: #45a049;
    transform: scale(0.95);
}

#pauseBtn {
    background: #ff9800;
    color: white;
}

#pauseBtn:active {
    background: #e68900;
    transform: scale(0.95);
}

#score {
    font-size: 14px;
    font-weight: bold;
    color: #333;
    padding: 8px 12px;
}

#gameOver {
    display: none;
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.85);
    color: white;
    padding: 30px;
    border-radius: 0;
    text-align: center;
    z-index: 1000;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

#gameOver h2 {
    font-size: 28px;
    margin-bottom: 15px;
}

#restartBtn {
    background: #2196F3;
    color: white;
    margin-top: 20px;
    padding: 12px 30px;
    font-size: 16px;
}

#restartBtn:active {
    background: #0b7dda;
    transform: scale(0.95);
}

.instructions {
    display: none;
}

/* Mobile Controls - Default (shown on mobile) */
.mobile-controls {
    display: grid;
    grid-template-columns: repeat(3, 55px);
    grid-template-rows: repeat(3, 55px);
    gap: 8px;
    margin-top: 15px;
    justify-content: center;
}

.control-btn {
    width: 55px;
    height: 55px;
    border-radius: 12px;
    background: rgba(255, 255, 255, 0.95);
    border: 2px solid #333;
    font-size: 22px;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    user-select: none;
    -webkit-user-select: none;
    touch-action: manipulation;
    -webkit-tap-highlight-color: transparent;
    transition: transform 0.1s ease;
}

.control-btn:active {
    background: rgba(200, 200, 200, 0.95);
    transform: scale(0.9);
}

.control-btn.empty {
    visibility: hidden;
}

/* Desktop Styles */
@media (min-width: 601px) {
    body {
        justify-content: center;
        padding: 20px;
    }

    h1 {
        font-size: 28px;
        margin-bottom: 20px;
    }

    #gameContainer {
        padding: 20px;
        max-width: 450px;
    }

    #gameCanvas {
        max-width: 400px;
        border-radius: 5px;
    }

    #controls {
        gap: 15px;
        margin-top: 20px;
    }

    button {
        padding: 10px 25px;
        font-size: 16px;
    }

    button:hover {
        transform: translateY(-2px);
    }

    #score {
        font-size: 20px;
    }

    .instructions {
        display: block;
        color: white;
        margin-top: 20px;
        text-align: center;
        background: rgba(0, 0, 0, 0.2);
        padding: 15px;
        border-radius: 8px;
    }

    .instructions p {
        margin: 5px 0;
        font-size: 14px;
    }

    .mobile-controls {
        display: none;
    }

    #gameOver {
        position: absolute;
        padding: 40px;
        border-radius: 12px;
    }

    #gameOver h2 {
        font-size: 36px;
    }
}

/* Small phones */
@media (max-width: 360px) {
    #gameContainer {
        padding: 10px;
    }

    #gameCanvas {
        max-width: 280px;
    }

    .mobile-controls {
        grid-template-columns: repeat(3, 48px);
        grid-template-rows: repeat(3, 48px);
        gap: 6px;
    }

    .control-btn {
        width: 48px;
        height: 48px;
        font-size: 18px;
    }

    button {
        padding: 8px 12px;
        font-size: 13px;
    }

    #score {
        font-size: 13px;
    }
}
```

**Step 2: Commit**

```bash
cd /home/hmj/myopencode/snake-game
git add index.html
git commit -m "feat: rewrite CSS with mobile-first approach"
```

---

## Task 6: Update HTML Structure

**Files:**
- Modify: `index.html` (body section)

**Step 1: Update the body HTML structure**

Replace the body content with:

```html
<body>
    <h1>🐍 贪吃蛇</h1>

    <div id="gameContainer">
        <canvas id="gameCanvas" width="400" height="400"></canvas>
        <div id="controls">
            <button id="startBtn">开始游戏</button>
            <button id="pauseBtn">暂停</button>
            <div id="score">得分: 0</div>
        </div>
    </div>

    <div id="gameOver">
        <h2>游戏结束!</h2>
        <p id="finalScore">最终得分: 0</p>
        <button id="restartBtn">重新开始</button>
    </div>

    <div class="instructions">
        <p>🎮 使用方向键 ↑ ↓ ← → 或 WASD 控制蛇的移动</p>
        <p>⭕ 圆形=10分 | ⬜ 方块=25分 | 🔺 三角=5分(减速) | 🔷 菱形=15分(加速)</p>
        <p>⚠️ 不要撞到墙壁或自己的身体</p>
    </div>

    <!-- Mobile Controls -->
    <div class="mobile-controls" id="mobileControls">
        <div class="control-btn empty"></div>
        <div class="control-btn" id="upBtn">⬆️</div>
        <div class="control-btn empty"></div>
        <div class="control-btn" id="leftBtn">⬅️</div>
        <div class="control-btn empty"></div>
        <div class="control-btn" id="rightBtn">➡️</div>
        <div class="control-btn empty"></div>
        <div class="control-btn" id="downBtn">⬇️</div>
        <div class="control-btn empty"></div>
    </div>

    <script>
    // ... (game script will be updated in next task)
    </script>
</body>
```

**Step 2: Commit**

```bash
cd /home/hmj/myopencode/snake-game
git add index.html
git commit -m "feat: update HTML structure for mobile layout"
```

---

## Task 7: Update JavaScript - Touch and PWA Features

**Files:**
- Modify: `index.html` (script section)

**Step 1: Add touch improvements to JavaScript**

Keep all existing game logic, but update the touch handling section. Find the existing touch event handlers and replace with:

```javascript
// Enhanced Touch Controls
let touchStartX = 0;
let touchStartY = 0;
const minSwipeDistance = 30;

// Touch start - works on entire document for mobile
document.addEventListener('touchstart', (e) => {
    touchStartX = e.touches[0].clientX;
    touchStartY = e.touches[0].clientY;
}, { passive: true });

// Touch end - swipe detection
document.addEventListener('touchend', (e) => {
    if (!gameRunning || gamePaused) return;

    const touchEndX = e.changedTouches[0].clientX;
    const touchEndY = e.changedTouches[0].clientY;

    const deltaX = touchEndX - touchStartX;
    const deltaY = touchEndY - touchStartY;

    // Ignore small movements
    if (Math.abs(deltaX) < minSwipeDistance && Math.abs(deltaY) < minSwipeDistance) {
        return;
    }

    // Determine swipe direction
    if (Math.abs(deltaX) > Math.abs(deltaY)) {
        // Horizontal swipe
        if (deltaX > 0) {
            handleDirectionChange(1, 0); // Right
        } else {
            handleDirectionChange(-1, 0); // Left
        }
    } else {
        // Vertical swipe
        if (deltaY > 0) {
            handleDirectionChange(0, 1); // Down
        } else {
            handleDirectionChange(0, -1); // Up
        }
    }
}, { passive: true });

// Mobile control buttons
const upBtn = document.getElementById('upBtn');
const downBtn = document.getElementById('downBtn');
const leftBtn = document.getElementById('leftBtn');
const rightBtn = document.getElementById('rightBtn');

function handleDirectionChange(newDx, newDy) {
    if (!gameRunning || gamePaused) return;

    if (newDx === 0 && newDy === -1 && dy !== 1) {
        dx = 0;
        dy = -1;
    } else if (newDx === 0 && newDy === 1 && dy !== -1) {
        dx = 0;
        dy = 1;
    } else if (newDx === -1 && newDy === 0 && dx !== 1) {
        dx = -1;
        dy = 0;
    } else if (newDx === 1 && newDy === 0 && dx !== -1) {
        dx = 1;
        dy = 0;
    }
}

// Touch events for control buttons
function addTouchHandler(btn, dx, dy) {
    btn.addEventListener('touchstart', (e) => {
        e.preventDefault();
        handleDirectionChange(dx, dy);
    }, { passive: false });

    btn.addEventListener('click', () => {
        handleDirectionChange(dx, dy);
    });
}

addTouchHandler(upBtn, 0, -1);
addTouchHandler(downBtn, 0, 1);
addTouchHandler(leftBtn, -1, 0);
addTouchHandler(rightBtn, 1, 0);

// Tap canvas to start game
canvas.addEventListener('click', () => {
    if (!gameRunning) {
        startGame();
    }
});

// Prevent context menu on long press
document.addEventListener('contextmenu', (e) => {
    if (isMobile()) {
        e.preventDefault();
    }
});

// Detect mobile device
function isMobile() {
    return /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent) ||
           window.innerWidth <= 600;
}

// Register Service Worker
if ('serviceWorker' in navigator) {
    window.addEventListener('load', () => {
        navigator.serviceWorker.register('./sw.js')
            .then((registration) => {
                console.log('Service Worker registered:', registration.scope);
            })
            .catch((error) => {
                console.log('Service Worker registration failed:', error);
            });
    });
}

// Wake Lock API - keep screen on during gameplay
let wakeLock = null;

async function requestWakeLock() {
    if ('wakeLock' in navigator && gameRunning && !gamePaused) {
        try {
            wakeLock = await navigator.wakeLock.request('screen');
        } catch (err) {
            console.log('Wake Lock error:', err);
        }
    }
}

function releaseWakeLock() {
    if (wakeLock) {
        wakeLock.release();
        wakeLock = null;
    }
}

// Request wake lock when game starts
const originalStartGame = startGame;
startGame = function() {
    originalStartGame();
    requestWakeLock();
};

// Release wake lock when game pauses or ends
const originalGameOver = gameOver;
gameOver = function() {
    originalGameOver();
    releaseWakeLock();
};

const originalTogglePause = togglePause;
togglePause = function() {
    originalTogglePause();
    if (gamePaused) {
        releaseWakeLock();
    } else {
        requestWakeLock();
    }
};

// Handle visibility change
document.addEventListener('visibilitychange', () => {
    if (document.hidden && wakeLock) {
        releaseWakeLock();
    } else if (!document.hidden && gameRunning && !gamePaused) {
        requestWakeLock();
    }
});
```

**Step 2: Commit**

```bash
cd /home/hmj/myopencode/snake-game
git add index.html
git commit -m "feat: add enhanced touch controls and PWA features"
```

---

## Task 8: Final Verification

**Step 1: Verify all files exist**

Run: `ls -la /home/hmj/myopencode/snake-game/`
Expected: manifest.json, sw.js, icons/, index.html all present

**Step 2: Verify manifest.json content**

Run: `cat /home/hmj/myopencode/snake-game/manifest.json`
Expected: Valid JSON with PWA configuration

**Step 3: Verify git status**

Run: `cd /home/hmj/myopencode/snake-game && git status`
Expected: Working tree clean

**Step 4: Final commit for implementation plan**

The implementation plan is already saved. Run:

```bash
cd /home/hmj/myopencode/snake-game
git add docs/plans/2026-02-25-mobile-pwa-implementation.md
git commit -m "docs: add mobile PWA implementation plan"
```

---

## Summary

| Task | Description | Files |
|------|-------------|-------|
| 1 | Create PWA manifest | manifest.json |
| 2 | Create Service Worker | sw.js |
| 3 | Create app icons | icons/ |
| 4 | Add PWA meta tags | index.html |
| 5 | Rewrite CSS mobile-first | index.html |
| 6 | Update HTML structure | index.html |
| 7 | Update JavaScript | index.html |
| 8 | Final verification | - |
