# Resumind — AI Resume Analyzer

An AI-powered resume analysis platform that scores and provides actionable feedback on your resume against a specific job posting. Powered by Claude AI via Puter.js, with cloud storage and authentication — no backend required.

---

## Overview

Resumind lets job seekers upload their resume as a PDF alongside a target job description. The app converts the resume to an image, sends it to Claude Sonnet for analysis, and returns a structured report covering:

- **ATS Compatibility** — how well the resume passes applicant tracking systems
- **Tone & Style** — voice, professionalism, and clarity
- **Content** — relevance, impact statements, and keywords
- **Structure** — layout, hierarchy, and readability
- **Skills** — match between stated skills and job requirements

Each category is scored 0–100 with specific, actionable improvement tips. Resumes and feedback are persisted to the user's Puter cloud storage so they can be reviewed at any time.

---

## Tech Stack

| Layer            | Technology                                                             |
| ---------------- | ---------------------------------------------------------------------- |
| Framework        | [React Router 7](https://reactrouter.com/) (SSR) with React 19         |
| Styling          | [Tailwind CSS 4](https://tailwindcss.com/)                             |
| State Management | [Zustand 5](https://zustand-demo.pmnd.rs/)                             |
| AI               | Claude Sonnet 4 via [Puter.js](https://puter.com/) AI API              |
| Auth & Storage   | Puter.js (cloud filesystem + KV store)                                 |
| PDF Processing   | [PDF.js](https://mozilla.github.io/pdf.js/) (client-side PDF-to-image) |
| File Upload      | [React Dropzone](https://react-dropzone.js.org/)                       |
| Build Tool       | [Vite](https://vite.dev/)                                              |
| Deployment       | Vercel (via `@react-router/vercel` preset) / Docker                    |

---

## Features

- **Puter Authentication** — sign in with a Puter account; protected routes redirect to `/auth`
- **Drag-and-Drop Upload** — upload a PDF resume (up to 20 MB) with a live file size indicator
- **PDF to Image Conversion** — client-side high-resolution preview generation using PDF.js
- **AI Analysis** — structured JSON feedback from Claude Sonnet 4 tailored to the target role
- **Resume Dashboard** — browse all saved resumes with thumbnail previews and overall scores
- **Detailed Feedback View** — sticky resume preview alongside an expandable feedback accordion
- **Color-Coded Scores** — green (≥70), yellow (50–69), red (<50) across all score dimensions
- **Animated Score Gauge** — SVG arc gauge with smooth fill animation
- **Cloud Persistence** — PDFs, preview images, and feedback stored in Puter's filesystem + KV store
- **Data Wipe Utility** — `/wipe` route to clear all stored resume data

---

## Project Structure

```
ai-resume-analyzer/
├── app/
│   ├── routes/
│   │   ├── home.tsx          # Resume dashboard
│   │   ├── upload.tsx        # Upload & analysis workflow
│   │   ├── resume.tsx        # Feedback detail page
│   │   ├── auth.tsx          # Authentication
│   │   └── wipe.tsx          # Data wipe utility
│   ├── components/
│   │   ├── FileUploader.tsx  # Drag-and-drop PDF input
│   │   ├── Summary.tsx       # Overall score + category scores
│   │   ├── ATS.tsx           # ATS compatibility section
│   │   ├── Details.tsx       # Accordion feedback sections
│   │   ├── ResumeCard.tsx    # Dashboard resume card
│   │   ├── ScoreGauge.tsx    # Animated SVG gauge
│   │   ├── ScoreCircle.tsx   # Circular score badge
│   │   ├── ScoreBadge.tsx    # Inline score chip
│   │   ├── Accordion.tsx     # Reusable accordion
│   │   └── Navbar.tsx        # Top navigation
│   ├── lib/
│   │   ├── puter.ts          # Zustand store wrapping Puter.js API
│   │   ├── pdf2img.ts        # PDF to PNG conversion utility
│   │   └── utils.ts          # cn(), formatSize(), generateUUID()
│   ├── routes.ts             # Route definitions
│   └── root.tsx              # Root layout
├── constants/
│   └── index.ts              # AI system prompt (prepareInstructions)
├── types/
│   ├── index.d.ts            # Resume, Feedback, Job types
│   └── puter.dt.ts           # Puter.js API type declarations
├── public/
│   ├── pdf.worker.min.mjs    # PDF.js web worker
│   └── images/
├── Dockerfile
├── react-router.config.ts
├── vite.config.ts
└── package.json
```

---

## App Routes

| Route         | Description                                 |
| ------------- | ------------------------------------------- |
| `/`           | Dashboard — lists all saved resumes         |
| `/auth`       | Sign in / sign out with Puter               |
| `/upload`     | Upload a resume PDF and trigger AI analysis |
| `/resume/:id` | View AI feedback for a specific resume      |
| `/wipe`       | Clear all stored resume data                |

> There is no traditional REST API. All data operations go through the Puter.js SDK (cloud filesystem and KV store) directly from the client.

---

## Data Model

```ts
interface Resume {
  id: string;
  companyName?: string;
  jobTitle?: string;
  imagePath: string; // path to preview PNG in Puter filesystem
  resumePath: string; // path to original PDF in Puter filesystem
  feedback: Feedback;
}

interface Feedback {
  overallScore: number; // 0–100
  ATS: Category;
  toneAndStyle: Category;
  content: Category;
  structure: Category;
  skills: Category;
}

interface Category {
  score: number; // 0–100
  tips: Tip[];
}

interface Tip {
  type: "good" | "improve";
  tip: string;
  explanation: string;
}
```

---

## Local Setup

### Prerequisites

- **Node.js** 20 or later
- **npm** 10 or later
- A [Puter](https://puter.com/) account (free) — used for auth, storage, and AI

> No API keys or `.env` file are required. Authentication and AI access are handled entirely through the Puter.js SDK loaded at runtime.

### Install

```bash
git clone https://github.com/your-username/ai-resume-analyzer.git
cd ai-resume-analyzer
npm install
```

### Run in Development

```bash
npm run dev
```

The app will be available at `http://localhost:5173`.

### Build for Production

```bash
npm run build
npm run start
```

### Run with Docker

```bash
docker build -t resumind .
docker run -p 3000:3000 resumind
```

---

## Deployment

The project ships with a Vercel preset via `@react-router/vercel`. To deploy:

1. Push the repository to GitHub.
2. Import the project in the [Vercel dashboard](https://vercel.com/).
3. Vercel will auto-detect the React Router framework preset — no configuration needed.
4. Deploy.

---

## How It Works

1. **Sign in** with your Puter account.
2. On the **Upload** page, enter the company name, job title, and paste the job description.
3. Drop or select your resume PDF.
4. The app converts the first page to a high-resolution PNG using PDF.js (client-side, no server upload needed).
5. Both files are uploaded to your Puter cloud storage.
6. The preview image and job context are sent to Claude Sonnet 4 via the Puter AI API with a structured prompt requesting JSON feedback.
7. The feedback JSON is saved to Puter KV storage under a UUID key.
8. You are redirected to the feedback page where scores and tips are displayed.

---

## Scripts

| Command             | Description                |
| ------------------- | -------------------------- |
| `npm run dev`       | Start dev server with HMR  |
| `npm run build`     | Production build           |
| `npm run start`     | Serve production build     |
| `npm run typecheck` | Run TypeScript type checks |

---
