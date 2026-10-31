# PARKLAB 3D

![TypeScript](https://img.shields.io/badge/TypeScript-5.8-3178C6?logo=typescript&logoColor=white)
![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=black)
![Three.js](https://img.shields.io/badge/Three.js-0.170-000000?logo=threedotjs&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-6-646CFF?logo=vite&logoColor=white)
![Status](https://img.shields.io/badge/Status-Prototype-informational)

**Practise UK parking manoeuvres in the browser, then find out exactly why an attempt went wrong.**

PARKLAB 3D is a browser-based 3D parking simulator for learner drivers and anyone who wants to practise parking without a real car. Drivers attempt timed manoeuvres, are scored on how accurately they park, and receive spoken coaching based on what the car was doing just before a crash. It runs entirely in the browser with no backend: physics, scoring, and the retrieval behind the coach all run on the client.

<!-- Add a screenshot or demo GIF here once one is in the repository. -->

**Links:** [Live Demo](https://parklab-3d.vercel.app/)

> **Disclaimer:** PARKLAB 3D is an independent project and is not affiliated with or endorsed by the DVSA or any vehicle manufacturer or company named in the app. It does not replace professional driving lessons.

## Contributors

| Contributor | Role / Feature Ownership |
| --- | --- |
| [Anj-13](https://github.com/Anj-13) | Challenges, parking evaluation, crash coach and retrieval, voice and webcam features, replay storage, soundtrack, and mobile controls |
| [Faizan Alvi](https://github.com/alvi83252-dot) | Project setup, shared contracts, 3D world, simulator integration, free roam and geocoding, damage system, replay viewer, leaderboard, landing page, vehicle fleet expansion, and Roadster flight |
| [Joachimsyh](https://github.com/Joachimsyh) | Original vehicle models, Tesla Roadster, driving physics, collision detection, vehicle controller and selector, customisation studio, and Lenis scrolling |

## Table of Contents

- [Project Overview](#project-overview)
- [My Role & Technical Contributions](#my-role--technical-contributions)
- [Key Features](#key-features)
- [Tech Stack](#tech-stack)
- [System Architecture & Project Structure](#system-architecture--project-structure)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Testing & Quality](#testing--quality)
- [Challenges & Solutions](#challenges--solutions)
- [Results & What I Learned](#results--what-i-learned)
- [Known Limitations](#known-limitations)
- [Future Improvements](#future-improvements)
- [License](#license)

## Project Overview

**Problem:** Parking is hard to practise. Learners get limited time in a car, and a failed attempt rarely explains what went wrong or how to correct it.

**Solution:** A simulator that measures each attempt against fixed criteria. After a crash, it works out which part of the car was hit and the likely cause, then gives advice drawn from a parking knowledge base and the driver's own attempt history.

**Audience / Context:** Learner drivers preparing for the UK driving test. Built as a team project by three developers.

**Project Status:** Prototype, deployed on Vercel. See [Known Limitations](#known-limitations).

## My Role & Technical Contributions

**[Anj-13](https://github.com/Anj-13): Gameplay, evaluation, and AI coaching**

I built the challenge system, the scoring logic, and the crash coach. The 3D world, vehicles, and free roam were built by my teammates, who are credited under [Contributors](#contributors).

### Core Contributions

**Challenges and scoring**
- Defined the five parking challenges, including their targets, obstacles, instructions, and the 3D scene they load into.
- Wrote the parking evaluator, which checks all four corners of the rotated car against the bay and combines position, alignment, kerb distance, and efficiency into a score out of 100.
- Built the original challenge HUD and results panel.

**Crash feedback and coaching**
- Built a feedback loop that keeps a rolling 60-frame telemetry buffer, extracts pre-impact speed, gear, and steering, identifies the impact zone, and classifies the likely cause.
- Implemented hybrid retrieval that combines TF-IDF cosine similarity with structured telemetry similarity, across a static knowledge base and the driver's stored crashes and attempts.
- Added attempt-over-attempt comparison, an Ask Coach feature with typed or push-to-talk questions, and spoken feedback through the Web Speech API.
- Added an opt-in webcam observer that adjusts the coaching tone from visible facial cues. It uses on-device analysis and never stores or uploads frames.

**Replay, audio, and mobile**
- Built the attempt recorder and saved replay storage, and connected replays to the results panel, top bar, pause menu, and coach bar.
- Synthesised a separate background track for each challenge with the Web Audio API, with volume ducking while the coach speaks.
- Added the mobile thumbstick and touch brake, and reworked the mobile HUD layout.

> **Contribution summary:** Built the challenge system, deterministic parking evaluator, retrieval-based crash coach, attempt replay storage, procedural soundtrack, and mobile driving controls for a team-built 3D parking simulator in React, TypeScript, and Three.js.

## Key Features

- **Five parking challenges:** Parallel parking, forward bay, reverse bay, and pull up on the right from the UK driving test, plus angled bay as extra practice.
- **Precise scoring:** Each attempt gets a score out of 100, a star rating, and specific notes on position, alignment, and corrections.
- **Crash coaching:** Explains what happened and how to fix it, on screen and by voice, and compares the attempt with earlier ones.
- **Replays:** Saves recent attempts in the browser so they can be watched back in 3D.
- **Seven vehicles:** Different dimensions and handling, from a Modern Hatchback and Large SUV to a New Tesla Roadster that can fly up to 38 m.
- **Crash damage:** Structural health, dents, sparks, and body parts that come loose and fall off.
- **Free roam:** Drive around eight preset locations in the UK, New York, and Tokyo, or search for an address, postcode, or coordinates.
- **Customisation:** Seven car styles and six environments, including wooden, brick-built, and cyberpunk themes. Car styles also affect handling.
- **Progression:** Points, levels, ranks, streaks, and a leaderboard of fictional rival drivers.
- **Mobile support:** On-screen thumbstick, brake pedal, and collapsible HUD panels.

## Tech Stack

| Layer | Technologies | Purpose |
| --- | --- | --- |
| Frontend / UI | React 18, TypeScript, CSS custom properties, Lucide React | Interface, HUD, and domain types |
| 3D Rendering | Three.js, React Three Fiber, Drei | WebGL scenes, vehicles, and camera work |
| Core Logic | Custom TypeScript modules | Driving physics, collisions, damage, scoring, and retrieval |
| Browser APIs | Web Speech, Web Audio, MediaDevices, FaceDetector (where supported), localStorage | Voice coach, soundtrack, opt-in webcam, and saved progress |
| Storage | Browser localStorage | Driver profile, coach memory, replays, and customisation |
| External Data | Mapbox (optional), OpenStreetMap Nominatim, Overpass API | Free-roam search, road names, buildings, and map imagery |
| Tools / Deployment | Vite, Lenis, Git, GitHub, Vercel | Bundling, landing page scrolling, source control, and hosting |

## System Architecture & Project Structure

`App.tsx` switches between two views: the scrolling landing page and the full-screen simulator. Inside the simulator, each frame flows like this:

```text
Keyboard or touch input
        ↓
Driving physics → collision detection → damage system
        ↓
Vehicle state → 3D scene + HUD + telemetry buffer
        ↓
Park & Evaluate → score and results
Crash           → cause analysis → knowledge retrieval → coaching
```

<details>
<summary>View project folder structure</summary>

```text
PARKLAB-3D/
├── public/                     # Favicon and static asset folders
├── src/
│   ├── app/                    # Root component and app-level styles
│   ├── components/
│   │   ├── ui/                 # Customisation studio panel and shared UI
│   │   ├── screens/            # Earlier route screens, not used by App.tsx
│   │   └── layout/             # Earlier page layout, not used by App.tsx
│   ├── config/                 # App, showcase, and customisation settings
│   ├── features/
│   │   ├── challenge-ui/       # HUD, results, crash panel, replay modal, thumbstick
│   │   ├── challenges/         # Challenge definitions and 3D challenge scene
│   │   ├── cities/             # Free-roam presets, geocoding, and Mapbox config
│   │   ├── evaluation/         # Scoring, crash analysis, coach, replays, audio
│   │   ├── leaderboard/        # Driver profile, points, and rivals
│   │   ├── showcase/           # Landing page and 3D vehicle showcase
│   │   ├── simulation/         # Main simulator screen and replay viewer
│   │   ├── vehicles/           # Models, physics, collisions, damage, and controls
│   │   └── world/              # Parking environment, lighting, and camera rig
│   ├── hooks/                  # Lenis smooth scrolling
│   ├── types/                  # Shared TypeScript contracts
│   ├── main.tsx                # Application entry point
│   └── vite-env.d.ts           # Vite type declarations
├── .env.example                # Environment variable template
├── .nvmrc                      # Node.js version
├── index.html                  # HTML entry point
├── package.json                # Scripts and dependencies
├── tsconfig.json               # TypeScript configuration
├── vercel.json                 # Vercel build and SPA rewrite settings
└── vite.config.ts              # Vite and chunk-splitting settings
```

</details>

### Notable Design Decisions

- **Local retrieval instead of an LLM:** The coach ranks stored guidance and past attempts, then builds its feedback from that evidence in the browser. It needs no API key or server, and its advice stays tied to recorded telemetry. The trade-off is that replies are less flexible than a generative model.
- **Deterministic scoring:** The same attempt always gets the same result, which makes the evaluator possible to check with fixed test cases.
- **Fixed-rule pass criteria:** A high score cannot override a fault. A mandatory failure caps the score at 59, so the score and the result never disagree.
- **Telemetry held outside React state:** The rolling buffer lives in a ref, so recording data every frame does not trigger a re-render.
- **Webcam off by default:** The camera only starts when the driver turns it on, and its cues are treated as uncertain signals rather than a reading of how someone feels.

## Getting Started

### Prerequisites

- Node.js 22 (see [.nvmrc](.nvmrc)) and npm
- A browser with WebGL support
- Optional: a [Mapbox](https://www.mapbox.com/) public access token

### 1. Clone and Install

```bash
git clone https://github.com/Anj-13/PARKLAB-3D.git
cd PARKLAB-3D
npm install
```

### 2. Configure (Optional)

The app runs without any configuration. To enable Mapbox search and map imagery in free roam, create a `.env.local` file in the project root:

```bash
VITE_MAPBOX_ACCESS_TOKEN=your_public_token
```

Never commit this file. Vite includes `VITE_` variables in the client bundle, so only use a public token, ideally restricted to your own domains.

### 3. Build and Run

```bash
npm run dev
```

**Expected result:** The landing page is available at `http://localhost:5173`.

For a production build:

```bash
npm run build
npm run preview
```

### Deploy to Vercel

Import the repository into Vercel. [vercel.json](vercel.json) already sets the framework, build command, and output directory, and rewrites every path to `index.html` so direct links work. To enable Mapbox, add `VITE_MAPBOX_ACCESS_TOKEN` under **Project Settings → Environment Variables** and redeploy.

## Usage

1. Open the app, choose a vehicle on the landing page, and select **Start Driving** for challenges or **Free Roam** to explore.
2. Follow the on-screen instructions and drive into the marked target area.
3. Come to a complete stop, then select **Park & Evaluate** to see your score.
4. Review the coaching after a crash, ask the coach a question, or watch the attempt back with **Replay**.

### Controls

| Action | Keyboard | Touch |
| --- | --- | --- |
| Accelerate | `W` or `↑` | Thumbstick |
| Brake or reverse | `S` or `↓` | Thumbstick |
| Steer | `A` / `D` or `←` / `→` | Thumbstick |
| Handbrake | `B`, or `Space` / `Q` on ground vehicles | Brake pedal |
| Fly up (Roadster) | `Space`, `Q`, or `PageUp` | HUD button |
| Fly down (Roadster) | `Ctrl`, `Shift`, `F`, or `PageDown` | HUD button |
| Switch camera | `C` | HUD button |
| Show or hide HUD | `H` | HUD button |
| Evaluate | `E` or `Enter` | HUD button |
| Restart | `R` | HUD button |
| Pause | `Esc` | HUD button |

### Direct Links

| URL | Opens |
| --- | --- |
| `/simulate?challenge=reverse-bay&vehicle=suv` | A specific challenge and vehicle |
| `/?mode=freeroam&city=harrow-uk` | Free roam at a preset location |

Challenge IDs are `parallel`, `reverse-bay`, `forward-bay`, `angled-bay`, and `pull-up-right`.

### Passing a Challenge

An attempt passes only if all of the following are true:

- No collisions
- The car is stationary
- All four corners are inside the target
- The car is within 0.85 m of the target position
- The car is within 8.5° of the target angle for parallel and pull up on the right, or 10° for bay challenges
- Kerb distance is valid
- The reverse is verified (pull up on the right only)
- The final score is at least 60

### Data and Privacy

Driver progress, coach memory, replays, and customisation are saved in the browser's localStorage and can differ between devices. Webcam frames are not stored or uploaded. When someone searches in free roam, the search text is sent to the map services listed under [Tech Stack](#tech-stack).

## Testing & Quality

- **Tests:** There is no test runner or `npm test` script. The repository includes two self-check modules written in TypeScript:
  - `runEvaluationSelfCheck` in [verifyEvaluation.ts](src/features/evaluation/verifyEvaluation.ts) covers scoring across every challenge, crash analysis, retrieval, memory storage, attempt comparison, and coach answers.
  - `runWorldModuleSmokeTests` in [worldSmokeTest.ts](src/features/world/__tests__/worldSmokeTest.ts) covers camera maths, invalid input handling, and the layout of the parking area.
- **Validation:** Evaluation rejects moving cars, overhanging footprints, and collisions. Camera calculations fall back to safe values on invalid numbers, and the world includes fallback components so a rendering error in one layer does not take down the scene.
- **How to run checks:** `npm run typecheck` runs the TypeScript compiler. `npm run build` also runs it before bundling.

## Challenges & Solutions

| Challenge | Approach | Outcome |
| --- | --- | --- |
| Three developers building connected modules at the same time | Agreed shared TypeScript contracts for the world, vehicles, challenges, and evaluation early on | Modules could be built separately and then merged into one simulator |
| Recording telemetry every frame without slowing the UI | Kept the 60-frame buffer in a ref and only updated React state on events such as crashes | Data capture stays separate from rendering |
| Scores could suggest a pass when a mandatory check had failed | Capped failed attempts at 59, one point below the pass mark | The score and the pass result always agree |
| Replays could fill browser storage | Limited saved replays to 20 attempts of up to 600 frames, recorded at 10 Hz | Replay storage has a fixed upper limit |
| Background music competed with the voice coach | Boosted the soundtrack and ducked it automatically during speech | The coach stays clear without silencing the music |
| Mobile controls overlapped the HUD | Moved the thumbstick and brake, enlarged both, and collapsed HUD panels by default | More of the driving view stays clear on small screens |

## Results & What I Learned

- **Outcome:** A deployed 3D parking simulator with five challenges, seven vehicles, free roam, and a working crash coaching loop.
- **Skills gained:** Geometry-based evaluation, information retrieval with TF-IDF, the Web Speech and Web Audio APIs, responsible use of webcam input, and integrating work across a team using shared TypeScript contracts.
- **Reflection:** I would connect the self-checks to a real test runner from the start, so they run on every change instead of being called by hand.

## Known Limitations

- **Coach:** The AI coach uses local retrieval and rule-based text generation. It does not use an LLM.
- **Leaderboard:** Rivals are fictional and scores stay on the device, so the leaderboard is not global. New drivers start with a seeded profile at level 20 with 16,400 points.
- **Live demo:** The public deployment has no Mapbox token, so free roam relies on presets and OpenStreetMap services.
- **Free roam accuracy:** Preset locations are hand-built approximations and other areas are partly generated. If a search finds no match, the app generates placeholder coordinates instead of a real location.
- **Browser support:** Speech recognition and FaceDetector depend on the browser. Facial cues fall back to a simpler pixel-based estimate and are not reliable measures of emotion.
- **In-app labels:** Labels such as "SpaceX" and "Mapbox Live GPS" are presentation text. Flight is simulated, and the app does not use the device's location.
- **Unused code:** `src/components/screens`, `src/components/layout`, and the gameplay video component are left over from earlier versions, and `src/three` contains only empty folders.
- **Version numbers:** `package.json` lists `0.1.0` while `src/config/app.config.ts` lists `1.0.0`.

## Future Improvements

- [ ] Add a test runner and move the self-checks into automated tests, so scoring and physics changes are checked on every push.
- [ ] Remove the unused screens and empty folders to make the codebase easier to navigate.
- [ ] Add a shared online leaderboard so drivers can compete with real people.
- [ ] Split the 3D code into smaller chunks to reduce the initial download.
- [ ] Add screenshots and a short demo video to this README.

## License

No license has been specified.
