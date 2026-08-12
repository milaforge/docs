---
description: >-
  This development log documents the incremental development of a fully
  client-side React.js application using TypeScript, Vite, React Router,
  Tailwind CSS, Vitest, GitHub Actions, GitHub Pages, and loc
coverY: 0
coverHeight: 139
layout:
  width: default
  cover:
    visible: true
    size: background
    mask: radial
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
  actions:
    visible: true
tags:
  - frontend
  - engineering
---

# Building a React.js Application

### Introduction

This development log documents the incremental development of a fully client-side React.js application using TypeScript, Vite, React Router, Tailwind CSS, Vitest, GitHub Actions, GitHub Pages, and localStorage.

The project is a small learning game for practicing React Hooks. The engineering focus is broader: establish a reproducible foundation, define a small scope, test important application logic, separate game rules from the React UI, debug real development problems, and ship working milestones incrementally.

{% embed url="https://github.com/milaforge/react-hooks-quest" %}

### What This React Project Demonstrates

* React.js and TypeScript application development
* Vite setup and configuration
* Client-side state and localStorage persistence
* React Router navigation
* Test-driven development with Vitest
* Separation of business logic from React components
* Incremental architecture and feature development
* Git versioning and continuous deployment
* Debugging real development-environment problems
* Refining a data model when implementation exposes better requirements

### Technology Stack

* React.js
* TypeScript
* Vite
* React Router
* Tailwind CSS
* Vitest
* Git and GitHub
* GitHub Actions
* GitHub Pages
* localStorage

### Phase 0: Setting Up a React.js Application with Vite

#### Creating the React + TypeScript project

The first goal was not to build the whole game. It was to establish a reproducible React application that could be built, tested, versioned, and deployed.

```sh
gh repo create react-hooks-quest \
  --public \
  --clone \
  --description "Learn React Hooks by playing."

cd react-hooks-quest
npm create vite@latest . -- --template react-ts

pnpm install
pnpm add react-router-dom
```

The initial project uses React + TypeScript with ESLint. Tailwind CSS is also part of the intended UI stack.

#### Fixing a Vite native binding error

The first build exposed a tooling problem rather than an application problem:

```
Error: Cannot find native binding
...
rolldown@1.2.3
```

The useful clues were `rolldown` and the native binding error. Vite 7 was using Rolldown, which introduced platform-specific native dependencies. Rather than spending the first milestone debugging tooling unrelated to the product, I chose a stable Vite version for the project.

```sh
pnpm add -D vite@^6 @vitejs/plugin-react@^4
rm -rf node_modules pnpm-lock.yaml
pnpm install
pnpm build

✓ built in 432ms
```

The engineering decision was to solve the smallest problem necessary to keep product development moving. The goal at this stage was simply:

> Can a user complete one mission, earn XP, and have progress saved?

#### Fixing Vite localhost connection problems

The development server started, but the browser could not connect. Even `curl` hung:

```sh
curl http://localhost:5173/
```

I verified that the process was actually listening instead of assuming Vite had failed:

```sh
ss -ltnp | grep 5173
lsof -i :5173
```

The server was bound to the local interface. Running Vite with an explicit host resolved the problem:

```sh
pnpm vite --host 0.0.0.0
```

I then made the fix part of the project configuration:

```typescript
export default defineConfig({
  plugins: [react()],
  server: {
    host: true,
  },
});
```

This is representative of the debugging approach used throughout the project: verify the observed failure, identify its scope, then make the smallest durable fix.

#### Configuring Git and GitHub

Once the React foundation worked, I created a reproducible snapshot:

```sh
git init
git add .
git commit -m "chore: bootstrap Vite react app"
git push origin main
```

The project uses release tags to represent meaningful states:

```
v0.0.1  Project scaffold
v0.0.2  Specification and architecture
v0.1.0  First playable mission
v0.2.0  useState chapter complete
v0.3.0  useEffect chapter complete
v1.0.0  Complete learning game
```

#### Deploying React with GitHub Actions and GitHub Pages

I wanted every change pushed to `main` to produce a publicly accessible version without manually deploying from the development machine.

GitHub Pages provides the hosting, while GitHub Actions provides the deployment workflow. This avoids an additional `gh-pages` dependency and makes deployment automatic.

The Vite configuration needs the repository base path:

```typescript
export default defineConfig({
  base: "/react-hooks-quest/",
});
```

The result is a simple continuous-deployment loop:

```
commit → push → GitHub Action → build → GitHub Pages
```

At this point the project had a working React foundation, versioning, and automated deployment. Tests were added before application logic was integrated so that deployment would not become the only automated verification step.

### Phase 1: Defining the React Application Architecture

#### Requirements and acceptance criteria

The first playable milestone was deliberately constrained.

A user should be able to:

* Visit the home page.
* Start a mission.
* See a question.
* Select an answer.
* Receive immediate feedback.
* Earn XP when correct.
* Have progress saved in localStorage.
* Refresh the page and retain progress.

Nothing more was required for the first playable release.

The complete loop was:

```
Start Screen
    ↓
Choose Mission
    ↓
Play Mission
    ↓
Answer Question
    ↓
Receive XP + Explanation
    ↓
Return to Mission List
```

#### Client-side architecture

The application has no backend. The browser contains the UI, game rules, mission data, and persistent player state:

```
Browser
├── React UI
├── Game rules
├── Mission data
└── localStorage
```

The important boundary is between the React layer and the game logic. React should collect input and render results; it should not become the source of truth for progression or scoring.

#### React application state

The initial player model was intentionally small:

```typescript
interface PlayerState {
  xp: number;
  completedMissions: string[];
  unlockedMissions: string[];
}
```

The first implementation stored unlocks explicitly because the first milestone had simple linear progression. Later, implementation evidence showed that unlockability could be derived, so this model was simplified.

#### Mission and chapter data model

The first milestone used a flat mission collection. That was enough to render the first mission, but the product was already heading toward a curriculum organized around React Hook topics.

The later model makes chapters explicit:

```typescript
interface Chapter {
  id: string;
  title: string;
  description?: string;
  missions: Mission[];
}
```

The canonical structure is:

```
Chapter
├── useState
│   ├── state-001
│   ├── state-002
│   └── state-003
├── useEffect
│   ├── effect-001
│   └── effect-002
└── useRef
    ├── ref-001
    └── ref-002
```

A flat list can still be derived for lookup or routing:

```typescript
const missions = chapters.flatMap((chapter) => chapter.missions);
```

The chapter structure, rather than the flattened list, is the source of truth.

### Phase 2: Implementing the React Application

#### Test-driven development with Vitest

Before implementing important behavior, I added the test infrastructure:

```sh
pnpm install -D vitest jsdom@26
```

The first storage tests were intentionally written before the implementation:

```
returns default player when no save exists
persists player progress
clears saved progress
recovers from invalid saved data
```

The initial result was four failing tests. The implementation was then added until the tests passed.

This pattern was repeated for game rules where tests provided useful protection: write the expected behavior, implement the smallest logic that satisfies it, then integrate it with React.

#### LocalStorage persistence

Persistence is isolated from the UI. The storage module owns serialization, loading, saving, clearing, and recovery from invalid saved data.

This means components do not need to know how player state is represented in `localStorage`.

#### Pure game progression logic

Answer evaluation is deliberately a pure function:

```
mission + selected answer
        ↓
 evaluation result
        ↓
 React decides what to display
```

XP and persistence are not updated inside answer evaluation. Completing a mission is a separate operation:

```
correct answer
     ↓
complete mission
     ↓
add XP
     ↓
save player state
```

This keeps the rules independently testable and prevents UI code from becoming responsible for business behavior.

#### Mission validation

Mission data is mostly static content, so testing every question would add little value. Instead, the application tests the mission contract: the predictable shape that the rest of the application depends on.

The boundary is therefore:

```
Mission data
    ↓
validated contract
    ↓
Game engine
    ↓
React UI
```

#### React UI components

Once the game core was tested, I moved into the React layer.

The initial component structure included:

```
src/components/
├── HUD.tsx
├── MissionCard.tsx
└── MissionList.tsx
```

The start screen consumes player state and mission data. It displays XP, available missions, and locked missions without implementing the progression rules itself.

The mission screen follows the same boundary:

```
Home
 ↓
Select mission
 ↓
Mission Screen
 ↓
Select answer
 ↓
Submit
 ↓
Show result
```

The UI calls the game functions rather than reproducing their rules.

#### React Router navigation

Once mission selection worked, the application moved from logging a selection to routing to the mission page.

```typescript
<BrowserRouter basename="/react-hooks-quest">
  <Routes>
    <Route path="/" element={<Home />} />
    <Route path="/mission/:id" element={<MissionPage />} />
  </Routes>
</BrowserRouter>
```

Routing remains a presentation/navigation concern. Mission lookup and progression remain in the application model.

### Development Approach

#### Build the smallest useful milestone

Each release answers a concrete product question rather than implementing every anticipated feature. The first playable release exists to prove the core learning loop, not the entire learning platform.

#### Keep game rules out of React components

The resulting boundary is:

```
Mission data
     ↓
Game rules
     ↓
Player state
     ↓
React UI
```

This is not an attempt to create a large architecture. It is a way to keep the source of truth in predictable places.

#### Store authoritative state; derive consequences

The first player model stored both completed and unlocked missions:

```
completedMissions
unlockedMissions
```

As linear progression became clearer, this duplicated information. Unlockability can be calculated from completed missions and the chapter structure.

The model was therefore simplified to:

```typescript
interface PlayerState {
  xp: number;
  completedMissions: string[];
}
```

with access derived through a rule such as:

```typescript
isMissionUnlocked(player, chapterIndex, missionIndex);
```

The progression rules are intentionally simple:

```
First mission of first chapter → unlocked
Next mission → previous mission completed
Next chapter → previous chapter completed
```

The principle is broader than this game:

> Store authoritative facts; derive consequences.

#### Refine the model when implementation provides evidence

The chapter refactor was driven by a concrete UI problem. A flat mission list could display content, but it could not naturally express:

* the player's current chapter;
* chapter completion;
* locked chapters;
* progress within a chapter;
* the next meaningful action.

Instead of adding presentation-specific metadata and increasingly complex UI conditions, I changed the domain model so chapters became first-class objects.

This made the data structure better match the product and simplified both progression and UI structure.

The general decision rule became:

```
Problem
  ↓
Is it local or structural?
  ↓
Local      → fix the local issue
Structural → reconsider the model
```

#### Avoid unnecessary generalization

The curriculum is sequential. It does not currently require arbitrary prerequisite graphs or branching progression.

A more general dependency system would increase complexity without solving an existing requirement. The implementation therefore models the progression that actually exists.

#### Use tests to make change safe

Tests cover the parts of the application where a regression would be costly or where pure logic can be verified independently of the UI: persistence, progression, answer evaluation, mission contracts, and mission access.

This allows the data model and game rules to evolve without relying on manual browser testing for every change.

### Current Result

The project has progressed from a Vite React scaffold to a structured client-side application with:

* automated GitHub Pages deployment;
* typed mission and chapter data;
* localStorage persistence;
* tested game and progression logic;
* React UI components;
* React Router navigation; and
* a simpler progression model based on derived unlock state.

More importantly, the implementation changed the architecture when the original assumptions stopped fitting the product. The initial flat mission model and explicit unlock state were useful for the first playable milestone, but later evidence showed that chapters should be first-class and unlockability should be derived.

The project remains intentionally small. The engineering value is in the process: start with a constrained problem, build a complete loop, separate responsibilities, test important behavior, and let implementation evidence improve the design.
