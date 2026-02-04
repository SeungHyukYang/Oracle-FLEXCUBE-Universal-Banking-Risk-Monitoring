# Oracle-FLEXCUBE-Universal-Banking-Risk-Monitoring

# Customer Trust & External Risk Monitoring Project

## Overview

This project demonstrates a **customer-facing risk and trust reporting framework** that monitors publicly disclosed external risks and translates them into an interpretable **Trust Score** over time.

Rather than detecting security incidents or breaches, this project focuses on **risk awareness, exposure monitoring, and trust communication**, which are critical components of modern service reporting, cybersecurity governance, and executive decision-making.

---

## Project Objective

The primary objective of this project is to answer the following question:

> How does exposure to publicly disclosed external risks evolve over time, and what does that mean for customer trust?

This project is designed to reflect **real-world reporting needs** where:
- Actual incidents may not have occurred
- External risk signals still require monitoring
- Trust must be maintained through transparency and governance

---

## Key Concepts

### External Risk Signals (CVE Data)

The project uses publicly available **CVE (Common Vulnerabilities and Exposures)** data from the National Vulnerability Database (NVD) as **external risk signals**.

Important clarifications:
- CVEs represent **potential risk**, not confirmed breaches
- The presence of a CVE does **not** imply exploitation
- CVEs are used to model **risk exposure**, not incidents

---

### Risk State Model

Each external risk signal is assigned a **Risk State** for reporting purposes.

| Risk State | Definition |
|-----------|------------|
| **Open** | Vulnerability has been disclosed and remains exposed |
| **Mitigated** | Vendor advisory or remediation guidance exists |
| **Closed** | Risk is no longer considered active for reporting |

> Risk states are reporting definitions and do not indicate confirmed exploitation or remediation status in any specific organization.

---

### Risk Index

To enable trend analysis and aggregation, qualitative risk states are transformed into a **quantitative Risk Index**.

#### Risk Index Components

1. **Severity** – CVSS base score (0–10)  
2. **Risk State Weight** – exposure level based on risk state  
3. **Time Exposure** – duration since disclosure  

#### Risk State Weights

| State | Weight |
|------|--------|
| Open | 1.0 |
| Mitigated | 0.5 |
| Closed | 0.0 |

#### Risk Index Formula

Risk Index = CVSS Score × State Weight × Time Exposure Factor


This approach ensures that:
- Older unresolved risks carry higher weight
- Managed risks gradually lose impact
- Closed risks no longer influence reporting

---

## Trust Score Framework

### Trust Score Definition

> **Trust Score represents customer confidence based on the organization’s exposure to external risks over time and its ability to manage them.**

Trust Scores are:
- **Not incident-based**
- **Not breach indicators**
- **Not security guarantees**

They are **communication and governance metrics**.

---

### Trust Score Logic

All customers or services begin with a baseline Trust Score of **100**.

Trust decreases when:
- External risks remain open
- High-severity vulnerabilities persist
- Exposure duration increases

#### Trust Penalty Calculation

Trust Penalty = Σ (Risk Index × Impact Weight)
Trust Score = 100 − Trust Penalty


Impact weights may vary by risk type:
- Confidentiality impact
- Integrity impact
- Availability impact

This ensures the Trust Score remains:
- Interpretable
- Actionable
- Explainable

---
## Data Source & Ingestion

This project retrieves vulnerability data directly from the **National Vulnerability Database (NVD)** using the official NVD REST API (CVE API v2.0).

- **Source**: National Institute of Standards and Technology (NIST) – NVD
- **Data Type**: Publicly disclosed CVE (Common Vulnerabilities and Exposures)
- **Access Method**: Direct API query (no local database persistence)

### API Endpoint
https://services.nvd.nist.gov/rest/json/cves/2.0


### Query Strategy
- Data is queried dynamically using a **rolling 30-day window**
- Only **HIGH severity (CVSS v3.x)** vulnerabilities are retrieved
- API parameters are generated at refresh time to ensure freshness

Example parameters:
- `pubStartDate` / `pubEndDate` (last 30 days, UTC)
- `cvssV3Severity = HIGH`
- `resultsPerPage = 200`

This approach ensures that dashboards always reflect the most recent external risk disclosures without maintaining a local data store.

---

## Risk Index Methodology

Each CVE is transformed into a quantitative **Risk Index** to enable aggregation and trend analysis.

### Risk Index Components

1. **Severity**
   - CVSS Base Score (v3.1 preferred, fallback to v3.0)

2. **Risk State**
   - `Open`: No vendor advisory detected
   - `Mitigated`: Vendor advisory exists  
   - State Weights:
     - Open = 1.0
     - Mitigated = 0.5

3. **Time Exposure**
   - Days since public disclosure
   - Time Weights:
     - ≤ 30 days → 1.0
     - 31–90 days → 1.2
     - > 90 days → 1.5

### Risk Index Formula

Risk Index = CVSS Base Score × State Weight × Time Weight


This ensures that:
- Older unresolved risks carry higher impact
- Mitigated risks reduce overall exposure
- Closed risks can be excluded from calculations

---

## Daily Trust Score Framework

### Trust Score Definition

> **Trust Score represents customer confidence based on exposure to publicly disclosed external risks and the organization’s ability to manage them over time.**

Trust Scores are **not indicators of confirmed breaches or incidents**, but a communication and governance metric.

---

### Daily Aggregation

Risk exposure is aggregated **by disclosure date**:

Daily Risk Index = Σ (Risk Index per CVE on that date)


---

### Trust Penalty & Scaling

To prevent Trust Scores from collapsing to zero during high-volume disclosure days, an **automatic scaling mechanism** is applied.

- The maximum daily risk within the window is used to calibrate sensitivity
- The worst day is capped at a predefined penalty threshold (e.g., 70)

Scale Factor = Target Penalty / Max(Daily Risk Index)
Trust Penalty = Daily Risk Index × Scale Factor
Trust Score = 100 − Trust Penalty


This allows Trust Scores to:
- Remain interpretable
- Reflect relative risk intensity
- Adapt automatically as data volume changes

---

## Output Metrics

The daily trust dataset contains the following fields:

| Column | Description | Interpretation |
|------|------------|---------------|
| `risk_date` | CVE disclosure date | Time axis |
| `daily_risk_index` | Aggregated risk exposure | Higher = more risk |
| `trust_penalty` | Scaled negative impact | Higher = worse |
| `trust_score` | Customer trust indicator (0–100) | Higher = better |
| `scale_factor` | Auto-calibration coefficient | Governance / audit |

---

## Governance & Limitations

- This project uses **publicly disclosed data only**
- No assumptions are made about exploitability or internal system exposure
- Trust Scores are intended for **trend analysis and communication**, not operational security decisions

> Trust Scores reflect external risk exposure and do not indicate confirmed security incidents, breaches, or exploitation events.

---


## What This Project Is (and Is Not)

### What This Project Is

- A **risk visibility and trust reporting framework**
- A **governance-oriented analytics project**
- A **customer-facing reporting model**

### What This Project Is Not

- A real-time security monitoring system
- A breach detection or forensic tool
- A replacement for SOC or IDS systems

---

## Why This Matters

Modern organizations are expected to:
- Acknowledge external risks before incidents occur
- Communicate risk transparently to stakeholders
- Demonstrate governance and awareness

This project reflects how organizations can **maintain trust without waiting for failures**.

---

## Disclaimer

> Trust Scores reflect exposure to publicly disclosed external risks and do not indicate confirmed security incidents, breaches, or exploitation events.

---

## Next Steps

- Power BI dashboard implementation  
- Risk trend visualization  
- Customer-level trust reporting views  
- Governance and data validation documentation  

---

## Summary

This project bridges the gap between **technical risk data** and **human-readable trust reporting**, de
