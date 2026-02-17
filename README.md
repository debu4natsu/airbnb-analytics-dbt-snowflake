
# 🏠 Airbnb Data Platform – End-to-End ELT with Snowflake & dbt

## 📌 Overview

This project implements a production-style, end-to-end data platform for Airbnb analytics using **Snowflake**, **dbt**, and **AWS S3**. The pipeline follows a **Medallion architecture** and demonstrates:

- Incremental data processing
- Slowly Changing Dimensions (SCD Type 2)
- Metadata-driven modeling
- Modular transformation design
- Layered schema isolation
- Automated lineage and testing

The system transforms raw CSV data into analytics-ready datasets optimized for BI and downstream consumption.

---

## 🏗 Architecture

```
CSV → AWS S3 → Snowflake (Staging)
                      ↓
               Bronze Layer (Raw)
                      ↓
              Silver Layer (Cleansed)
                      ↓
               Gold Layer (Analytics)
```

**Core Principles**
- ELT-based transformation
- Incremental-first processing strategy
- Layer isolation via schema management
- Reusable macro-driven transformations
- Snapshot-based historical tracking

---

## 🛠 Technology Stack

| Component | Technology |
|---|---|
| Cloud Data Warehouse | Snowflake |
| Transformation Engine | dbt (Core) |
| Cloud Storage | AWS S3 |
| Templating Engine | Jinja |
| Language | SQL + Python |
| Version Control | Git |

---

## 📊 Data Modeling Approach

### 🥉 Bronze Layer
- Raw ingestion from staging
- Incremental materialization
- Minimal transformation
- **Schema:** `AIRBNB.BRONZE`

### 🥈 Silver Layer
- Business logic & cleansing
- Derived metrics
- Reusable macro logic
- **Schema:** `AIRBNB.SILVER`

### 🥇 Gold Layer
- Analytics-ready models
- Fact + dimensional structures
- Metadata-driven join generation
- **Schema:** `AIRBNB.GOLD`

---

## 🔁 Incremental Processing Strategy

Models use timestamp-based filtering to avoid full refresh:

```sql
{{ config(materialized='incremental') }}

{% if is_incremental() %}
  WHERE CREATED_AT > (SELECT COALESCE(MAX(CREATED_AT), '1900-01-01') FROM {{ this }})
{% endif %}
```

This design:
- Minimizes compute cost
- Reduces full-table scans
- Improves scalability for growing datasets

---

## 🧠 Snapshot Strategy (SCD Type 2)

Implemented timestamp-based snapshots to:
- Preserve historical record versions
- Enable point-in-time analytics
- Maintain `valid_from` / `valid_to` tracking
- Avoid destructive updates

---

## ⚙️ Metadata-Driven Modeling

Gold OBT model uses configuration-driven joins:
- Dynamic SQL generation via Jinja loops
- Reduced hardcoding
- Centralized join definitions
- Improved maintainability

---

## 🔄 Custom Macros

Reusable logic implemented via dbt macros:

| Macro | Purpose |
|---|---|
| `multiply()` | Controlled precision calculations |
| `tag()` | Dynamic price categorization |
| `generate_schema_name()` | Custom schema resolution |
| `trimmer()` | Standardized string formatting |

---

## 🧪 Data Quality & Governance

- Source validation tests
- Unique and not-null constraints
- Snapshot consistency checks
- Automatic lineage tracking via `dbt docs`

---

## 🚀 Running the Project

```bash
dbt debug      # Validate configuration
dbt run        # Execute models
dbt test       # Run tests
dbt snapshot   # Execute SCD logic
dbt docs serve # View lineage graph
```

---

## 🔐 Engineering Best Practices

- Schema isolation by data layer
- Incremental-first design philosophy
- Environment-driven materialization
- Credentials excluded from version control
- Role-based access control in Snowflake

---

## 📈 Future Enhancements

- [ ] CI/CD integration
- [ ] Automated data quality monitoring
- [ ] BI integration (Tableau / Power BI)
- [ ] Data observability & alerting
- [ ] Performance benchmarking
