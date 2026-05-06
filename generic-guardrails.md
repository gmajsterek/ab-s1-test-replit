# Generic Guardrails (Stage 1 Demo Scenarios Only)

**Applicability:** These guardrails apply to all scenarios unless a scenario explicitly states an exception.

## Scope and Intent

Your task is to create a functional application that would be a "mockup" or demo/preview of experience, with prebuilt scenario of experience - but without database, security, or login; it's to show how the application would work

So not a figma design but an app of AI-first (ideatie on the futuristic AI-first, proactive-first experience that is yet realistic)

- Treat scenario outputs as PoC/demo artifacts unless the scenario explicitly requires production hardening.
- Keep implementation runnable, and reproducible for benchmark evaluation.
- Demos prioritize clickable realism and local demonstrability over production architecture.

It would be nice to have something more than just a mockup but some built in functionalities also (no database needed for now).
Also if we could add some "AI agent" functionality would be great - both built into the core experience and as a type of "recommendations" and “reasoning” showcased etc.
Idea is to create a full preview of how such application in a business scenario would work.
I would also like to have a "wiget"-like dashboards and analytics about the same.
 
Project need to include also HOWTORUNDEMO.MD file - that would provide background, and step by step instruction how to best present this demo.
 
## Local-First Execution

- Prefer local, self-contained execution for happy-path flows.
- Do not require remote services or external model calls unless explicitly requested by the scenario.
- Do not integrate external APIs or external ticket systems unless explicitly requested by the scenario.

## Data and Persistence

- Use only the persistence approach required by the scenario.
- Include synthetic data where demo/replay/reset behavior is part of the scenario.
- Seed/init/reset operations should be deterministic and safe to re-run where required.

## API and Documentation

- Document exposed local API endpoints using OpenAPI when APIs are in scope.
- Provide clear run steps and known limitations for each artifact.
- Include architecture notes when required by the scenario.

## Design System & Styling

- The app must use the provided `logo-dgem.svg` as its primary product logo.
- Use `Test_Scenarios/logo-dgem.svg` as the source asset. If the app copies assets into its own folder, the copied logo must come from that file without replacing it with a different logo.
- The app name must start with `DGEM` when the scenario includes visible product branding.

Some notes on the design system: Color Scheme Primary Colors (User-specified): Primary Blue: #0158AC (HSL: 209 97% 34%) Light Gray: #F3F4F5 (background accents) Deep Blue: #2457A6 (secondary blue) Sky Blue: #57B6ED (highlights, accents) Dark Navy: #141A36 (dark mode background) Pure White: #FFFFFF (headers, cards) Teal: #61D2CF (accent color) Deep Teal: #38808C (secondary accent) Background: Colorful gradient applied to body background: linear-gradient(90deg, rgba(255, 47, 75, 0.3) 0%, rgba(255, 178, 74, 0.3) 11%, rgba(0, 230, 227, 0.3) 41.5%, rgba(1, 176, 245, 0.3) 69.5%, rgba(229, 87, 173, 0.3) 93%, rgba(255, 47, 75, 0.3) 100%); Headers: Pure white (#FFFFFF) in light mode, dark navy (#141A36) in dark mode Use .app-header class for consistent header styling Core Design Principles Touch-First Optimization: All interactive elements minimum 44px touch targets, generous spacing for finger-based interaction Canvas Clarity: Maximum contrast between workspace and UI chrome, distraction-free canvas areas Interconnected Flow: Visual indicators showing data relationships between canvases Progressive Disclosure: Complex features accessible but not overwhelming White Headers: All headers use white/light backgrounds for clean separation from colorful gradient Typography Font Stack: Inter (Google Fonts) Headings: 600 weight, sizes: 32px (H1), 24px (H2), 18px (H3) Body: 400 weight, 16px base UI Labels: 500 weight, 14px Canvas Text: 400 weight, 18px (larger for touch readability) Layout System Spacing Units: Tailwind 4, 6, 8, 12, 16 units Canvas padding: p-8 Card spacing: gap-6 Touch target minimum: h-12, w-12 Grid Structure: Menu page: grid-cols-2 md:grid-cols-3 lg:grid-cols-4 Canvas layouts: Full-screen with side panels (sidepanel: w-80)

Name of the app should start with : “DGEM …”  and logo is below (SVG)
 
Some notes on the design system:
Color Scheme
Primary Colors (User-specified):
Primary Blue: #0158AC (HSL: 209 97% 34%)
Light Gray: #F3F4F5 (background accents)
Deep Blue: #2457A6 (secondary blue)
Sky Blue: #57B6ED (highlights, accents)
Dark Navy: #141A36 (dark mode background)
Pure White: #FFFFFF (headers, cards)
Teal: #61D2CF (accent color)
Deep Teal: #38808C (secondary accent)
Background: Colorful gradient applied to body
background: linear-gradient(90deg, rgba(255, 47, 75, 0.3) 0%, rgba(255, 178, 74, 0.3) 11%, rgba(0, 230, 227, 0.3) 41.5%, rgba(1, 176, 245, 0.3) 69.5%, rgba(229, 87, 173, 0.3) 93%, rgba(255, 47, 75, 0.3) 100%);
 
Headers: Pure white (#FFFFFF) in light mode, dark navy (#141A36) in dark mode
Use .app-header class for consistent header styling
Core Design Principles
Touch-First Optimization: All interactive elements minimum 44px touch targets, generous spacing for finger-based interaction
Canvas Clarity: Maximum contrast between workspace and UI chrome, distraction-free canvas areas
Interconnected Flow: Visual indicators showing data relationships between canvases
Progressive Disclosure: Complex features accessible but not overwhelming
White Headers: All headers use white/light backgrounds for clean separation from colorful gradient
Typography
Font Stack: Inter (Google Fonts)
Headings: 600 weight, sizes: 32px (H1), 24px (H2), 18px (H3)
Body: 400 weight, 16px base
UI Labels: 500 weight, 14px
Canvas Text: 400 weight, 18px (larger for touch readability)
Layout System
Spacing Units: Tailwind 4, 6, 8, 12, 16 units
Canvas padding: p-8
Card spacing: gap-6
Touch target minimum: h-12, w-12
Grid Structure:
Menu page: grid-cols-2 md:grid-cols-3 lg:grid-cols-4
Canvas layouts: Full-screen with side panels (sidepanel: w-80)

- Include clearly distinguishable loading, error, and success states when UI is in scope.
- Ensure validation behavior is deterministic and user-readable when validation is in scope.
- Preserve realistic, clickable flow behavior appropriate to the scenario.

## Observability and Testing

- Include basic structured business observability events when business flow events are in scope.
- Include light automated tests for critical paths (happy path and at least one failure path when applicable).
- Run static analysis (SonarQube or equivalent) and resolve blocker/critical issues in submitted artifacts.


## Repository and Delivery Constraints

- Place generated code under `Test_Scenarios/CreatedApps/<SCENARIO_ID>/` and avoid out-of-scope edits.
- Folder naming rule: use the exact scenario ID as the folder name (for example: `Test_Scenarios/CreatedApps/S1-DEMO-TICKET/`).
- Keep each scenario in its own separate subfolder under `Test_Scenarios/CreatedApps/`.
- Keep setup and commands straightforward so reviewers can reproduce results quickly.
