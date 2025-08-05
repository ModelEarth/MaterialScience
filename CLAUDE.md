# MRIO CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this MaterialScience webroot repo for Multi-Region Input-Output (MRIO).

## Repository Architecture

This is a comprehensive Multi-Region Input-Output (MRIO) system using 721 environmental factors across global trade industries, built around the USEEIO (US Environmentally-Extended Input-Output) framework. The repository contains 15 submodules organized in a layered architecture:

### Core Processing Pipeline
1. **Raw Data** → `exiobase` (Python/MRIO processing) 
2. **Model Building** → `useeior` (R package for economic models)
3. **API Layer** → `useeio_api` (Go REST service)
4. **Client Libraries** → `useeio.js` (TypeScript/JavaScript)
5. **Visualization** → `useeio-widgets` (React), `io` (charts)
6. **Web Framework** → `localsite` (navigation/display)

### Key Submodules
- **localsite**: Static web framework for navigation and display
- **team**: Rust API for Azure Postgres
- **exiobase**: Python MRIO data processing pipeline in exiobase/tradeflow
- **products**: Product-level environmental impact data pull from BuildingTransparancy API
- **io**: Input-output charts and visualization tools
- **useeior**: R package (core computational engine)
- **useeio_api**: Go-based REST API server
- **useeio.js**: TypeScript library for JSON model data
- **useeio-widgets**: React visualization components

## Development Commands

### Local Development
Start local web server (required for viewing web components):
```bash
python -m http.server 8888
```

Key local URLs:
- http://localhost:8888/products/ - Product impact data
- http://localhost:8888/useeio.js/footprint/ - State-level footprint reports  
- http://localhost:8888/io/ - Input-output charts
- http://localhost:8888/profile/ - Profile analysis tools
- http://localhost:8888/localsite/ - Navigation framework

### Python Environment Setup
```bash
# MacOS
python3 -m venv env
source env/bin/activate

# Windows
python -m venv env
env\Scripts\activate
```

### Deploy all submodules:
```bash
git submodule foreach 'git add . && git commit -m "Deploy updates" && (git push origin main || git push origin master)'
```
**Note**: This only pushes to individual submodules. It does NOT update the upstream parent repository with new submodule references. Use individual "commit [submodule name]" or "PR [submodule name]" for complete workflows.

### PR [submodule name]:
Create a pull request for a submodule when you lack collaborator privileges:
```bash
cd [submodule name]
git add . && git commit -m "Description of changes"
git push origin feature-branch-name
gh pr create --title "Update [submodule name]" --body "Description of changes"
cd ..
```

### Submodule Management

#### IMPORTANT: "commit [submodule name]" Command Requirements
When a user says "commit [submodule name]", you MUST complete ALL these steps in sequence:

1. **Navigate to submodule**: `cd [submodule name]`
2. **Add and commit in submodule**: `git add . && git commit -m "Description of changes"`
3. **Push submodule changes**: `git push` 
4. **Return to parent**: `cd ..`
5. **Add submodule reference**: `git add [submodule name]`
6. **Commit parent update**: `git commit -m "Update [submodule name] submodule"`
7. **Push parent changes**: `git push`
8. **If no push access**: Submit PR for parent repository instead

**⚠️ CRITICAL**: Steps 5-7 are REQUIRED. Simply committing to the submodule is incomplete - the parent repository must also be updated with the new submodule reference.

#### Quick Commands for Submodules
- **"commit [submodule name]"**: Complete 8-step workflow above
- **"push [submodule name]"**: Only push submodule changes (steps 1-3)
- **"PR [submodule name]"**: Create pull request workflow

When displaying "Issue Resolved" use the same checkbox icon as "Successfully Updated"

#### Additional Notes
- Allow up to 12 minutes to pull repos (large repositories)
- Always verify both submodule AND parent repository are updated

### Git Commit Guidelines
- **NEVER add Claude Code attribution or co-authored-by lines to commits**
- **NEVER add "🤖 Generated with [Claude Code]" or similar footers**
- Keep commit messages clean and focused on the actual changes
- Include a brief summary of changes in the commit text

### Quick Commands

When you type "restart", run this single command to restart the server in seconds:
```bash
cd $(git rev-parse --show-toplevel) && pkill -f "node.*index.js"; (cd server && NODE_ENV=production nohup node index.js > /dev/null 2>&1 &)
```

When you type "quick", add the following permissions block to setting.local.json under allow. "
When you type "confirm" or less quick", remove it:
```json
[
  "Bash(yarn setup)",
  "Bash(npx update-browserslist-db:*)",
  "Bash(mkdir:*)",
  "Bash(yarn build)",
  "Bash(cp:*)",
  "Bash(npx prisma generate:*)",
  "Bash(npx prisma migrate:*)",
  "Bash(pkill:*)",
  "Bash(curl:*)",
  "Bash(git submodule add:*)",
  "Bash(rm:*)",
  "Bash(find:*)",
  "Bash(ls:*)",
  "Bash(git add:*)",
  "Bash(git commit:*)",
  "Bash(git push:*)"
]
```

## Technology Stack

### Languages & Frameworks
- **R**: Economic modeling and analysis (useeior, useeio-state)
- **TypeScript/JavaScript**: Web interfaces with React/Material-UI (useeio-widgets, useeio.js)
- **Python**: MRIO data processing with pandas/pymrio (exiobase, useeiopy)
- **Go**: High-performance API server (useeio_api)

### Build Tools
- **npm/webpack**: JavaScript bundling and dependencies
- **rollup**: Library bundling for useeio.js
- **R package system**: For R-based components

## Important Processing Notes

### Exiobase Processing Pipeline
The exiobase tradeflow processing has strict ordering requirements:
1. `industryflow.py` → `industryflow_finaldemand.py` → `industryflow_factor.py`
2. `create_full_trade_factors.py` → `create_trade_impacts.py` → `trade_resources.py`

Configuration is centralized in `exiobase/tradeflow/config.yaml` for countries, trade flows, and processing parameters.

### Model Variants
- **Domestic flows**: Use all 721 environmental factors (comprehensive analysis)
- **International flows**: Use 120 selected factors (performance-optimized)
- **State-level models**: Specialized processing in useeio-state

## Key Configuration Files
- `exiobase/tradeflow/config.yaml`: Central MRIO processing configuration
- `package.json` files: npm dependencies and build scripts
- `DESCRIPTION` files: R package metadata and dependencies  
- Model specification files in `useeior/inst/extdata/modelspecs/`

## Deep Technical Details: USEEIO Submodules

### useeior (R Package) - Core Model Builder

**Primary Function**: `buildModel()` in `useeior/R/BuildModel.R`
**Export Function**: `writeModelforAPI()` in `useeior/R/WriteModel.R`

**Data Structures**:
- Model object: Named R list with 60+ components (matrices, metadata, specifications)
- Key matrices: V, U, A, L (economic), B, C, D, M, N (environmental)
- Export formats: Binary `.bin` files for matrices, CSV for metadata, JSON for demand vectors

**API Output Structure**:
```
model/
├── matrices/ (binary .bin files)
├── demands/ (JSON demand vectors)  
├── data/ (CSV metadata files)
└── model_info.json
```

### useeio_api (Go API) - Data Serving Layer

**Main Server**: `useeio_api/go/useeioapi/main.go`

**Key Endpoints**:
- `/api/models` - List available models
- `/api/{model}/sectors` - Sector metadata
- `/api/{model}/indicators` - Environmental indicators
- `/api/{model}/matrix/{matrix}` - Matrix data (A, B, C, D, L, M, N)
- `/api/{model}/calculate` - POST calculation endpoint

**Calculation Format**:
```json
// Input
{
  "perspective": "DIRECT|FINAL", 
  "demand": [{"sector": "123456/US", "amount": 1000000}]
}

// Output  
{
  "indicators": ["GHG", "ACID", "WATR"],
  "sectors": ["123456/US", "234567/US"],
  "data": [[1.2, 3.4], [5.6, 7.8]],
  "totals": [4.6, 13.4]
}
```

### useeio.js (TypeScript Library) - Frontend Data Access

**Main Entry**: `useeio.js/src/useeio.ts`
**Build Output**: `dist/useeio.js` (UMD bundle)

**Key Classes**:
- `WebModel` - High-level model interface
- `WebApiConfig` - Configuration with static file support

**Configuration**:
```ts
interface WebApiConfig {
  endpoint: string;      // API base URL
  model?: string;        // Model ID
  apikey?: string;       // Optional API key  
  asJsonFiles?: boolean; // Static files mode
}
```

**Matrix Operations** (`useeio.js/src/calc.ts`):
- `CommodityVector.directImpactsOf()` - D × demand vector
- `CommodityVector.directRequirementsOf()` - A × demand vector

### useeio-widgets (React Components) - Visualization Layer

**Main Widgets** (`useeio-widgets/src/widgets/`):
- `impact-chart.tsx` - Multi-indicator bar charts
- `sector-list.tsx` - Sector selection/filtering  
- `settings.tsx` - Analysis configuration
- `matrix-selector.tsx` - Matrix data viewer

**Configuration Interface**:
```ts
interface Config {
  model?: string;           // Model ID
  sectors?: string[];       // Sector codes to analyze
  indicators?: string[];    // Environmental indicators
  perspective?: ResultPerspective; // DIRECT|FINAL
  analysis?: DemandType;    // Production|Consumption
  year?: number;           // Analysis year
}
```

**Dependencies**: React 17, Material-UI 4, ApexCharts 3.45

### useeio-state (State-level Processing) - Regional Extensions

**Additional Functions** (`useeio-state/R/StateEEIOCalculations.R`):
- `calculateStateCBE()` - State consumption-based emissions
- `getStateUsebyType()` - State final demand by category
- `adjustDollarMatrixPriceYear()` - Price year adjustments

**Configuration**: `useeio-state/model_specs/` - State-specific YAML files

**Key Differences from useeior**:
- Regional scope: State vs national economic data
- Additional demand categories: State vs federal government
- Rest-of-US calculations: RoUS economic interactions

### useeio-json (Pre-generated JSON) - Static Data Repository

**Structure**:
```
models/2020/
├── models.json                    // Model metadata
├── sectorcrosswalk.csv           // Code mappings
├── {MODEL_ID}/
│   ├── indicators.json           // Environmental indicators
│   ├── sectors.json             // Economic sectors
│   ├── flows.json              // Elementary flows
│   ├── demands.json            // Available demand vectors
│   └── matrix/
│       └── A.json, B.json, C.json, D.json, L.json, M.json, N.json
```

**Usage**: Direct HTTP serving of pre-computed model data

### useeiopy (Python Package) - Alternative Builder

**Main Functions** (`useeiopy/useeiopy/__init__.py`):
- `build_EEIO_model()` - Assembles models using IO-Model-Builder
- `calculate_EEIO_model()` - Runs impact calculations  
- `write_EEIO_result()` - Exports results to CSV

**Status**: Alpha development, alternative to useeior

## Static File Generation Strategy

**Replacement of useeio_api**: The USEEIO ecosystem supports static file generation through:

1. **useeior** exports via `writeModelforAPI()` → JSON files
2. **useeio-json** demonstrates static file hosting
3. **useeio.js** supports `asJsonFiles: true` mode
4. **useeio-widgets** work identically with static data

**Implementation**:
```r
# Generate static files
model <- buildModel('USEEIOv2.0.1-411')
writeModelforAPI(model, '/path/to/static/files/')
```

```ts
// Configure for static files
const config: WebApiConfig = {
  endpoint: './api',
  model: 'USEEIOv2.0.1-411', 
  asJsonFiles: true
}
```

## International Data Integration

**Current US State Implementation** (`useeio.js/footprint/states.html`):
- Tabulator.js 5.2.7 for data display
- Batch processing (5 states at a time)
- Hash-based filtering (`#state=NY,CA`)
- Economic output and employment calculations

**Exiobase Data Structure** (`exiobase/tradeflow/year/2019/US/domestic/`):
- `industryflow.csv` - Economic flows between countries/industries
- `trade_employment.csv` - Employment impacts by trade flow
- `trade_impacts.csv` - Environmental impacts by trade flow

**Integration Strategy**:
1. **Data Mapping**: Country codes (US, CN, DE) → readable names  
2. **CSV Loading**: Parse Exiobase CSV files for international data
3. **Aggregation**: Sum flows/impacts by country from trade CSVs
4. **Toggle UI**: Radio buttons to switch US/international views
5. **Consistent Formatting**: Reuse existing `formatCell()` functions