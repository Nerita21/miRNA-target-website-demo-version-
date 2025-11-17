# OrthoMIRdb - Orthologous miRNA Target Database

![OrthoMIRdb](https://img.shields.io/badge/version-1.0.0-blue)
![Node.js](https://img.shields.io/badge/Node.js-ES%20Modules-green)
![Database](https://img.shields.io/badge/Database-SQLite-lightblue)

## Table of Contents
1. [Overview](#overview)
2. [Features](#features)
3. [Project Structure](#project-structure)
4. [Prerequisites](#prerequisites)
5. [Installation](#installation)
6. [Configuration](#configuration)
7. [Running the Application](#running-the-application)
8. [API Documentation](#api-documentation)
9. [Frontend Architecture](#frontend-architecture)
10. [Database Structure](#database-structure)
11. [Usage Guide](#usage-guide)
12. [Troubleshooting](#troubleshooting)
13. [Development](#development)
14. [Contributing](#contributing)

---

## Overview

**OrthoMIRdb** is a web-based database for discovering **orthologous miRNA targets across multiple species**. The application allows researchers to:

- Search for miRNA sequences across different organisms (Human, Mouse, Rat, Pig)
- Identify conserved target genes across species
- View target enrichment analysis and disease associations
- Generate interactive visualizations (histograms, gene networks, GO enrichment)
- Download detailed results for further analysis (under development)

This is particularly useful for comparative genomics studies, identifying evolutionarily conserved miRNA-target relationships, and understanding cross-species miRNA function.

### Supported Species:
- **hsa**: *Homo sapiens* (Human)
- **mmu**: *Mus musculus* (Mouse)
- **rno**: *Rattus norvegicus* (Rat)
- **ssc**: *Sus scrofa* (Pig)

---

## Features

### Core Functionality
-  **miRNA Search**: Auto-complete suggestions for miRNA queries
-  **Multi-species Selection**: Choose multiple species for comparative analysis
-  **Data Retrieval**: Fetch miRNA target data from SQLite database
-  **Interactive Results**: Switch between different data views (tabs)
-  **Advanced Visualization**:
  - Histogram showing target overlap percentages
  - Gene interaction networks
  - GO term enrichment analysis (Sunburst chart)
  - Disease association data

### User Interface
-  Bootstrap 5 responsive design
-  Mobile-friendly layout
-  Real-time data loading with spinners
-  Interactive charts with D3.js
-  DataTables for sortable, searchable data grids

### Backend Performance
-  API caching (2-minute TTL on suggestions endpoint)
-  Efficient SQLite queries
-  JSON-based API responses
-  RESTful API architecture

---

## Project Structure

```
orthomirdb/
├── public/                          # Frontend assets
│   ├── index_style2.html           # Home/search page
│   ├── results_style2.html         # Results/visualization page
│   ├── main2.js                    # Main frontend logic
│   ├── style1.css                  # Stylesheet
│   ├── lib/
│   │   ├── jquery/
│   │   │   └── jquery-3.7.1.min.js
│   │   └── utils/
│   │       ├── api.js              # API client functions
│   │       ├── charts.js           # D3.js visualization
│   │       ├── datatable.js        # DataTables initialization & tab switching
│   │       ├── navbar.js           # Navigation bar logic
│   │       ├── network.js          # Gene network visualization
│   │       ├── tables.js           # Table utilities
│   │       ├── ch.js               # Chart utilities
│   │       └── utils.js            # General utilities
│   └── outdated_ver/               # Previous versions
│
├── src/
│   ├── server.js                   # Express server entry point
│   ├── version1/
│   │   └── routes/
│   │       └── webRoutes.js        # API route definitions
│   ├── controllers/
│   │   ├── serviceController.js    # v1 business logic
│   │   └── serviceController_v2.js # v2 business logic (active)
│   ├── services/
│   │   ├── dataService.js          # Data loading & querying
│   │   ├── filterService.js        # Data filtering
│   │   ├── histogramService.js     # Histogram calculations
│   │   └── resultsService.js       # Results aggregation
│   └── database/
│       ├── config.json             # Database configuration
│       └── mirt_ortho_enrichment_metadata.db # SQLite database
│
├── tests/                          # Test files
│   ├── backend/
│   │   ├── api/
│   │   └── integration/
│   ├── frontend/
│   │   └── dom/
│   └── performance/
│
├── .env                            # Environment variables
├── package.json                    # Node.js dependencies
├── package-lock.json               # Locked versions
└── README.md                       # This file
```

---

## Prerequisites

### System Requirements
- **Node.js**: v14+ (v16+ recommended)
- **npm**: v6+
- **OS**: Linux, macOS, or Windows

### Dependencies
All dependencies are listed in `package.json` and will be installed automatically.

---

## Installation

### 1. Clone the Repository
```bash
git clone <repository-url>
cd orthomirdb
```

### 2. Install Dependencies
```bash
npm install
```

This will install:
- **express** (v4.21.2): Web framework
- **sqlite3** (v5.1.7): Database driver
- **sqlite** (v5.1.1): Database wrapper
- **apicache** (v1.6.3): Response caching
- **d3-dsv** (v3.0.1): TSV/CSV parsing
- **nodemon** (v3.1.10): Development server auto-reload

### 3. Verify Installation
```bash
npm list
```

---

## Configuration

### Environment Variables (.env)

Create or modify `.env` file in the project root:

```env
# Server Configuration
PORT=3000
NODE_ENV=development

# Database Configuration
DATABASE_PATH=/src/database/mirt_ortho_enrichment_metadata.db

# API Configuration
API_VERSION=v1

# Cache Configuration
CACHE_DURATION=1800000
```

### Database Configuration (src/database/config.json)

```json
{
    "input_file": {
        "filepath": "/path/to/input/data.tsv"
    },
    "species": ["hsa", "mmu", "rno", "ssc"],
    "database_file": {
        "dbpath": "/path/to/mirt_ortho_enrichment_metadata.db"
    }
}
```

**Note**: Ensure paths are absolute paths to avoid issues.

---

## Running the Application

### Development Mode (with auto-reload)
```bash
npm run dev
```

The server will start on `http://localhost:3000` and automatically reload when you modify files.

### Production Mode
```bash
node src/server.js
```

(Set `NODE_ENV=production` in `.env`)

### Access the Application
Open your browser and navigate to:
```
http://localhost:3000
```

---

## API Documentation

### Base URL
```
http://localhost:3000/api/version1
```

### Endpoints

#### 1. **GET /suggestions**
Retrieve miRNA suggestions for autocomplete.

**Request:**
```bash
curl "http://localhost:3000/api/version1/suggestions?query=hsa-let"
```

**Response:**
```json
[
  "hsa-let-7a-5p",
  "hsa-let-7a-3p",
  "hsa-let-7b-5p"
]
```

**Cache**: 2 minutes

---

#### 2. **POST /histogram**
Get histogram data for target overlap analysis.

**Request:**
```bash
curl -X POST http://localhost:3000/api/version1/histogram \
  -H "Content-Type: application/json" \
  -d '{
    "miRNA": "hsa-let-7a-5p",
    "enabledSpecies": ["hsa", "mmu", "rno"]
  }'
```

**Response:**
```json
{
  "histogramData": [...],
  "columns": ["Species", "Count"],
  "extractedValue": 0.85
}
```

---

#### 3. **POST /enrichment**
Retrieve enrichment data (gene targets, disease associations).

**Request:**
```bash
curl -X POST "http://localhost:3000/api/version1/enrichment?miRNA=hsa-let-7a-5p&species=hsa,mmu"
```

**Response:**
```json
[
  {
    "tableName": "Target_genes",
    "rows": [
      {
        "GeneSymbol": "KRAS",
        "GeneID": "3845",
        "transcriptID": "ENST00000256078",
        "ContextScore": 0.92,
        ...
      }
    ]
  },
  {
    "tableName": "Disease_enrichment",
    "rows": [
      {
        "ID": "DOID:5419",
        "Description": "Cancer",
        "pvalue": 0.001,
        "p.adjust": 0.05
      }
    ]
  }
]
```

---

## Frontend Architecture

### Page Structure

#### 1. **index_style2.html** - Home Page
- Search form with 3 steps:
  - Step 1: Enter miRNA name with autocomplete
  - Step 2: Select target species (multi-select)
  - Step 3: Optional Ensembl version selection
- Submit button triggers search

#### 2. **results_style2.html** - Results Page
Displays:
- **Search Parameters**: Shows selected miRNA and species
- **Visualization Section**:
  - Left: Histogram with density toggle
  - Right: GO enrichment chart (Sunburst) or Gene network
- **Data Tables Section**:
  - Tab 1: Target Genes table
  - Tab 2: Associated Diseases table

### JavaScript Modules

#### **api.js**
```javascript
// Fetches data from backend endpoints
- getSuggestions(query)        // Autocomplete
- getHistogramData(miRNA, species)
- getEnrichmentData(miRNA, species)
```

#### **datatable.js**
```javascript
// DataTables initialization and management
- initializeDataTables()       // Initialize empty tables
- loadTables(dataTables, data) // Populate with data
- handleTab(dataTables)        // Tab switching handler
- getTableColumns(tableName)   // Get column definitions
```

#### **charts.js**
```javascript
// D3.js visualizations
- drawHistogramWithHighlight()  // Histogram with highlighting
- generateGOchart()             // Sunburst GO enrichment chart
- setupDensityToggle()          // Toggle density line
```

#### **network.js**
```javascript
// Gene network visualization
- createGeneNetwork()  // D3 force-directed graph
```

#### **utils.js**
```javascript
// General utilities
- localStorage management
- URL parameter parsing
- Display functions
```

---

## Database Structure

### SQLite Database (`mirt_ortho_enrichment_metadata.db`)

#### Tables

**1. Target_genes**
```
Columns:
- miRNA_name (VARCHAR): Normalized miRNA ID
- GeneSymbol (VARCHAR): Gene symbol (e.g., KRAS)
- GeneID (VARCHAR): Ensembl/EntrezGene ID
- transcriptID (VARCHAR): Transcript ID
- ProteinID (VARCHAR): Protein ID
- ContextScore (REAL): miRNA context score
- BindingSiteNumber (INTEGER): Number of binding sites
```

**2. Disease_enrichment**
```
Columns:
- miRNA_name (VARCHAR): Normalized miRNA ID
- ID (VARCHAR): Disease ID
- Description (VARCHAR): Disease name
- pvalue (REAL): P-value
- p.adjust (REAL): Adjusted P-value
```

### Data Flow
```
TSV Input File (config.json)
         ↓
loadTSV() → Parse & Extract miRNAs
         ↓
SQLite Database
         ↓
Query by miRNA name (normalized)
         ↓
Return filtered rows to API
         ↓
Frontend displays results
```

---

## Usage Guide

### Step 1: Search for a miRNA
1. Go to `http://localhost:3000`
2. Click on the miRNA input field
3. Start typing (e.g., "let-7", "mir-21")
4. Select from suggestions

### Step 2: Select Species
1. Check desired species:
   - Human (hsa)
   - Mouse (mmu)
   - Rat (rno)
   - Pig (ssc)
2. At least one species must be selected

### Step 3: Submit
1. Click "Submit" button
2. Wait for data loading (spinner shown)

### Step 4: View Results
1. **Histogram**: Shows target overlap percentage across species
2. **Toggle Density**: Optional density curve overlay
3. **GO Enrichment**: Click to see enriched biological processes
4. **Gene Network**: Visualize target gene interactions
5. **Data Tables**:
   - **Target Genes**: Sortable, searchable gene targets
   - **Associated Diseases**: Disease enrichment results

### Tips
- Data persists in browser storage (localStorage)
- Use DataTable search to filter results
- Hover over chart elements for details
- Click tabs to switch between different data views

---

## Troubleshooting

### Issue 1: Port Already in Use
```
Error: listen EADDRINUSE: address already in use :::3000
```

**Solution**:
```bash
# Change port in .env
PORT=3001
npm run dev

# Or kill process using port 3000
lsof -i :3000  # Find process
kill -9 <PID>  # Kill process
```

### Issue 2: Database File Not Found
```
Error: Error loading enrichment DB path: ENOENT
```

**Solution**:
- Verify database path in `src/database/config.json`
- Ensure path is absolute
- Check file permissions: `ls -la src/database/`

### Issue 3: Tabs Not Switching
**Cause**: DataTables not properly initialized

**Solution**:
1. Check console for errors (F12)
2. Verify jQuery and Bootstrap are loaded
3. Check `initializeDataTables()` is called
4. Ensure DataTables are returned properly

### Issue 4: Autocomplete Not Working
**Cause**: Suggestions endpoint issue

**Solution**:
```bash
# Test endpoint directly
curl "http://localhost:3000/api/version1/suggestions?query=hsa"

# Check server logs for errors
npm run dev  # Will show server logs
```

### Issue 5: No Results Returned
**Possible causes**:
- miRNA not in database (check exact spelling with hyphens)
- Species abbreviations incorrect
- Database query timeout

**Debug steps**:
1. Check browser Network tab (F12)
2. Inspect API response
3. Verify miRNA format matches database

---

## Development

### Adding a New Feature

#### Example: Add a new visualization

1. **Create new utility module**:
   ```javascript
   // public/lib/utils/myViz.js
   export function createMyVisualization(data, container) {
     // Implementation
   }
   ```

2. **Import in results page**:
   ```html
   <!-- In results_style2.html -->
   <script src="lib/utils/myViz.js"></script>
   ```

3. **Call from main script**:
   ```javascript
   // In main2.js
   createMyVisualization(data, '#my-container');
   ```

### Testing

Run tests (if configured):
```bash
npm test
```

### Code Style
- Use ES6 modules (`.js` with `import/export`)
- Use `async/await` for async operations
- Add console logs for debugging

---

## Technology Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| **Backend** | Node.js + Express | 14+ / 4.21.2 |
| **Database** | SQLite3 | 5.1.7 |
| **Frontend** | HTML5 + Bootstrap 5 | 5.3.3 |
| **Charting** | D3.js | 7.x |
| **Tables** | DataTables | 2.3.4 |
| **Data Parsing** | d3-dsv | 3.0.1 |
| **Caching** | apicache | 1.6.3 |
| **Dev Tool** | Nodemon | 3.1.10 |

---

## License


---

## Contact & Support

For issues, questions, or contributions, please:
1. Check the [Troubleshooting](#troubleshooting) section
2. Review server logs (`npm run dev`)
3. Check browser console (F12 → Console tab)
4. Verify configuration files

---

## Roadmap

- [ ] Advanced filtering options
- [ ] Download results as CSV/Excel
- [ ] Custom visualization options
- [ ] Literature integration (PubMed links)
- [ ] Multiple database versions support
- [ ] API rate limiting
- [ ] Unit/integration tests

---

**Last Updated**: November 14, 2025  
**Version**: 1.0.0
