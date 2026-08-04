# Camptel AI: Using Google AI to Help Colleges Make Data-Driven Decisions

*By Shreyas Pawar, Veer Shah, and Atharva Mahajan — Built for the Google Gen AI Academy APAC Edition, Cloud Hackathon 2026*

---

## Introduction

We are a team of developers who care about one simple question: **why do colleges make important decisions on guesswork?**

Every semester, institutions collect enormous amounts of data — attendance records, exam marks, LMS engagement, placement scores, and room bookings — yet most of this data sits unused in spreadsheets. Administrators, deans, and placement officers end up making critical decisions about student retention, training budgets, and infrastructure based on intuition rather than evidence.

So for the **Google Gen AI Academy APAC Edition**, we built **Camptel AI** — a decision intelligence platform that turns raw campus data into actionable insights, powered by Google Cloud's AI and data stack.

## The Problem

Higher education institutions face three connected challenges:

1. **Data is scattered.** Attendance lives in one system, marks in another, placements in a third. Nobody sees the full picture of a student's journey.
2. **Insights are slow.** Even when data is centralised, asking a question requires a data analyst who knows SQL. By the time a report is ready, the moment for action has passed.
3. **Intervention is reactive.** At-risk students are usually identified only after they fail — not before.

The result? Students slip through the cracks, training budgets are spent where they aren't needed, and classrooms sit half-empty while others are overbooked.

## Our Solution: Camptel AI

Camptel AI is a web platform with three analytical dashboards and a conversational AI assistant, designed so that *non-technical staff* can make data-driven decisions in minutes.

### 1. Decision Intelligence Dashboard

- **Academic Risk Panel** — identifies students at risk of falling behind using a weighted formula of attendance, average marks, and LMS engagement. Admins can filter by course, adjust the risk threshold, view a top-10 risk chart, and export the list as CSV for counsellors to act on.
- **Placement Readiness Panel** — computes a readiness index for every student from technical scores, communication skills, CGPA, and internship experience, then groups students into Ready / Needs Work / At Risk buckets so training resources go where they matter most.
- **Classroom Utilisation Panel** — a date-wise heatmap of room usage with conflict alerts for rooms running at 90%+ utilisation, so schedulers can spot overbooked rooms before chaos happens.

### 2. AI Assistant

The flagship feature: a chat interface where anyone can ask questions in plain English — *"Show the top 10 students by risk score"* or *"Which rooms are over 90% utilised next week?"* — and get an instant, human-readable answer. Under the hood, **Gemini 2.5 Flash** translates the question into BigQuery SQL, executes it against live data, and summarises the results with recommendations. For transparency, the generated SQL and raw rows are shown alongside the answer.

### 3. Decision Challenges

A library of pre-built analytical scenarios (academic, placement, infrastructure) that run live queries with one click — making the platform useful the moment you log in.

## Technology Stack

| Layer | Technology |
|-------|-----------|
| **Framework** | Next.js 14 (App Router) + TypeScript |
| **Styling** | Tailwind CSS, Recharts |
| **Data warehouse** | Google BigQuery |
| **Generative AI** | Google Gemini 2.5 Flash (`@google/genai`) |
| **Acceleration** | NVIDIA RAPIDS cuDF (GPU) |
| **Deployment** | Vercel |

## Architecture

The platform follows a clean API-driven architecture:

- **Frontend** — Next.js server components render the dashboard; client components handle interactivity, charts, and CSV exports.
- **API layer** — Server-side API routes (`/api/at-risk`, `/api/placement`, `/api/classroom`) run parameterised, injection-safe queries against BigQuery.
- **AI pipeline** — `POST /api/ask-gemini` orchestrates a three-step flow: schema-aware prompt → Gemini generates SQL → validation → BigQuery execution → Gemini summarises results.
- **Data** — Synthetic campus datasets (50,000 students, 50,000 placement records, 10,000 room bookings) generated with a Python script and loaded into BigQuery.

## Challenges We Faced

**1. Getting Gemini to write correct SQL.** The first attempts produced plausible-looking but invalid queries. We fixed this by injecting the actual BigQuery schema into the prompt and adding server-side validation (the query must start with `SELECT`), so the model always knows exactly which tables and columns exist.

**2. Secure credentials on Vercel.** Service-account keys can't be stored as plain files in a serverless environment. We moved to a base64-encoded service account key stored as an environment variable, with graceful fallback to mock data when BigQuery isn't configured — so the demo never breaks.

**3. Performance at scale.** Querying large datasets (1.2 million attendance rows) with Pandas was painfully slow. We benchmarked **NVIDIA RAPIDS cuDF** on a GPU: the same risk-scoring task dropped from **29.8 seconds to 2.6 seconds — an 11.5x speedup** — and we built a dedicated performance page that demonstrates this side-by-side.

## Results

Camptel AI delivers impact across four areas:

- **Academic** — early identification of at-risk students enables timely intervention and better retention.
- **Placement** — readiness tracking focuses training budgets on the branches and skills that need it most.
- **Operations** — proactive conflict alerts prevent classroom double-booking.
- **Accessibility** — non-technical staff can query institutional data in plain English, no SQL required.

## Future Improvements

- **Real-time alerts** — automated WhatsApp/email notifications when a student's risk score crosses a threshold.
- **Multi-campus support** — roll out to multiple institutions with per-campus datasets.
- **Forecasting** — use Gemini to predict next-semester dropout and placement trends.
- **Streaming ingestion** — connect live LMS and attendance systems so insights update in real time.

## Conclusion

Camptel AI taught us that the most valuable AI applications aren't always the flashiest — they're the ones that put insight into the hands of people who need it. By combining **BigQuery's scale**, **Gemini's language understanding**, and **GPU-accelerated analytics**, we built a platform that helps colleges make smarter, faster decisions about the students they serve.

## Links

- **GitHub:** https://github.com/ShreyasP10/camptel-ai
- **Live Demo:** [add your Vercel URL here]
