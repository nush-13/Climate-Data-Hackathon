# SuryaSutra: District Energy Decarbonization & Equity Decision Engine

**SuryaSutra** is a decision-support and scenario-optimization platform designed for state energy planners, DISCOM managers, and policy makers in India (including MNRE and NITI Aayog advisors). It calculates nodal capital outlay allocations for utility-scale solar PV and Battery Energy Storage Systems (BESS) across state power distribution networks.

---

## Key Features

- **5-Step Decision Lifecycle**:
  1. **Region Reality & Baseline**: Assess regional grid realities, peak evening blackout durations across agricultural feeders, and establish state capital outlay budgets.
  2. **Planning & Tradeoffs**: Interactive multi-objective optimization engine evaluating Generation Efficiency vs. Social Equity & Rural Access with co-located BESS storage.
  3. **Locked Decision Output**: Generates formal executive decision memorandums suitable for NITI Aayog ICED & MNRE PM-KUSUM compliance.
  4. **Nodal Site Selection Rationale**: Plain-language site justification breakdown paired with hour-by-hour 24-hour solar PV and battery dispatch simulations across custom weather scenarios.
  5. **District Transformation Impact**: Before vs. After blackout elimination metrics and district-level execution matrices.

- **AI-Powered Policy Memorandum Generator**:
  - Leverages server-side **Gemini 3.6 Flash** (`@google/genai`) to generate structured executive policy briefs with executive summaries, capital outlay rationale, feeder outage impact analysis, and implementation milestones.

- **Print & PDF Export**:
  - Dedicated print stylesheets ensuring executive memos print cleanly without modal clipping or UI clutter.

---

## Tech Stack

- **Frontend**: React 18, TypeScript, Vite, Tailwind CSS, Lucide Icons, Recharts.
- **Backend**: Full-stack Express server with Vite dev middleware (`server.ts`).
- **AI Engine**: `@google/genai` SDK using `gemini-3.6-flash`.

---

## Getting Started

### Prerequisites

- Node.js 18+ or Bun
- Gemini API Key (`GEMINI_API_KEY`) configured in environment variables

### Local Development

1. Install dependencies:
   ```bash
    npm install
   ```

2. Copy `.env.example` to `.env` and configure your API key:
   ```env
   GEMINI_API_KEY=your_gemini_api_key_here
   ```

3. Start the development server:
   ```bash
   npm run dev
   ```

4. Open [http://localhost:3000](http://localhost:3000) in your browser.

---

## Build & Production

To build the project for production deployment:

```bash
npm run build
npm run start
```

---

## License

MIT
