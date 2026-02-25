# Multiple Food Types Design

**Date:** 2026-02-25
**Status:** Approved

## Overview

Enhance the snake game with multiple food types that have different point values and temporary effects. 2-3 foods will be on screen simultaneously, encouraging strategic gameplay.

## Requirements

- 4 food types with different shapes, points, and effects
- 2-3 foods on screen at once
- Weighted spawn distribution
- Temporary speed/slow effects with visual feedback

## Food Types

| Type | Shape | Points | Effect | Spawn % |
|------|-------|--------|--------|---------|
| regular | Circle | 10 | None | 50% |
| bonus | Square | 25 | None | 25% |
| poison | Triangle | 5 | Slow snake for 3 seconds | 15% |
| speed | Diamond | 15 | Speed boost for 3 seconds | 10% |

## Data Structure

Replace single `food` object with `foods` array:

```javascript
let foods = [];
// Each food: { x: number, y: number, type: string }
```

## Spawn Logic

**Initial spawn:** Generate 3 foods with weighted random types, avoiding snake and each other.

**On eat:** Remove eaten food, spawn 1 new food. Always maintain 3 foods.

**Weighted random:**
```javascript
function getRandomFoodType() {
  const rand = Math.random();
  if (rand < 0.50) return 'regular';
  if (rand < 0.75) return 'bonus';
  if (rand < 0.90) return 'poison';
  return 'speed';
}
```

## Visual Rendering

Add `drawFood(food)` function with shape-based rendering:

- **Circle (regular):** Red gradient circle with highlight
- **Square (bonus):** Gold/yellow square with glow
- **Triangle (poison):** Purple triangle pointing down
- **Diamond (speed):** Cyan/blue rotated square (45°)

## Effect System

```javascript
let effectType = null;      // 'slow' | 'speed' | null
let effectEndTime = 0;      // timestamp when effect ends
```

- **Slow effect:** Update interval 100ms → 150ms
- **Speed effect:** Update interval 100ms → 60ms
- **Visual feedback:** Canvas border changes color during effect

## Edge Cases

- **Food spawn collision:** Retry up to 10 times, then place in first available cell
- **Effect stacking:** New effect replaces old effect immediately
- **No available cells:** Reduce food count if board is nearly full

## Implementation Scope

**Files modified:** `index.html` only
**Estimated lines:** ~60-80 new/modified lines
