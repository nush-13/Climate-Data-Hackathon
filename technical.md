# Surya Sutra (सूर्य सूत्र) — Technical Architecture & Implementation Documentation

## 1. System Overview

**Surya Sutra** is an AI-powered spatial intelligence and decision-support platform engineered for district-level renewable energy planning, microgrid balancing, and socioeconomic equity optimization. 

Using high-resolution geospatial telemetry, satellite solar irradiance data (NASA POWER), feeder-level electricity demand contours, and socioeconomic demographic indicators (Census & NFHS-5), Surya Sutra bridges the gap between raw data and executable clean-energy infrastructure plans.

### Core Objectives
1. **Spatial Intelligence**: Multi-layered GIS mapping across Solar Potential, Electricity Demand, Socioeconomic Equity, and Multi-criteria Opportunity.
2. **Microgrid Energy Balancing**: Automated discovery of surplus and deficit zones with peer-to-peer power transfer optimization.
3. **Multi-Criteria Decision Analysis (MCDA)**: Scenario-driven site ranking factoring in solar yield, grid interconnection cost, population density, and equity priority.
4. **Long-Term Trajectory Modeling (Usha Urja)**: 24-hour diurnal dispatch curves and multi-year growth projections.
5. **Dossier & Plan Generation**: Project Passports and one-click Executive Brief export (PDF).

---

## 2. Technology Stack

| Layer | Technology | Purpose |
| :--- | :--- | :--- |
| **Frontend Framework** | React 19 (`react`, `react-dom`) | Declarative component model and reactive state architecture |
| **Language** | TypeScript (~5.8) | Static typing, interface definitions, and compile-time verification |
| **Build Tooling** | Vite 6 | Development server (HMR disabled for sandbox stability) & production bundling |
| **Styling Engine** | Tailwind CSS v4 (`@tailwindcss/vite`) | Utility-first, dark-theme styling (`#0a0a0b`, `#121214`, `#18181b`) |
| **GIS & Mapping** | Leaflet 1.9 (`leaflet`, `@types/leaflet`) | Interactive cartography, polygon overlays, and custom HTML markers |
| **Animation** | Motion (`motion/react`) | Transition layout animations and micro-interactions |
| **Icons** | Lucide React (`lucide-react`) | Standardized iconography |
| **Report Generation** | `jspdf` & `html2canvas` | Client-side export of executive dossiers into printable PDF documents |
| **AI Integration** | `@google/genai` (SDK 2.4.0) | Server-side Gemini intelligence and reasoning capabilities |

---

## 3. Architecture & Component Hierarchy

```
src/
├── App.tsx                      # Root coordinator, active view routing, top-level state
├── main.tsx                     # DOM mounting entry point
├── index.css                    # Tailwind CSS v4 imports and custom map styling
├── types/
│   └── index.ts                 # Core TypeScript types & data schemas
├── data/
│   └── mockData.ts              # Telemetry, energy zones, candidate sites, and scenarios
└── components/
    ├── layout/
    │   ├── Header.tsx           # Global navigation bar & view selector
    │   └── NavigationSidebar.tsx# Collapsible left navigation drawer
    ├── map/
    │   ├── RealMathuraMap.tsx   # Primary Leaflet GIS map with layers & transfer lines
    │   ├── MapControls.tsx      # Layer/metric switcher (Solar, Demand, Equity, Opportunity, Data)
    │   ├── MapInspector.tsx     # Context-aware slide-out sidebar for zone/site details
    │   ├── MapLegend.tsx        # Dynamic legend adapted to selected heatmap metric
    │   ├── MapDataPanel.tsx     # Real-time tabular data drawer with CSV export
    │   ├── MapInsightIndicator.tsx # Quick regional metric highlights
    │   └── IndiaMap.tsx         # National context map
    └── views/
        ├── LandingView.tsx      # Dark-themed overview, methodology & product walk-through
        ├── PlannerDashboardView.tsx # Central planner view embedding the GIS map
        ├── EnergyBalanceView.tsx# Deep-dive microgrid dispatch & inter-zone transfers
        ├── EquityLensView.tsx   # Socioeconomic & NFHS-5 access metrics
        ├── ScenarioComparisonView.tsx # Counterfactual scenario comparison (A vs B)
        ├── UshaUrjaForecastView.tsx   # 24h diurnal & 2026–2030 energy projections
        ├── ProjectPassportView.tsx    # Detailed candidate project dossiers
        └── FinalPlanExporterView.tsx  # Executive briefing & automated PDF generation
```

---

## 4. Data Models & Schemas (`src/types/index.ts`)

### 4.1. Energy Zone (`EnergyZone`)
Represents an administrative or electrical sub-district division:
```typescript
interface EnergyZone {
  id: string;                    // e.g. "MTH-01"
  code: string;                  // Short code identifier
  name: string;                  // e.g. "Goverdhan Sector"
  tehsil: string;                // Administrative tehsil
  lat: number;                   // Centroid latitude
  lng: number;                   // Centroid longitude
  solarPotential: number;        // Normalized score 0-100
  solarScore: number;            // Explicit solar score 0-100
  solarRadiationKwh: number;     // NASA GHI in kWh/m²/day (e.g. 5.72)
  demandIntensity: number;       // Feeder demand index 0-100
  peakDemandMw: number;          // Peak load in MW
  equityPriority: number;        // NFHS-5 & socioeconomic equity score 0-100
  costScore: number;             // Interconnection & land cost score 0-100
  overallOpportunity: number;    // Composite ranking 0-100
  generationKwh: number;         // Current generation capacity (kWh)
  demandKwh: number;             // Baseline demand (kWh)
  balanceKwh: number;            // generationKwh - demandKwh (+: surplus, -: deficit)
  status: 'surplus' | 'deficit' | 'balanced';
  insightSummary: string;        // AI-generated spatial summary
  candidateSiteIds: string[];    // Associated candidate project IDs
  rooftopSuitabilityScore?: number;
  groundSuitabilityScore?: number;
  areaCategory?: string;
}
```

### 4.2. Candidate Project Site (`CandidateSite`)
Represents a vetted physical site for renewable infrastructure:
```typescript
interface CandidateSite {
  id: string;
  name: string;
  zoneId: string;
  type: 'rooftop' | 'ground_mount' | 'agri_pv' | 'floating_solar' | 'microgrid_hub';
  capacityMw: number;
  estAnnualGenerationGwh: number;
  estCostCr: number;             // In INR Crores
  equityScore: number;
  priorityRank: number;
  landType: string;
  gridDistanceKm: number;
  lat: number;
  lng: number;
  status: 'recommended' | 'under_review' | 'planned';
  beneficiaryHouseholds: number;
  co2ReductionTonsYr: number;
  roiYears: number;
  selectionRationale: string;
}
```

### 4.3. Microgrid Network Connection (`NetworkConnection`)
Models inter-zone high-voltage/medium-voltage balancing links:
```typescript
interface NetworkConnection {
  id: string;
  fromZoneId: string;            // Surplus donor zone
  toZoneId: string;              // Deficit recipient zone
  transferCapacityKwh: number;   // Active energy dispatched
  distanceKm: number;
  lossPercentage: number;        // Transmission loss (~3-5%)
  status: 'active' | 'recommended' | 'constrained';
}
```

---

## 5. Mathematical & Decision Algorithms

### 5.1. Multi-Criteria Opportunity Score (MCDA)
The composite opportunity score $S_{opp}$ for any zone or site is calculated dynamically from weighted policy variables:

$$S_{opp} = \left( w_{solar} \cdot S_{solar} \right) + \left( w_{equity} \cdot S_{equity} \right) + \left( w_{cost} \cdot S_{cost} \right) - \left( w_{demand} \cdot S_{deficit\_penalty} \right)$$

Where:
- $w_{solar} + w_{equity} + w_{cost} + w_{demand} = 1.0$
- $S_{solar} \in [0, 100]$: NASA POWER Global Horizontal Irradiance (GHI) index.
- $S_{equity} \in [0, 100]$: NFHS-5 rural household electrification gap & agricultural dependency.
- $S_{cost} \in [0, 100]$: Inverse distance to substation and terrain grade.

### 5.2. Microgrid Equilibrium & Loss Optimization
For a network with surplus donors $D = \{d_1, d_2, \dots\}$ and deficit sinks $K = \{k_1, k_2, \dots\}$:

1. **Net Balance Calculation**:
   $$\Delta E_z = E_{gen, z} - E_{dem, z}$$
2. **Transfer Optimization**:
   Minimizes total transmission loss $L_{total}$:
   $$L_{total} = \sum_{(i, j) \in T} P_{i \to j} \cdot \left( \alpha \cdot d_{ij} \right)$$
   Subject to:
   $$\sum_j P_{i \to j} \le \Delta E_i \quad \forall i \in D$$
   $$\sum_i P_{i \to j} \cdot (1 - \alpha \cdot d_{ij}) \le |\Delta E_j| \quad \forall j \in K$$

---

## 6. GIS Cartography & Conditional Visibility Engine

### 6.1. Map Layering & Dark Mode Tile Engine
- Uses Leaflet with CartoDB Dark Matter tile service (`https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png`) for high contrast against solar color scales.
- Polygons and bounding markers are rendered using SVG overlays and custom `L.divIcon` markers.

### 6.2. Strict Conditional Solar Score Visibility
To prevent visual clutter across multidimensional analyses:
- **Solar Tab (`heatmapMetric === 'solar'`)**: Renders the Solar Potential Score badge (`⚡ XX/100`), NASA GHI telemetry, and Rooftop/Ground suitability breakdown in both the map pins and the `MapInspector`.
- **Non-Solar Tabs (`demand`, `equity`, `opportunity`, `data`, `network`)**: Automatically suppresses the Solar Potential Score elements without mutating the underlying zone or site state, ensuring seamless switching without re-renders or data loss.

---

## 7. State Management & Navigation

Surya Sutra uses a centralized top-level state machine in `src/App.tsx`:

```
                    ┌─────────────────────────┐
                    │      App Component      │
                    │ (currentView, appState) │
                    └────────────┬────────────┘
                                 │
     ┌──────────────┬────────────┼────────────┬──────────────┐
     ▼              ▼            ▼            ▼              ▼
LandingView     PlannerView  EnergyBalance  EquityLens   ScenarioComp
     │              │            │            │              │
     ▼              ▼            ▼            ▼              ▼
Product Tour   RealMathuraMap DispatchView NFHS-5 View  A/B Optimizer
```

### View Identifiers (`AppView`)
- `'landing'`: Executive portal & methodology overview.
- `'planner'`: Core interactive GIS planning map with real-time zone inspectors.
- `'scenarios'`: Counterfactual scenario evaluator (Yield vs. Equity).
- `'energy_balance'`: Microgrid directional power transfer simulator.
- `'equity'`: Socioeconomic prioritization matrix.
- `'forecast'`: Usha Urja 24h & 2026–2030 trajectory engine.
- `'passport'`: Candidate site project dossiers.
- `'export'`: Print-ready executive briefing generator.

---

## 8. Export & Reporting Pipeline

The platform includes a client-side document generation engine:
1. **DOM Capture (`html2canvas`)**: Renders high-resolution vector and chart snapshots of the executive brief layout at `scale: 2`.
2. **Document Synthesis (`jspdf`)**: Builds multi-page, formatted PDF reports with district statistics, prioritized candidate project passports, and balance matrices.

---

## 9. Verification & Quality Assurance

- **Build Verification**: Compile checks performed via `tsc --noEmit` and Vite production bundling.
- **Responsive Layout**: Validated across mobile (375px+), tablet, and ultra-wide displays (1920px+).
- **Zero API Key Leaks**: All client-side code remains free of secret keys, complying with server-side proxy standards.
