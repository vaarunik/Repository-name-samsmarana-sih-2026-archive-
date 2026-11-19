# SAMSMARANA
### Memory. Connection. Care.

**Personalized, culturally familiar cognitive engagement for older adults.**

SAMSMARANA is a web-based cognitive engagement and family-connection platform designed around older adults, caregivers and family members. It offers short visual activities, familiar everyday scenes, adaptive activity suggestions, reminders, caregiver-facing engagement summaries and offline-aware activity tracking.

Developed for the **Smart India Hackathon (SIH) 2026** context, with a focus on accessibility and culturally relevant experiences for communities across North Eastern India.

> **Important:** SAMSMARANA is an engagement and activity-support concept. It is not a medical device, diagnostic system or substitute for professional healthcare.

[Live application](https://sih-samsmarana.vercel.app) · [Source code](https://github.com/vaarunik/Repository-name-samsmarana-sih-2026)

---

## The idea

Cognitive activities are more approachable when they feel familiar, clear and connected to everyday life. SAMSMARANA brings elder-friendly activities and family engagement into one interface, while giving caregivers a simple way to review participation and activity performance.

The experience is organized around three roles:
- **Elder:** accessible home, activities, reminders, progress and profile.
- **Caregiver:** activity history, performance summaries, observations and recommendations.
- **Family:** a space for family engagement and messages.

## Product features

### Cognitive activities
A curated activity library covers:
- Recognition
- Recall
- Attention
- Counting
- Spatial awareness
- Sequencing
- Concentration
- Language
- Problem solving
- Story-based activities

Activities use familiar scenes such as gardens, markets, local shops, cooking, tea preparation, birds, home settings, rivers and festivals.

### Adaptive activity planning
A rule-based adaptive engine reviews recent activity attempts and recommends a category and difficulty. It adjusts difficulty gradually, considers skips and rotates activity categories. The session planner creates a short activity plan and uses recent activity history to reduce repetition.

### Stimulus-grounded questions
Questions are built from structured evidence associated with the selected scene. The question validator checks whether a question is supported by that scene's content before it is returned to the activity interface.

### Content rotation
The stimulus selector tracks scene history per profile and activity category. It uses a 30-day cooldown to reduce repeated exposure to the same scene, while selecting from a curated pool of visual stimuli.

### Elder and caregiver experiences
- Elder dashboard with recommended activities and daily engagement indicators.
- Caregiver dashboard with activity history, performance metrics and non-diagnostic observations.
- Family engagement messages.
- Reminders for routines such as meals, hydration, medication and activities.
- Profile preferences including language, region and interests.

### Offline-aware activity tracking
Activity attempts are stored locally in IndexedDB and queued when the app is offline. When connectivity returns, the sync coordinator sends pending attempts to the backend. A unique `syncId` makes retry handling idempotent and helps prevent duplicate attempt records.

### Language and voice support
The language configuration includes English, Kannada, Hindi, Tamil, Telugu, Assamese, Bengali, Manipuri/Meitei, Khasi and Mizo. The voice layer uses the browser Web Speech API for speech synthesis and speech recognition where supported by the user's browser and device. Voice availability varies by platform and language; the interface is designed to handle unavailable voices gracefully.

### Optional AI video activity
The project includes a server-side integration path for Google's Gemini API and Veo video generation. Video generation is optional and depends on a valid server-side `GEMINI_API_KEY`, model availability and provider access. When generation is unavailable or fails, the app can offer a standard activity fallback.

---

## How the system works

1. The user completes onboarding and a profile is stored through the application API.
2. The application restores the signed session and loads profile, activity and reminder data.
3. The activity planner uses recent attempts to recommend a category and difficulty.
4. The stimulus endpoint selects a scene while checking the user's recent content history.
5. Questions are assembled from the selected scene's structured content pack and validated against its evidence.
6. Completed activity attempts are stored locally first and sent to the backend when connectivity allows.
7. Caregiver views use recorded attempts to calculate engagement metrics and factual, non-diagnostic observations.

See the [system architecture](docs/architecture.md) for the component and data-flow diagram.

## Architecture and implementation

| Layer | Implementation |
|---|---|
| Web application | Next.js 16 App Router |
| UI | React 19, TypeScript 5 |
| Styling and components | Tailwind CSS 4, shadcn/ui, Radix UI |
| Motion | Framer Motion |
| Client state | Zustand |
| API layer | Next.js Route Handlers |
| Data access | Prisma ORM |
| Database schema | PostgreSQL provider in Prisma schema |
| Offline storage | IndexedDB |
| Voice | Browser Web Speech API |
| Optional generative video | Google Gemini API / Veo, server-side |
| Runtime and package manager | Bun |

The repository also includes a Caddy reverse-proxy configuration and a Next.js standalone build configuration.

### Backend notes

- Application APIs cover session/profile handling, activities, attempts, reminders, family messages, caregiver insights, session planning, stimulus selection and video generation/status.
- The demo session is implemented with a signed, HTTP-only cookie.
- Prisma models cover profiles, attempts, reminders, family messages, video-generation records and stimulus history.
- The Prisma schema currently declares a PostgreSQL datasource. Configure `DATABASE_URL` for a compatible PostgreSQL database when running the project.
- Supabase variables are mentioned in the example environment file as a possible backend configuration, but the current repository's active data-access implementation uses Prisma. Do not assume Supabase Auth or a Supabase client is wired into the current application.
- The `.env.example` file contains an older SQLite-style `DATABASE_URL` example that does not match the current PostgreSQL Prisma datasource; update that value for your environment before running database commands.

## Repository structure

```text
.
├── prisma/
│   └── schema.prisma
├── public/
│   └── images/
│       ├── activities/
│       ├── hero/
│       └── story/
├── src/
│   ├── app/
│   │   ├── api/
│   │   │   ├── activities/
│   │   │   ├── attempts/
│   │   │   ├── family/
│   │   │   ├── insights/
│   │   │   ├── onboarding/
│   │   │   ├── profile/
│   │   │   ├── reminders/
│   │   │   ├── session/
│   │   │   ├── session-plan/
│   │   │   ├── stimulus/
│   │   │   └── video/
│   │   ├── globals.css
│   │   ├── layout.tsx
│   │   └── page.tsx
│   ├── components/
│   │   └── samsmarana/
│   └── lib/
│       ├── adaptive.ts
│       ├── content-rotation.ts
│       ├── game-i18n.ts
│       ├── i18n.ts
│       ├── idb.ts
│       ├── questions.ts
│       ├── session.ts
│       ├── store.ts
│       ├── sync.ts
│       └── voice.ts
├── .env.example
├── Caddyfile
├── next.config.ts
└── package.json
```

## Run locally

### Requirements
- Bun
- Node.js runtime compatible with the installed Next.js version
- PostgreSQL database
- Optional Google AI Studio API key for Veo video generation

### 1. Clone the repository

```bash
git clone https://github.com/vaarunik/Repository-name-samsmarana-sih-2026.git
cd Repository-name-samsmarana-sih-2026
bun install
```

### 2. Configure environment variables

Create a `.env.local` file in the project root:

```env
DATABASE_URL="postgresql://USER:PASSWORD@HOST:5432/DATABASE?schema=public"
SESSION_SECRET="replace-with-a-long-random-secret"

# Optional: required only for Gemini/Veo video generation
GEMINI_API_KEY=""
VEO_MODEL="veo-3.0-generate-001"
VEO_POLL_TIMEOUT_MS="180000"
```

Keep real credentials in `.env.local`; never commit secrets.

### 3. Generate Prisma Client and prepare the database

```bash
bunx prisma generate
bunx prisma db push
```

### 4. Start the development server

```bash
bun run dev
```

Open [http://localhost:3000](http://localhost:3000).

> The repository's `.env.example` currently has a SQLite-style database URL even though `prisma/schema.prisma` declares PostgreSQL. Use a PostgreSQL connection string as shown above unless you intentionally update the Prisma datasource and validate the rest of the setup for another database.

---

## Project contribution

This was developed as a team project. My contribution focused on product direction and delivery across the project:

- Conducted problem research and helped translate user needs into product requirements.
- Defined and refined feature scope, user flows and the elder/caregiver/family experience.
- Led UI/UX direction, adapting and redesigning a starter template to establish SAMSMARANA's visual identity and interface.
- Coordinated backend integration, application configuration and deployment.
- Used AI coding agents as development tools to accelerate implementation, while directing feature decisions, reviewing outputs and integrating the application.
- Worked on the overall solution architecture and technical coordination.

This description distinguishes product, design and integration ownership from AI-assisted code generation.

## Responsible-use principles

- Activities are intended for engagement and practice, not diagnosis or treatment.
- Caregiver observations describe application activity data only; they are not clinical assessments.
- Voice support depends on device/browser capabilities and language voice availability.
- Video generation is an optional provider-dependent feature and may be unavailable.
- Real-world deployment should include appropriate privacy, security, accessibility and clinical review before use with vulnerable users.

## Future improvements

- Validate language quality with native speakers, especially for North Eastern languages.
- Expand culturally relevant activity and stimulus libraries with community input.
- Strengthen production authentication, authorization and privacy controls.
- Add automated tests for activity validation, offline sync and adaptive planning.
- Improve deployment documentation and operational monitoring.
- Evaluate accessibility with older adults and caregivers.

---

**SAMSMARANA — Memory. Connection. Care.**
