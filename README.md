📄 AI-Powered ATS Resume Analyzer
A high-performance, AI-driven application designed to help job seekers bridge the gap between their resumes and job descriptions. This tool performs deep semantic analysis to provide actionable feedback, matching scores, and optimization tips.

✨ Key Features
Intelligent Parsing: Converts complex PDF resumes into structured data using OCR and Image Analysis.

Semantic Matching: Goes beyond keyword matching by using AI to understand the context and intent of your experience vs. the job requirements.

Instant ATS Scoring: Provides a percentage-based score reflecting how well the resume is optimized for specific Job Descriptions.

Actionable Insights: Generates specific suggestions on missing skills, phrasing improvements, and formatting issues.

Privacy-First AI: Built with Puter.js, utilizing client-side cloud execution which keeps your data secure without central server-side logging of sensitive documents.

🛠️ Tech Stack
Frontend: React Router v7 (The evolution of Remix) for high-performance Server-Side Rendering (SSR).

Styling: Tailwind CSS for a modern, responsive "Glassmorphism" UI.

AI Engine: Puter.js for cloud-native AI models, File System (FS), and Key-Value (KV) storage.

Deployment: Vercel leveraging Serverless Functions and Edge caching.

Language: TypeScript for robust, type-safe development.

🚀 Deployment & Infrastructure
This application is optimized for Vercel's Serverless Architecture.

Global Performance: Utilizing React Router v7's Vercel Preset to handle SSR and edge-ready API routes.

Serverless Scaling: Configured with a maxDuration: 60 export in route handlers to ensure complex AI analysis of large PDF files is completed without function timeouts.

No-Backend Strategy: By using Puter.js, the app maintains a "thick client" architecture where AI tasks and file storage are handled through the user's secure browser session, eliminating the need for traditional server-side API keys.

💻 Local Setup
Clone the Repo:

Bash
git clone https://github.com/vsingh2201/ai-resume-analyzer.git
cd ai-resume-analyzer
Install Dependencies:

Bash
npm install
Run Development Server:

Bash
npm run dev
Build for Production:

Bash
npm run build
🧠 How It Works
PDF Conversion: The app uses a custom convertPdfToImage utility to ensure the AI can "see" the exact layout and formatting of your resume, just like a modern ATS.

Instruction Synthesis: A dynamic prompt engine (prepareInstructions) combines the Job Description and the Resume content to guide the AI model.

Cloud-Native Processing: Puter's ai.chat is invoked to process the data, returning a structured JSON response that is instantly rendered into the UI.
