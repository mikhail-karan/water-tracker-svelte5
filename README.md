# Water Tracker

A simple Svelte application that helps track daily water consumption, demonstrating the use of Svelte's reactive state management features.

## Features

- Track water consumption with preset amounts
- Visualize progress towards daily water intake goals
- Automatically save data using localStorage
- Display time of last drink

## Svelte State Management Demo

This app demonstrates key Svelte state management concepts:

### `$state`

The app uses Svelte's `$state` for reactive variables:

```ts
let waterConsumed = $state(0);
let dailyGoal = $state(2000);
let lastDrink: Date | null = $state(null);
```

### `$derived`

Derived state automatically updates when dependencies change:

```ts
const blocksToFill = $derived(Math.min(Math.floor(waterConsumed / 100), 20));
const waterBlocks = $derived(
  Array(20)
    .fill(false)
    .map((_, index) => index < blocksToFill)
);
```

### `$effect`

Effects run when dependencies change, perfect for side effects like saving to localStorage:

```ts
$effect(() => {
  // Persist data to localStorage whenever values change
  if (!firstRun) {
    localStorage.setItem(
      'waterData',
      JSON.stringify({
        waterConsumedSaved: waterConsumed,
        lastDrinkSaved: lastDrink,
        dailyGoalSaved: dailyGoal
      })
    );
  } else {
    // Load saved data on first run
    // ...
  }
});
```

## Running the Application

```bash
# Install dependencies
pnpm install

# Start development server
pnpm run dev

# Build for production
pnpm run build
```
