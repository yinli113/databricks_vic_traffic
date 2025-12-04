# Databricks VIC Traffic

![Traffic Flow Dashboard](traffic_dashboard.png)

> **Transform Victorian Department of Transport feeds into trusted, analytics-ready insights with enterprise-grade governance**

Databricks VIC Traffic provides business leaders with near-real-time visibility into congestion, incidents, and network performance while enforcing enterprise-grade governance through Databricks Unity Catalog.

---

## 📋 Table of Contents

- [Executive Overview](#executive-overview)
- [Business Outcomes](#business-outcomes)
- [Key Capabilities](#key-capabilities)
- [Architecture](#architecture)
- [Quick Start](#quick-start)
- [Project Structure](#project-structure)
- [Technical Documentation](#technical-documentation)
- [CI/CD & Automation](#cicd--automation)
- [Contributing](#contributing)

---

## 🎯 Executive Overview

This solution accelerates decision-making by providing curated traffic intelligence that was previously spread across siloed systems. It delivers a governed, scalable data foundation ready for dashboards, predictive analytics, and partner reporting.

### Key Benefits

- ⚡ **Faster insights:** Reduces data preparation time from days to hours
- 🎯 **Improved service delivery:** Real-time congestion and incident metrics
- 🔧 **Operational efficiency:** Standardized pipelines minimize manual work
- 🔒 **Enterprise governance:** Unity Catalog ensures security and compliance

---

## 💼 Business Outcomes

| Outcome | Impact |
|---------|--------|
| **Faster Insights** | Shrinks data preparation time from days to hours by automating data landing, cleansing, and aggregation |
| **Improved Service Delivery** | Provides timely congestion and incident metrics to inform transport planning and public communications |
| **Operational Efficiency** | Standardized pipelines minimize manual data wrangling, freeing teams for higher-value analysis |
| **Scalable Governance** | Centralized access controls and auditability support compliance and partnership reporting |

---

## 🚀 Key Capabilities

### Data Integration
- **Unify disparate feeds:** Consolidates telemetry, incidents, and metadata into a single source of truth
- **Curated analytics layers:** Bronze, Silver, and Gold tables deliver the right level of detail for different audiences

### Enterprise Features
- **Enterprise governance:** Unity Catalog ensures consistent permissions, lineage, and audit trails
- **Automation ready:** CI/CD assets accelerate deployment, secret rotation, and scheduled refreshes across environments

---

## 🏗️ Architecture

### Medallion Architecture

This project implements the [Medallion Architecture](https://www.databricks.com/glossary/medallion-architecture) pattern:

```
┌─────────────────┐
│   Raw Sources   │
│  (VIC DoT Feeds)│
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  BRONZE Layer   │ ◄─── Raw ingestion, schema preservation
│  (Raw Data)     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  SILVER Layer   │ ◄─── Cleaned, validated, enriched
│  (Curated Data) │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   GOLD Layer    │ ◄─── Aggregated, business-ready
│  (Analytics)    │
└─────────────────┘
```

#### Layer Details

- **Bronze Layer** (`4_bronze_loader.ipynb`)
  - Lands raw traffic telemetry and metadata as Delta tables
  - Handles schema drift and ensures reproducibility with checkpoints
  - Preserves source data exactly as received

- **Silver Layer** (`5_silver_loader.ipynb`)
  - Cleanses and standardizes raw data (column typing, deduplication)
  - Applies business logic for traffic sensors, incidents, and congestion metrics
  - Creates dimension and fact tables

- **Gold Layer** (`6_gold_loader.ipynb`)
  - Produces analytics-friendly aggregates for reporting dashboards
  - Supports downstream consumption via Unity Catalog tables or Databricks SQL
  - Optimized for query performance

---

## 🚀 Quick Start

### Prerequisites

- Databricks workspace with Unity Catalog enabled
- Access to Victorian Department of Transport traffic data feeds
- Storage account (ADLS Gen2, S3, or compatible)
- Python 3.8+ (for local development)

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yinli113/databricks_vic_traffic.git
   cd databricks_vic_traffic
   ```

2. **Import notebooks into Databricks**
   ```bash
   # Using Databricks CLI
   databricks workspace import_dir notebooks /Workspace/Repos/databricks_vic_traffic/notebooks
   
   # Or use the Databricks UI:
   # Workspace → Import → Select notebooks directory
   ```

3. **Configure your environment**
   - Open `notebooks/1_config.ipynb`
   - Update configuration:
     - Catalog & schema names
     - Storage account details (ADLS Gen2, S3)
     - Secrets scope for credentials

4. **Run ETL pipeline in order**
   ```python
   # Execute notebooks sequentially:
   1. 1_config.ipynb      # Configuration
   2. 2_setup.ipynb       # Schema setup
   3. 4_bronze_loader.ipynb  # Raw data ingestion
   4. 5_silver_loader.ipynb  # Data transformation
   5. 6_gold_loader.ipynb     # Aggregation layer
   
   # Optional cleanup:
   - 3_clearup_old_tables.ipynb  # Remove legacy tables
   ```

---

## 📁 Project Structure

```
databricks_vic_traffic/
├── notebooks/
│   ├── 1_config.ipynb              # Environment & catalog configuration
│   ├── 2_setup.ipynb               # Source setup & schema registration
│   ├── 3_clearup_old_tables.ipynb  # Cleanup utilities for stale objects
│   ├── 4_bronze_loader.ipynb      # Raw ingestion into Bronze tables
│   ├── 5_silver_loader.ipynb       # Transformations into curated Silver tables
│   ├── 6_gold_loader.ipynb        # Aggregations & Gold-level marts
│   └── playground.ipynb           # Scratchpad for exploratory analysis
├── CICD/
│   ├── cicd-main.yml.ipynb         # GitHub Actions template for pipeline runs
│   ├── deploy.yml.ipynb            # Deployment workflow (jobs/workspaces)
│   └── shellScript/
│       └── DBToken.ps1.ipynb       # Helper script for managing PAT/DB tokens
├── traffic_dashboard.png           # Dashboard visualization
└── README.md                       # This file
```

---

## 📚 Technical Documentation

### Data Sources & Governance

- **Source Data:** Victorian Department of Transport traffic datasets
- **Storage:** Configured for ADLS Gen2, S3, or compatible object storage
- **Governance:** Unity Catalog for access control, auditing, and lineage
- **Security:** Service principal authentication with least-privilege ACLs

### Observability & Maintenance

- **Monitoring:** Enable auto-logging via Databricks Jobs or MLflow
- **Auditing:** Leverage Delta table history (`DESCRIBE HISTORY`) to track changes
- **Scheduling:** 
  - Bronze/Silver notebooks: Continuous ingestion (scheduled jobs)
  - Gold aggregations: On-demand or via workflow dependencies

### Performance Optimization

- Delta Lake optimizations (Z-ORDER, VACUUM)
- Partitioning strategies for time-series data
- Spark session tuning for large-scale processing

---

## 🔄 CI/CD & Automation

### Available Workflows

| File | Purpose |
|------|---------|
| `CICD/cicd-main.yml.ipynb` | GitHub Actions template for linting, testing, and Databricks job submission |
| `CICD/deploy.yml.ipynb` | Workflow for packaging notebooks and deploying to target workspaces |
| `CICD/shellScript/DBToken.ps1.ipynb` | PowerShell helper for managing Databricks PATs and GitHub secrets |

### Setup Instructions

1. Export `.ipynb` files as YAML or scripts
2. Configure GitHub Actions secrets:
   - `DATABRICKS_HOST`
   - `DATABRICKS_TOKEN`
   - Storage account credentials
3. Customize workflows for your environment

---

## 👥 Stakeholders & Consumers

| Role | Use Case |
|------|----------|
| **Executive Leadership** | Visibility into network performance, congestion hotspots, and investment impact |
| **Transport Operations** | Near-real-time insights for incident response and traffic management |
| **Analytics & Planning Teams** | Reliable dataset foundation for forecasting and scenario modeling |
| **External Reporting** | Shareable gold-layer tables that power dashboards and partner scorecards |

---

## 📊 Status & Roadmap

### Current State
✅ Core ingestion and transformation notebooks complete  
✅ CI/CD assets available for automation  
✅ Unity Catalog integration implemented

### In Progress
🔄 Operationalizing scheduled runs  
🔄 Finalizing Unity Catalog permissions for production rollout

### Next Opportunities
- Embed predictive models for demand forecasting
- Expand data sources (weather, events)
- Publish executive dashboards
- Add unit tests with `pytest`/`dbx`
- Integrate Delta Live Tables for managed orchestration

---

## ⚠️ Risk & Mitigation

| Risk | Mitigation |
|------|------------|
| **Data completeness** | Monitor source feed availability; bronze layer keeps history for replay |
| **Access control** | Unity Catalog enforces least-privilege; review quarterly |
| **Operational resilience** | Jobs scheduled with retries and alerting via Databricks Workflows |

---

## 🤝 Contributing

### Development Workflow

1. Create a feature branch from `main`
2. Make your changes
3. Test thoroughly in development environment
4. Submit a pull request with clear description

### PR Template

```markdown
## Summary
- [Brief description of changes]
- [Key value delivered]

## Testing
- [Testing approach and results]

## Deployment Notes
- [Any special considerations]
```

---

## 📝 License

This project is licensed under the MIT License. See `LICENSE` for details.

---

## 📞 Support & Contact

For questions or issues:
- Open a GitHub issue
- Contact the data platform team
- Review the technical documentation above

---

**Built with ❤️ using Databricks, Delta Lake, and Unity Catalog**
