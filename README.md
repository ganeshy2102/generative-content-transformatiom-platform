# Generative Content Transformation Platform ⚡

A modern, full-stack web application built with **Next.js (App Router)**, **React**, **Tailwind CSS**, and **SQLite (better-sqlite3)**. It enables users to transform raw notes, transcripts, and documents into executive summaries, multi-lingual translations, publication-ready blog posts, and actionable key points.

---

## 🌟 Key Features

### 1. Frontend & User Experience
- **Responsive Navigation**: Top navbar and mobile drawer linking to **Home**, **Transformation Studio**, and **My History**.
- **Dark / Light Mode**: Instant, persistent theme toggle with smooth transitions and system preference detection.
- **Transformation Studio**:
  - Large, ergonomic `<textarea>` with real-time character and word counters.
  - Dropdown menu with pre-configured transformation types:
    - **Summarize**: Distills complex text into executive briefs.
    - **Translate**: Multi-lingual neural translation (Spanish, French, German, Japanese, Chinese, Hindi, Italian, etc.).
    - **Rewrite as a Blog Post**: Structures raw thoughts into publication-ready articles.
    - **Extract Key Points**: Action-oriented takeaways and bullet points.
    - **Professional Polish**: Refines drafts for executive communications.
    - **Explain Like I'm 5 (ELI5)**: Simplifies technical concepts with vivid analogies.
    - **Social Media Thread**: Formats ideas into high-engagement threads.
  - Tone and length customization options.
  - Primary **Generate** button with spinning loader state and `Cmd+Enter` / `Ctrl+Enter` shortcut.
  - Quick sample text selector (*Product Launch Memo*, *Research Abstract*, *Marketing Notes*).
  - Output display with **Copy to Clipboard** (instant feedback & toast), **Download as Markdown**, and **Send to Input** iteration.
- **My History Dashboard**:
  - Dual view modes: **Table View** and **Grid View**.
  - Real-time search across original input text and transformed output text.
  - Filter by transformation type and sort by date.
  - Metrics summary banner: Total saved transformations, words processed, and active transformation types.
  - **Inspect Modal**: Side-by-side comparison of full original text vs. transformed text with one-click copy and "Open in Studio".
  - One-click deletion with confirmation.

### 2. Backend & Architecture
- **API Route `/api/transform`**:
  - Validates payload and processes transformation requests.
  - Intelligent context-aware mock engine that works immediately with **zero configuration**.
  - Drop-in support for **OpenAI (GPT-4o / GPT-4o-mini)** and **Google Gemini (1.5 Flash / 1.5 Pro)**.
  - Automatically saves every generation to the database table.
- **API Route `/api/history`**:
  - `GET`: Retrieve all transformation records with optional search, type filtering, pagination, and summary statistics.
  - `POST`: Manually create/save transformation records.
  - `DELETE`: Remove records by query param or body.
- **API Route `/api/history/[id]`**:
  - `GET`: Retrieve individual transformation by ID.
  - `DELETE`: Permanently remove transformation by ID.

### 3. Database & Storage
- Local SQLite database stored at `data/transformations.db` using `better-sqlite3`.
- Strict schema conforming to specifications:
  - `id` (TEXT PRIMARY KEY)
  - `original_text` (TEXT NOT NULL)
  - `transformed_text` (TEXT NOT NULL)
  - `transformation_type` (TEXT NOT NULL)
  - `created_at` (TIMESTAMP DEFAULT CURRENT_TIMESTAMP)
- Resilient fallback engine: Automatically handles storage failover to atomic JSON store if binary constraints arise.

---

## 🚀 Getting Started

### 1. Installation
The dependencies are already installed. If starting in a new environment, run:
```bash
npm install
```

### 2. Running in Development Mode
Start the Next.js development server:
```bash
npm run dev
```
Open [http://localhost:3000](http://localhost:3000) in your browser.

### 3. Running in Production Mode
Build and run the optimized production bundle:
```bash
npm run build
npm run start
```

---

## 🔑 Connecting Real AI Providers (OpenAI & Google Gemini)

The application includes an intelligent mock engine by default so you can test all features immediately without any API keys.

To connect live AI models, open `.env.local` and configure your keys:

### Option A: OpenAI (GPT-4o / GPT-4o-mini)
1. Generate an API key at [OpenAI Platform](https://platform.openai.com/api-keys).
2. Edit `.env.local`:
   ```bash
   AI_PROVIDER="openai"
   OPENAI_API_KEY="sk-proj-your-openai-api-key"
   OPENAI_MODEL="gpt-4o-mini"
   ```

### Option B: Google Gemini (Gemini 1.5 Flash / Pro)
1. Generate an API key at [Google AI Studio](https://aistudio.google.com/app/apikey).
2. Edit `.env.local`:
   ```bash
   AI_PROVIDER="gemini"
   GEMINI_API_KEY="AIzaSy-your-gemini-api-key"
   GEMINI_MODEL="gemini-1.5-flash"
   ```

> **Code Reference:** You can inspect or modify the prompt engineering and API request handlers in [lib/ai-service.ts](file:///Users/gani/generative%20content%20transformation%20platform/lib/ai-service.ts#L65-L145).

---

## 📁 Project Structure

```
├── app/
│   ├── api/
│   │   ├── history/
│   │   │   ├── [id]/route.ts       # GET/DELETE individual transformation
│   │   │   └── route.ts            # GET/POST/DELETE transformation history
│   │   └── transform/
│   │       └── route.ts            # POST: AI transformation & auto-save to DB
│   ├── history/
│   │   └── page.tsx                # My History dashboard (table/grid, search, modal)
│   ├── studio/
│   │   └── page.tsx                # Transformation Studio (input, options, output)
│   ├── globals.css                 # Tailwind directives, animations & dark styles
│   ├── layout.tsx                  # Root layout with theme provider & header/footer
│   └── page.tsx                    # Modern landing page & feature showcases
├── components/
│   ├── Footer.tsx                  # System status and links footer
│   ├── Navbar.tsx                  # Top navigation bar with theme toggle & mobile menu
│   ├── ThemeProvider.tsx           # React Context for dark/light mode
│   ├── ThemeToggle.tsx             # Sun/Moon animated theme toggle button
│   ├── Toast.tsx                   # Floating toast notification system
│   └── TransformationModal.tsx     # Side-by-side original vs transformed inspection modal
├── data/
│   └── transformations.db          # Persistent SQLite database file
├── lib/
│   ├── ai-service.ts               # Mock engine + OpenAI & Gemini SDK integrations
│   └── db.ts                       # SQLite schema, queries, indexing, and resilience
├── .env.example                    # Template environment variables
├── .env.local                      # Local environment configuration
├── next.config.mjs                 # Next.js configuration
├── tailwind.config.ts              # Tailwind CSS configuration with custom theme colors
└── tsconfig.json                   # TypeScript configuration
```

---

## 📡 API Reference

### 1. Transform Content
- **Endpoint**: `POST /api/transform`
- **Request Body**:
  ```json
  {
    "text": "Raw notes or content to transform...",
    "type": "Summarize",
    "targetLanguage": "Spanish", // Optional (for Translate)
    "tone": "Professional",       // Optional
    "length": "standard"          // Optional: "concise" | "standard" | "detailed"
  }
  ```
- **Response**:
  ```json
  {
    "success": true,
    "data": {
      "id": "tx_3a6335ec3e8d4f3f",
      "original_text": "...",
      "transformed_text": "...",
      "transformation_type": "Summarize",
      "created_at": "2026-11-15T18:18:40.000Z",
      "meta": {
        "provider": "mock",
        "model": "Mock-Engine-v2",
        "processingTimeMs": 620,
        "wordCount": 85
      }
    }
  }
  ```

### 2. Fetch Transformation History
- **Endpoint**: `GET /api/history?search=keyword&type=Summarize&includeStats=true`
- **Response**:
  ```json
  {
    "success": true,
    "count": 4,
    "data": [ ... ],
    "stats": {
      "total": 4,
      "byType": { "Summarize": 2, "Translate": 1, "Rewrite as a Blog Post": 1 },
      "totalWordsProcessed": 350
    }
  }
  ```

### 3. Delete Transformation Record
- **Endpoint**: `DELETE /api/history/[id]` or `DELETE /api/history?id=[id]`
- **Response**:
  ```json
  {
    "success": true,
    "message": "Transformation record deleted successfully.",
    "id": "tx_3a6335ec3e8d4f3f"
  }
  ```
