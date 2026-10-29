---
title: "FCU (Fraud) Data Lake – DMINE Migration to AWS — Solution Architecture V1.0"
subtitle: "Copy-ready slide content · Build the Fraud Data Lake on AWS and migrate DMINE; SEC1/2 retained on-premises as the interim consumption layer"
---

**How to use this document.** Each heading is one slide of the SA deck, in deck order, using the standard SA section titles. Bracketed values like [date] are to be filled in. Drawings referenced by file name are supplied separately as PNG.

\newpage

# Slide 1 — FCU (Fraud) Data Lake – DMINE Migration to AWS (Involved Parties / Intakes)

**Involved Parties**

- Author: Rakesh Sharma
- CIOO Area: Financial Crime Unit — Dibyendu Bhattacharya (STO), Tiberiu Chivaran (LTO), Jeff Machintosh (CIO)
- Enterprise Architect: Nicholas Aleksandroff
- Cyber Security: Noreen & Maurer, Chad
- Engineering Consultant / Cloud onboarding: Harun Rashid
- Other Contributors: Sam Sidhu

**Intakes**

| Intake | ID |
|---|---|
| Project Number | PR045132 |
| Enterprise Architecture Triage | PT-7306 |
| Governance Required | NA |
| Cyber Security Intake Record | RITM4724426 |
| ADRC | ADRCINTK-[NNNNN] |
| TICR Number | TICR-47344 |
| SA Version | V1.0 |

**Enterprise Architect Justification**

FCU is establishing a Fraud Data Lake on AWS and migrating the DMINE (Sybase IQ) data warehouse to it: ingestion of the fraud data sources (EDF, IDP, Direct Feeds, DMINE history), Raw/Standardized/Silver/Gold layers with S3 Tables as the transactional table layer over S3, Redshift Spectrum/Serverless for DW and SageMaker Unified Studio for analytics/ML workspaces. To avoid two concurrent migrations, SEC1/2 (Microsoft SQL Server – SSIS/SSAS/SSRS) are retained on-premises as an interim consumption layer and re-pointed from Sybase IQ to the AWS Gold layer; their decommission follows later, once their use cases are proven on the lake. The solution is advanced to TDRC for visibility, pattern conformance, and approval of the analytics/BI modernization scope.

\newpage

# Slide 2 — Objective

**Problem Statement**

- **Infrastructure Limitations, Scalability Constraints:** Some of the Fraud data infrastructure (data warehouse) operates on legacy on-premises systems with significant storage capacity limitations, high operation cost and maintenance overhead.
- **Limited Analytics Capabilities:** Legacy infrastructure and non-existing centralized data repository constrains advanced analytics capabilities, machine learning implementations, and AI-driven insights. This technological gap limits strategic business intelligence and cross-functional data collaboration opportunities.

**Proposed Solution — Establish the Fraud Data Lake on AWS and migrate DMINE**

| Element | Description | Status |
|---|---|---|
| Fraud Data Lake on AWS | Ingest EDF, IDP, Direct Feeds and DMINE history into S3; Raw → Standardized → Silver/Gold on S3 Tables; Glue, Catalog, MWAA; Athena, Redshift Spectrum/Serverless, SageMaker Unified Studio, Power BI; Lake Formation governance. | In Scope |
| DMINE migration | Migrate the DMINE (Sybase IQ) warehouse — ~700 tables, 7 years of history — to the lake; validate and retire Sybase IQ after cut-over. | In Scope |
| SEC1/2 interim | SEC1/2 (SQL Server – SSIS/SSAS/SSRS) stay on-premises; SSIS re-points its DMINE source from Sybase IQ to the AWS Gold layer. Reports, cubes and consumers unchanged. | In Scope |
| SEC1/2 decommission | Migrate SEC1/2 use cases to the lake, then decommission SEC1/2 — later, once use cases are proven on the lake. | Out of Scope |

- Build the lake: ingestion (EDF/Lambda, SFTP/FileX, DataSync), S3 Raw → Standardized, then **Silver/Gold** curated layers.
- Adopt **S3 Tables** (CE recommendation) for ACID semantics, schema evolution, and multi-engine reads on S3. **Redshift Spectrum/Serverless** only for heavy workloads.
- Enable **SageMaker Unified Studio** for collaborative notebooks, feature engineering, model prototyping, and governed promotion paths (availability **by May**).

**Business Value**

- Trusted, governed, analytics-ready data for **advanced fraud analytics** and cross-domain insights.
- Retires the legacy Sybase IQ warehouse; sets up the later SEC1/2 decommission once parity is proven.
- Existing SEC1/2-based reporting, OLAP cubes and business tooling continue uninterrupted while DMINE is retired — one cutover at a time.

**Risks**

- Platform Learning Curve: Development teams require comprehensive training on Data Lake architecture and AWS cloud services; delay of SageMaker Unified Studio within timelines potentially extending initial implementation timelines.
- Working with downstream teams for consumption compatibility with existing business tools; define alternatives where not compatible.
- Parallel-migration risk mitigated by sequencing — SEC1/2 stays as is; its cutover follows later.
- SSIS re-pointing from Sybase IQ to Redshift/Athena must be reconciled (row counts, schema, checksums) during a parallel run before DMINE cut-over.

**Assumptions**

- Current operations and development of the DMINE platform will not be impacted while the cloud enablement efforts are under way.

\newpage

# Slide 3 — Scope

**Project Overview**

- Establish the **Fraud Data Lake on AWS** — ingestion (EDF/Lambda, SFTP/FileX, DataSync), S3 Raw → Standardized → **Silver/Gold** on S3 Tables, Glue/Catalog/MWAA, Athena, Redshift Spectrum/Serverless, SageMaker Unified Studio, Power BI, Lake Formation governance.
- **Migrate DMINE (Sybase IQ)** — ~700 tables, 7 years of history — to the lake and retire Sybase IQ after cut-over.
- **SEC1/2 retained on-premises as an interim state**, consuming DMINE data from the AWS Gold layer; reports, cubes and consumers unchanged.

**Scope diagram (right-hand panel):** file `Scope_Panel_SA.png` — three columns: "On-premises today" (DMINE Sybase IQ; feeds EDF/IDP/Direct Feeds; CAD/MBA + IDP → SEC1/2 unchanged) → "AWS Fraud Data Lake (new)" (Ingestion; Raw → Standardized; Silver & Gold S3 Tables; Analytics; Governance) → "Interim consumption" (SEC1/2 on-prem fed from Gold via Redshift/Athena ODBC or S3 Outbound → SFTP). Hybrid connectivity band; future note: "migrate SEC1/2 use cases to the lake, then decommission DMINE and SEC1/2 — one cutover at a time".

**In Scope**

- **Data Lake Build:** ingestion (EDF/Lambda, SFTP/FileX, DataSync), S3 Raw → Standardized, **Silver** (conformed) and **Gold** (consumption-optimized) on **S3 Tables**; Glue, Catalog, MWAA.
- **DMINE Migration:** ~700 tables and 7 years of history to the lake; validation (counts, schema, checksums); Sybase IQ retired after cut-over.
- **Analytics Enablement:** SageMaker Unified Studio workspaces and Redshift Spectrum/Serverless (compute) for DW, governed access to prod data for analysis with CI/CD promotion.
- **Consumption Enablement:** Power BI/Athena connectors; compatibility evaluation with existing business tooling; define alternate access where needed. AWS → SEC1/2 consumption pattern: SSIS pulls DMINE data from the Gold layer via Redshift/Athena ODBC (primary); S3 Outbound → Transfer Family (SFTP) → NAS → SSIS bulk load for bulk/history.
- **Governance:** Data lineage (Alation), DQ checks, RBAC (Lake Formation/IAM), encryption, auditability for analytics.

**Out Of Scope**

- **SEC1/2 workload migration and decommission** — later, once use cases are proven on the lake.
- **CAD/MBA ingestion into the AWS lake** — CAD/MBA continues to feed SEC1/2 on-premises.
- New data sources beyond EDF, IDP, Direct Feeds and DMINE unless required by priority use cases.

**Constraints & Dependencies**

- CE/EA **pattern finalization** (S3 Tables pattern cataloging; SageMaker Unified Studio availability **by May**).
- Network path AWS → SEC hosts (Direct Connect / PrivateLink; SG/NACL allow-list) and approval of the Redshift/Athena ODBC driver on the SEC servers.
- Stakeholder and Governance: UAT by support & vendors validating the current warehouse (DMINE) with the new data lake (unit testing, count, schema checks, etc.) and training on new platform capabilities.
- Vendor/Support Agreements: Establishment of escalation procedures and support for AWS Cloud Engineering and adoption teams.

\newpage

# Slide 4 — Business View – Business Capabilities

**Business Capabilities**

| L1 | L2 | L3 | Additional Detail |
|---|---|---|---|
| Fraud Management | Fraud To Information Association | Fraud Analytics Management | Ability to identify, establish, validate, and articulate metrics and measures on historic and predictive determinations of fraud. |
| Information Management | Information Collection | Information Sourcing | Ability to discover information. |
| Information Management | Information Inventory | N/A | Ability to compile and manage identity inventory, data inventory, equipment inventory. |

**Business Functions**

| L1 | L2 | L3 | Additional Detail |
|---|---|---|---|
| Analysis & Recommendations | N/A | N/A | Reviewing fraud trends and providing insights and recommendations to improve fraud management strategies. |

\newpage

# Slide 5 — Context Diagram

**Inbound to FCS 93230 (Enhance L4)**

- Data Sources ERE, Symcor, PEGA, TSYS, RSA, FIS, FICO … → Treo FileX 87356 (L2) → File Data Source → FCS 93230.
- Data Sources XIM 1053, XST 1062, MECH 1547, EWS 91359, SNOW, IRIS, BOW 17568, DIS 7038, TRIPS 8812 → IDP OnPrem 13491 → File Data Source → TREO FileX 87356 (L2) → FCS 93230.
- EDF 24996 (L2) → Direct Kafka Feed → FCS 93230.
- DMINE (Sybase IQ) history → AWS DataSync → FCS 93230.

**Downstream of FCS 93230**

- Fraud Strategy & Analytics; BI; Audit Analytics (existing).
- **SEC Servers (SEC1/2 – SQL Server)** — New (blue) arrow from FCS 93230, labelled "DMINE data – Redshift/Athena ODBC or S3 outbound extract".

**Data Sources & Downstream / Operational Risk table**

| Data Sources & Downstream | Operational Risk |
|---|---|
| CAD | NA |
| PEGA | NA |
| TSYS | NA |
| EDF | NA |
| MECH | NA |
| FIS | NA |
| Fraud Strategy & Analytics | NA |
| BI | NA |
| Audit Analytics | NA |
| SEC1/2 | NA |

**Legend** — Existing (black), New (blue), Configure (green). CAD/MBA stays on-premises feeding SEC1/2 and is not an inbound to FCS 93230.

\newpage

# Slide 6 — Change Summary

| # | AppCat ID | Name | Summary of Change |
|---|---|---|---|
| 1 | 93230 | Fraud Data Lake | **New:** Fraud Data Lake on AWS – ingestion (MSK/Lambda, SFTP, DataSync), S3 Raw/Standardized; S3 Tables (over S3) – ACID, schema evolution, time-travel, multi-engine support. SageMaker Unified Studio – collaborative ML/analytics workspace; integration with Git/registries. Redshift Serverless/Spectrum for DW operations and as the ODBC endpoint for SEC SSIS. Glue outbound-extract job and S3 Outbound bucket; Transfer Family (SFTP) outbound. **Enhance:** Glue Data Catalog / Lake Formation – expanded metadata, row/column security, fine-grained permissions. DQ & Lineage Controls – automated checks at Raw→Standard→Silver→Gold; traceability. Orchestration – MWAA for curated pipelines; CI/CD for workspace jobs. **Enterprise services:** S3, Glue, Athena, Lambda, Step Functions, IAM, KMS, Secrets Manager, CloudWatch/CloudTrail, Splunk. |
| 2 | 87356 | FileX | **Reuse** – FileX delivers IDP and Direct Feed files to the AWS lake via SFTP. No CAD copy to AWS. |
| 3 | [SEC AppCat ID] | SEC1/2 | **Reuse with re-point (interim)** – SQL Server with SSIS/SSAS/SSRS retained on-premises as the interim consumption layer; decommissioned later once use cases are migrated to the lake. SSIS packages loading DMINE data are re-pointed from Sybase IQ to AWS Gold: ① Redshift/Athena ODBC pull (primary); ② S3 Outbound → SFTP → NAS → SSIS bulk load (bulk/history). SSAS cubes, SSRS reports and downstream consumers unchanged. |

\newpage

# Slide 7 — Current State Data Warehouse

*(use the existing current-state drawing)*

**DMINE Data Source:** EDF Cloud — near real-time, 200+ feeds, ~300 GB/day (Avro/csv, 15/30 min). IDP on-Prem — batch, 150+ feeds, ~300 GB/day (CRDS / Sterling). Direct Feed — batch, 150+ feeds, ~500 GB/day (ERE 10, Pega/NetR 50, TSYS 80). **BMA Data Source:** ~250 data feeds → SSIS / linked server → SEC1/2.

**DMINE Platform (migrates to AWS):** ETL — EDF connect/unload (Java), CSV/flat loads (Sybase utilities), EBCDIC parser (TSYS). Database — 500+ active tables, 30K+ elements, 200+ archive tables; 60M/day EDF-NRT, 180M/day batch, ~200B record history; 7-yr retention; PCI/PII clear-text, L1 model. 120+ users set up, 30+ active, 80+ SQL connections; top-20 tables = 70% space; 90% of usage < 2 years of data.

**BMA Platform — SEC1/2 (retained, interim):** ~2000 tables, 10 sources, ~500 processing jobs; ~250 extract jobs from DMINE. Fraud Reporting, Banking Analytics, Card Analytics teams.

**Data Usage:** Analytics & Reporting — SAS/SAS-Viya, Power BI (30%); Jupyter / Dataiku / DB Visualizer (70%). EFM Data Management — Alation catalogue; Informatica DQ (daily/weekly). Application Interface — MNS case management (real-time lookup); custom web & Windows apps.

**Caption:** the ~250 DMINE extract jobs on SEC1/2 are the SSIS re-point work; the SEC estate itself moves later.

\newpage

# Slide 8 — Current Business Consumption Process

- CAD/MBA → SEC Servers; DMINE (Sybase IQ) → SEC Servers.
- SEC Servers: SEC1/2 — Microsoft SQL Server (SSAS, SSIS, SSRS).
- How it's used: SSIS – load, transform and standardize DMINE data into tables/views; SSAS – OLAP cubes; SSRS – reporting before Power BI; SEC server also includes data from IDP and CAD/MBA.
- Data Management (Alation, Informatica, ServiceNow) · Adhoc (DbVisualizer) · Analytics (Jupyter, Python, Dataiku, SAS) · Reporting (Power BI).

**Caption:** After migration the picture is identical except that the DMINE arrow originates from the AWS Gold layer (Redshift/Athena ODBC or S3 outbound extract) instead of Sybase IQ.

\newpage

# Slide 9 — Business Use Case

Set of example business use cases that will be enabled by the FCU data platform: Synthetic Identity & High-Risk Profile Detection; Scam Detection & Intervention Framework; Cross-Channel Behavioral Analytics; Fraud Alert & Complaint Linkage; Unified Fraud Detection & Analyst Enablement; Insider Risk & Privileged Access Monitoring; Intelligent Fraud Analyst Enablement — all EFM / SageMaker, Redshift Spectrum. Current SEC1/2 reporting use cases migrate later.

\newpage

# Slide 10 — Application View

- Lane A (Data Sources) — BMO Cloud / Digital Core: EDF (DMINE Topics). BMO Corporate Network (On-Prem): IDP (Netezza On-prem), Direct Feed (Batch Interface), Fraud Datastore (DMINE On-prem) → File Transfer (FileX) / DataSync.
- Lanes B, C (Ingestion & Landing Zone) — Consumer Service (EDF), Batch Processing (DataSync, SFTP); Raw EDF / IDP / Direct Feed / DMINE exports.
- Lane D (Data Transformation) — ETL Services (Glue Crawler, Glue Data Catalogue, Glue Jobs); Automation (MWAA, EventBridge, Lambda); Glue Job "Outbound extract (Gold → S3 Outbound)".
- Lane E (Curated Data Layer) — Standardized Zone → Silver Layer → Gold Layer on S3 Tables; "S3 Outbound (SEC extracts)" beside Gold.
- Lane F (Data Analytics) — Query/Search (Athena), Data Warehouse (Redshift Spectrum), ML Service (SageMaker Unified Studio).
- Lane G (Data Consumption) — BMO AWS Cloud: EFM Data Management (Data Catalogue, Data Quality). BMO Corporate Network: Analytics & Reporting Service (BI Visualization [Power BI], DB Visualizer [Jupyter/Dataiku]), MNS, Web & Windows Apps, and **SEC Servers (SEC1/2 – SQL Server SSIS/SSAS/SSRS)** with a flow from Data Warehouse [Redshift Spectrum] (ODBC) and a dashed flow from "S3 Outbound → Transfer Family".

**Right-hand notes**

- **A: Data Sources** — EDF (NRT), FileX (Batch), IDP, DMINE history. CAD/MBA remains on-premises feeding SEC1/2 directly.
- **B, C: Data Processing & Curated Zones** — EventBridge, Lambda, MWAA orchestrate curated pipelines.
- **D:** Glue ETL transforms Standard → Silver (Conformed models); Glue ETL builds Gold (Analytics-ready) datasets; one Glue job extracts Gold to S3 Outbound for SEC1/2.
- **E:** Curated layers stored using S3 Tables for ACID + schema evolution.
- **F: Analytics & ML Layer** — SageMaker Unified Studio for notebooks, feature engineering, ML prototyping; Athena for SQL analytics and BI connectivity; feature sets prepared in Gold Zone. Redshift Spectrum external tables over Gold are the ODBC endpoint for SEC SSIS.
- **Governance & Security** — Lake Formation fine-grained RBAC; IAM / Identity Center; KMS encryption; CloudTrail + CloudWatch → Splunk; SNS alerts. AWS → SEC egress over Direct Connect / PrivateLink with SG/NACL allow-list to SEC hosts.

\newpage

# Slide 11 — Fraud Data Lake (AWS) High Level

**Drawing:** file `HighLevel_AWS_SA.png`. FCU Data Sources → Data Ingestion Layer (MSK, SFTP) → Data Lake / ETL (S3 Raw, Lambda events, Bronze, Glue Crawlers / Glue / Data Catalog, Silver and Gold Iceberg, S3 Outbound) → Compute (Athena, Redshift Gold external tables, Transfer Family outbound) → Consumption: Power BI, SageMaker Unified Studio, and the on-prem SEC1/2 (interim) box fed from Redshift (ODBC, solid) and from S3 Outbound → Transfer Family (file extract, dashed) over Direct Connect / PrivateLink · TLS 1.2.

\newpage

# Slide 12 — Technology View : Fraud Data Lake Architecture AWS

**Drawing:** file `EA_TV_DMINE_SEC_OnPrem.png`.

- Lane A (FCU Data Sources): EDF, IDP, Direct Feed, DMINE Sybase IQ; block "Stays on-prem → SEC Servers: CAD/MBA, IDP (SEC extract)".
- Lanes B–G: ingestion, landing, processing (incl. Glue Job SEC extract), curated layers (incl. S3 Outbound), analytics (incl. AWS Transfer Family outbound), data usage.
- Lane H — SEC1/2 (Microsoft SQL Server · SSIS/SSAS/SSRS), interim consumption layer, decommissioned later: ① SSIS ← AWS Gold (Redshift/Athena ODBC); ② SSIS bulk load ← NAS ← SFTP (S3 Outbound extract); Business Consumption unchanged; Security: TLS 1.2, IAM role/STS, KMS, Azure AD SSO, SG/NACL allow-list over Direct Connect.
- Bands: DMINE MIGRATION TO AWS (A–E) · ANALYTICS PLATFORM (F) · INTERIM — SEC1/2 RETAINED · DECOMMISSIONED IN A FUTURE INITIATIVE (H).

\newpage

# Slide 13 — Fraud Data Lake (AWS) Low Level

**Drawing:** file `EA_LL_DMINE_SEC_OnPrem.png`.

- Markers A (IDP), A′ (Direct Feed), A‴ (EDF) → TREO FileX / PGP / Kafka → AWS SFTP, Lambda, S3 landing; DMINE Sybase IQ via DataSync.
- Glue Crawler, Catalog, MWAA and Glue Jobs building Standardized, Silver, Gold; marker L″ — Glue Job (Outbound extract for SEC SSIS) → S3 Outbound (SEC1/2 extracts).
- Consumption → SEC1/2 block: P – Redshift/Athena (Gold external tables); P′ – Transfer Family (outbound SFTP); Direct Connect / PrivateLink egress; security note (TLS 1.2, IAM role/STS, KMS, SG/NACL allow-list).
- On-prem lane "BMO Corporate Network – SEC1/2 (interim)": SQL Server; ① SSIS ← Redshift/Athena (ODBC pull); ② SSIS bulk load ← NAS ← SFTP (file); CAD/MBA + IDP → SSIS (on-prem). Markers X (SEC lane) and Y (hybrid trunk).
- Governance & Control (N–S: Lake Formation, IAM, KMS, CloudWatch, IAM Identity Center, SNS) grouped in one box.

\newpage

# Slide 14 — Information Lifecycle Mapping

- **Data Creation, Ingestion or Capture — Digital Core (Cloud/On-prem):** IDP (On-Prem Batch) Confidential — BOW, DIS, TRIPS, MECH, XIS, XST. EDF Confidential — PDFF, OLB, CARDBASE, SymcorIQ, PinDrop, OTP, CONNEX, MECH. FileX Confidential — FRAUDCHEX, FraudTrack360, PEGA, RDR, RSA, RSABDB, RSAOLB, TSYSComm, TSYSCons, UFO, weblog, Cyber.
- **Data Processing:** AWS DataSync, AWS Lambda, AWS SFTP, AWS Glue (Standard → Silver → Gold; outbound extract Gold → S3 Outbound).
- **Data Sharing or Publication — FCU Data Lake (Confidential):** Credit card & banking transactions, authentication events, payment / non-payment txns, CC authorizations, Accounts BOR (MECH), Debit Cards BOR (CADS), cheque data, RSA AA scores/alerts/audit, OLB/OLBB events, BMO ATM txn. Consumers: SageMaker Unified Studio, Athena, Power BI; SEC Servers (on-prem) via Redshift/Athena ODBC or S3 Outbound → Transfer Family — classification Confidential preserved (TLS 1.2, KMS).
- **Archiving:** S3 7-year retention; Splunk 2-year logs. SEC1/2 retention under SEC's own record schedule.

\newpage

# Slide 15 — AWS Technology Stack

**① FCU Data Sources:** Three primary data sources feed into the data lake:

- EDF Kafka Topic: near real-time data feeds on Kafka topic.
- BMO Corporate Network: on-premises data including IDP OnPrem batch files and Direct Feeds batch files like TSYS, etc. CAD/MBA continues to load SEC1/2 on-premises and is not ingested to AWS.
- DMINE SybaseIQ DB history migration of approx. ~700 tables with max history of 7 years.

**② Data Ingestion Layer · ③ Landing Zone · ④ Data Processing** — Multiple ingestion methods (MSK/Lambda, SFTP, DataSync); S3 Raw stores original format; EventBridge/Lambda/Step Functions orchestrate; Glue ETL expands to Silver (conformed) and Gold (analytics-ready); selective Crawler; Glue Jobs (PySpark/Python/SQL); Catalog extended with lineage; Glue Job for outbound extract (Gold → S3 Outbound) to serve SEC SSIS.

**⑤ Curated Data Zones** — Silver = conformed domain models; Gold = aggregated, feature-ready, Power BI/Athena-optimized, DQ checkpoints.

**⑥ Data Analytics** — SageMaker Unified Studio, Athena SQL, Redshift Spectrum/Serverless for heavy workloads and as the ODBC endpoint for SEC SSIS; CI/CD and auditability.

**⑦ Data Usage & Consumption**

- API-based access for operational systems (future readiness).
- Unified analytics layer replaces the DMINE consumption path now; the SEC1/2 path follows later; **SEC1/2 retained in the interim and fed from the Gold layer** (Redshift/Athena ODBC pull by SSIS; S3 Outbound → Transfer Family → NAS → SSIS for bulk/history).
- Consumption parity for existing reporting/operational workloads — achieved in the interim by retaining SEC1/2 in place.

**⑧ Governance & Control** — AWS Lake Formation, AWS SNS, AWS IAM, AWS KMS, IAM Identity Center, CloudTrail. AWS → SEC egress over Direct Connect / PrivateLink with SG/NACL allow-list for SEC hosts; dedicated IAM role for Redshift/Athena access; Secrets Manager for SSIS connection credentials.

\newpage

# Slide 16 — Non-Functional Requirement Assertions

| Attribute | Project Requirements | Assertions |
|---|---|---|
| Availability & Reliability | Application Criticality L4; 99.00% availability; RTO 168 hrs (14 days); RPO 72 hrs (3 days); peak 8am–6pm EST weekdays and period ends. | AWS built-in redundancy; maintenance off-hours; S3 Standard 99.99%; Glue, SageMaker Studio, MWAA fully managed with HA meeting L4. |
| Reliability | Data backup; DR required. | S3 archiving of processed files; multi-region S3 replication Canada Central → US East 1; near-real-time copy; no active-passive compute DR this phase. |
| Archiving & Retention | Maximum 7 years for FCU data. | S3 holds 7 years; Splunk 2 years of logs. |
| Performance & Capacity | NRT and batch ingestion; 10+ users for validation (count, schema). | Athena validation by named users; Glue/SageMaker/MWAA auto-scale at 80% CPU; Lambda for NRT and SFTP for batch; CloudWatch + Splunk logging. |
| Scalability & Growth | 15–20% annual growth in storage and compute. | All AWS services designed for scale; S3 auto-scales. |
| SEC1/2 consumption (interim) | SEC SSIS loads of DMINE data must complete within the existing SEC batch window after cut-over from Sybase IQ. | Redshift external tables over Gold sized for SSIS pull volumes; S3 Outbound / Transfer Family path for bulk and history; reconciliation (row counts, checksums) against Sybase IQ during parallel run; Gold layer inherits the RTO/RPO above. |

Contributors: Rakesh Sharma · Reviewer (Service Transition): Daniel Marinelli

\newpage

# Slide 17 — Security Standards and Assertions (1 of 2)

- **Access Control** — AD authentication and AD-group RBAC; privileged accounts via CyberArk (AD Management, EIAM, PAM standards; PTN-EIAM-20). *Aligned:* Azure AD roles for RBAC on the data lake; SageMaker authenticated via AD; separation of duties, least privilege, MFA, PAM, Secrets Management. SSIS service account for Redshift/Athena is a managed service entity under the same EIAM pattern.
- **Data Security** — Classification Confidential; encrypted at rest in BMO Cloud; keys per crypto standard; processing within BMO VPC (Data Classification & Protection; DLP). *Aligned:* TLS 1.2 in transit, encrypted at rest; PGP on SFTP; role/IAM controls; bucket policies per BMO standard. AWS → on-premises SEC egress is a private path (Direct Connect / PrivateLink), TLS 1.2, SG/NACL allow-list to SEC hosts only; S3 Outbound bucket restricted to the Transfer Family role; Confidential classification preserved on SEC1/2.
- **Information Protection** — Static code scanning; base data encryption controls (Secure Software Development; Cryptography Management). *Aligned:* separate environments per SDLC; TLS in transit, CMK & ALE at rest; payload encryption on-prem → AWS and AWS → on-prem; KMS & Secrets Manager for keys.

Solution Architect: Rakesh Sharma · Cyber Advisor: Noreen & Maurer, Chad

# Slide 18 — Security Standards and Assertions (2 of 2)

- **Infrastructure Security** — established security patterns; SDLC adherence (Cloud Security Management; Network Security Management). *Aligned:* separate environments per SDLC; AWS non-prod and prod; CloudTrail and Inspector; BMO standard protocols; Direct Connect / PrivateLink path to SEC hosts under the approved network pattern.
- **Security Monitoring** — baseline hardening and vulnerability assessment; no known findings to production without approved VCMER; logs to Splunk. *Aligned:* CloudWatch and CloudTrail feed Splunk; cloud infrastructure monitored through Wiz.
- **Secure Configuration & Change Management** — Technical Vulnerability & Configuration Management Standard. Automated Qualys scans and remediation by cloud team; CSPM via Wiz; all changes via ServiceNow CHG.
- **Crypto Management** — Cryptography Management Standard; Data Redaction Pattern. Customer-managed keys in AWS KMS; BMO key rotation; key management via Secrets Manager and KMS.

SEC1/2's own on-premises controls are unchanged and outside this SA; only the AWS → SEC consumption interface is in scope.

\newpage

# Slide 19 — Influencing Factors (Management Decisions / Architectural Considerations)

**Management Decisions**

| # | AppCat ID | Decision Summary | Rationale for the Decision | Deviation From | Owner | Exception | T-Shirt Size |
|---|---|---|---|---|---|---|---|
| 1 | 93230 | Sequence migrations — retain SEC1/2 as an interim consumption layer fed from AWS; decommission SEC1/2 later | Parallel migration of DMINE and SEC1/2 workloads puts business consumption at risk; one cutover at a time. Existing use cases stay on SEC1/2 until proven on the lake. | NA | [Dibyendu Bhattacharya – STO] | NA | NA |

**Architectural Considerations**

| # | AppCat ID | Decision Summary | Rationale for the Decision | Owner | T-Shirt Size |
|---|---|---|---|---|---|
| 1 |  | Leveraging the existing ingestion patterns (DataSync, SFTP), also in the outbound direction (Transfer Family) for SEC extracts | Approved ingestion patterns lead to a robust proven design. | Andy Marsh | N/A |
| 2 | 93230 | Leverage existing security and network architecture | Approved security and network patterns lead to robust proven design; Direct Connect / PrivateLink path to SEC hosts under the same patterns. | Noreen & Maurer, Chad | NA |
| 3 | 93230 | Retain CAD/MBA datasets on SEC1/2 (on-premises); no CAD sourcing into the AWS lake | CAD/MBA already serves SEC's consumption needs on-prem; with SEC retained, no CAD copy to AWS is required until SEC1/2 is decommissioned. | Dibyendu | NA |
| 4 |  | Redshift Spectrum/Serverless for DW operations and as the ODBC endpoint for SEC SSIS | MS Fabric is not an approved DW tool in BMO yet; using the existing AWS native tool for DW operations. | Andy Marsh | NA |
| 5 | 93230 | SEC SSIS source: Redshift/Athena ODBC pull (primary); S3 Outbound → SFTP file (bulk/history) | Minimum change to existing SSIS packages; mirrors today's linked-server pattern; file path reuses existing SFTP tooling. Glue JDBC push rejected to keep ETL ownership within SEC1/2 for the interim period. | Rakesh Sharma / Harun Rashid | NA |

\newpage

# Slide 20 — Influencing Factors (Business Considerations)

| # | AppCat ID | Decision Summary | Rationale for the Decision | Owner | T-Shirt Size |
|---|---|---|---|---|---|
| 1 | 93230 | Cost Optimization | Transition from CapEx infrastructure costs to OpEx pay-per-use model with 40-60% cost reduction through auto scaling and reserved instance pricing. | Dibendu |  |
| 2 | 93230 | Analytical Platform | Building Analytical platform for future AI/ML and Analytical/Reporting use cases. | Andy Marsh |  |
| 3 | 93230 | Business continuity of consumption | Retaining SEC1/2 as an interim state keeps SSRS reporting, SSAS cubes, Power BI, Alation/Informatica and the Adhoc / Analytics / Reporting teams on current tooling through the DMINE migration — a single cut-over (DMINE) now, with SEC1/2 following later. SEC on-prem run cost continues at [$__ p.a.] until then. | [Business Owner – FCU] |  |

\newpage

# Slide 21 — Approval Requested & Next Steps

**Approval requested for**

1. Solution Architecture V1.0 — the Fraud Data Lake on AWS (ingestion, Raw/Standardized/Silver/Gold on S3 Tables, Athena, Redshift Spectrum/Serverless, SageMaker Unified Studio) and the DMINE (Sybase IQ) migration to it.
2. SEC1/2 retained on-premises as the interim consumption layer, fed from AWS Gold via Redshift/Athena ODBC pull (primary) and S3 Outbound → Transfer Family (bulk/history) over Direct Connect / PrivateLink.
3. SEC1/2 decommission recorded as out of scope — to follow once its use cases are migrated to the lake.
4. Management Decision #1 (sequenced migrations) recorded in Influencing Factors.

**Next steps**

| Action | Owner | By |
|---|---|---|
| Confirm Direct Connect / PrivateLink path and SG/NACL allow-list to SEC hosts | Cloud Engineering · Network Security | [date] |
| Approve Redshift/Athena ODBC driver on SEC and SSIS service account under EIAM | Cyber Security · SEC administrators | [date] |
| Build the lake (ingestion, curated layers), Gold → S3 Outbound Glue job and Transfer Family outbound | DMINE Data Engineering | [date] |
| Migrate DMINE history (~700 tables) via DataSync; validate; retire Sybase IQ | DMINE Data Engineering | [date] |
| Re-point SSIS packages; parallel run and reconciliation vs Sybase IQ | SEC / Fraud Reporting team | [date] |
| Complete context, application view and ILM drawings in the EA template; circulate to TDRC | Rakesh Sharma | [date] |
| Define the SEC1/2 use-case migration plan and decommission criteria | Rakesh Sharma · Fraud Reporting | [date] |

Please confirm approval or raise objections by [date] so the build can proceed.
