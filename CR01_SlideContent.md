---
title: "FCU (Fraud) Data Lake – Phase 2 — Change Request CR-01 (SEC Servers retained On-Prem)"
subtitle: "Copy-ready slide content in the approved SA v1.0 format · SA Version V1.0 → V1.1"
---

**How to use this document.** Each heading is one slide of the approved SA deck, in the same order and with the same section titles. Text is reproduced from SA v1.0; lines marked **[CHANGED]** replace the original line, **[REMOVED]** lines are deleted from the slide, **[NEW]** lines are added. Slides with no markers are unchanged — keep them as they are in the deck. Bracketed values like [CR-NNNNN] are to be filled in.

\newpage

# Slide 0 (NEW) — Change Request Summary

**Title:** FCU (Fraud) Data Lake – Phase 2 — Change Request CR-01

**What was approved (SA v1.0)**

- Phase 2 delivers analytics-ready Silver/Gold layers on S3 Tables, Redshift Spectrum/Serverless and SageMaker Unified Studio.
- In scope: CAD datasets and workload migration of current SEC1/SEC2 for a unified Fraud Data Lake platform.
- Phase 3: migrate existing analytical and BI use cases; decommission DMINE / SEC1 / SEC2.

**What changes (CR-01 → SA v1.1)**

- SEC1 / SEC2 (Microsoft SQL Server – SSIS / SSAS / SSRS) are retained on-premises and are not migrated in Phase 2.
- SEC's SSIS re-points its DMINE source from Sybase IQ to the AWS Gold layer (Redshift / Athena ODBC pull; S3 Outbound file extract via Transfer Family for bulk/history).
- CAD/MBA and IDP continue to load SEC directly on-premises; CAD is removed from the AWS ingestion scope.

**Why**

- The current business consumption process (Data Management – Alation/Informatica/ServiceNow; Adhoc – DbVisualizer; Analytics – Jupyter/Python/Dataiku/SAS; Reporting – Power BI) is built on the SEC servers. Retaining SEC avoids a second consumption cutover in Phase 2 and de-risks the DMINE decommission.

**Impact**

- Phase 2 scope narrows (no SEC workload migration, no CAD ingestion pipeline).
- One new approved outbound path AWS → SEC over Direct Connect / PrivateLink.
- No change to ingestion, curated layers, analytics services or governance.

**Approval requested**

- SA v1.1 with the revised Phase 2 scope, the AWS → SEC consumption pattern, and Phase 3 redefined as DMINE decommission only (SEC modernization under a separate intake).

\newpage

# Slide 1 — FCU (Fraud) Data Lake – Phase 2 (Involved Parties / Intakes)

**Involved Parties** *(unchanged)*

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
| SA Version | **[CHANGED]** V1.1 (CR-01 to approved V1.0) |
| Change Request ID | **[NEW]** [CR-NNNNN] |

**Enterprise Architect Justification**

FCU is embarking on building an advanced data and analytics capability. Previous (1a) solution/first phase is comprised of moving fraud related data sources to AWS. This new phase 2 introduces curated analytics layers (Silver/Gold), S3 Tables as the transactional table layer over S3, Redshift Spectrum/Serverless for DW and SageMaker Studio Unified for analytics/ML workspaces. **[NEW]** This change request (CR-01) retains the SEC servers (SEC1/SEC2 – SQL Server with SSIS/SSAS/SSRS) on-premises and re-points their DMINE data source to the AWS Gold layer, preserving the existing business consumption process while DMINE is migrated and decommissioned. The solution is advanced to TDRC for visibility, pattern conformance, and approval of the analytics/BI modernization scope.

\newpage

# Slide 2 — Objective

**Problem Statement:** *(unchanged)*

- **Infrastructure Limitations, Scalability Constraints:** Some of the Fraud data infrastructure (data warehouse) operates on legacy on-premises systems with significant storage capacity limitations, high operation cost and maintenance overhead.
- **Limited Analytics Capabilities:** Legacy infrastructure and non-existing centralized data repository constrains advanced analytics capabilities, machine learning implementations, and AI-driven insights. This technological gap limits strategic business intelligence and cross-functional data collaboration opportunities.

**Proposed Solution (Phase 2) — Establish Fraud Data Lake on AWS Cloud Platform**

| Phase | Theme | Description | Status |
|---|---|---|---|
| Phase 1a | Foundation | Build Fraud data lake with DMINE prioritized workloads | Completed |
| Phase 2 | Data Standardization & Analytical Unified Platform | **[CHANGED]** Deliver analytics-ready Silver/Gold layers on S3 Tables, Redshift Spectrum/Serverless and SageMaker Unified Studio to enable ML/advanced analytics. SEC1/SEC2 remain on-premises and consume DMINE data from the AWS Gold layer, providing consumption parity for downstream applications without migrating SEC workloads. | In Scope |
| Phase 3 | Migration & Decommission | **[CHANGED]** Migrate remaining analytical and business intelligence use cases; decommission the existing DMINE (Sybase IQ) platform. SEC1/SEC2 are retained; any future SEC modernization to be assessed under a separate intake. | Out of Scope |

- Build **Silver/Gold** curated layers over Phase 1a data (Standard zone). *(unchanged)*
- Adopt **S3 Tables** (CE recommendation) for ACID semantics, schema evolution, and multi-engine reads on S3. **Redshift Spectrum/Serverless** only for heavy workloads. *(unchanged)*
- Enable **SageMaker Unified Studio** for collaborative notebooks, feature engineering, model prototyping, and governed promotion paths (availability **by May**). *(unchanged)*

**Business Value:**

- Trusted, governed, analytics-ready data for **advanced fraud analytics** and cross-domain insights. *(unchanged)*
- Establishes the foundation for **Phase 3** (DMINE decommission) once adoption and parity are proven. *(unchanged)*
- **[NEW]** Existing SEC-based reporting, OLAP cubes and business tooling continue uninterrupted while DMINE is retired.

**Risks**

- Platform Learning Curve: Development teams require comprehensive training on Data Lake architecture and AWS cloud services; delay of SageMaker unified platform within timelines potentially extending initial implementation timelines. *(unchanged)*
- Working with downstream teams for consumption compatibility with existing business tools; define alternatives where not compatible. *(unchanged)*
- **[NEW]** SSIS re-pointing from Sybase IQ to Redshift/Athena must be reconciled (row counts, schema, checksums) during a parallel run before DMINE cut-over.

**Assumptions**

- Current operations and development of DMINE platform will not be impacted while the cloud enablement efforts are underway. *(unchanged)*

\newpage

# Slide 3 — Scope

**Phase 1a Recap** *(unchanged)*

- AWS Data Lake foundation (S3 Raw/Standard), Glue/Athena, ingestion (EDF/Lambda, SFTP/FileX, DataSync).
- Testing and validation on Athena; initial governance and access controls.

**Phase 2**

- **[CHANGED]** Curated **Silver/Gold layers**, production-grade lineage; **SEC1/SEC2 retained on-premises and consuming DMINE data from the AWS Gold layer**. *(was: "…SEC1/SEC2 workloads, CAD datasets for SEC1/SEC2")*
- **[CHANGED]** Convert lake data into **standardized subject models**, enable **analytics workspaces**, and **modernize consumption** to reduce dependency on the DMINE server. *(was: "…on DMINE/SEC1/2 servers")*

**Scope diagram (right-hand panel) edits**

- Phase 2 (new) column: keep "Silver & Gold (S3 Tables)" and "SageMaker, Redshift, Power BI / Alation, MWAA, DQ, RBAC".
- **[REMOVED]** box "SEC1/SEC2 workloads – CAD & BMA data migration".
- **[NEW]** box "SEC1/SEC2 (on-prem) consume Gold via Redshift/Athena ODBC or S3 outbound extract".
- Phase 3 (future) note: **[CHANGED]** "DMINE decommission" *(was "DMINE / SEC1 / SEC2 decommission")*.

**In Scope**

- **Curated Layers:** Build **Silver** (conformed) and **Gold** (subject/consumption-optimized) on **S3 Tables**. *(unchanged)*
- **Analytics Enablement:** SageMaker Unified Studio workspaces and Redshift Spectrum/Serverless (compute) for DW, governed access to prod data for analysis with CI/CD promotion. *(unchanged)*
- **Consumption Enablement:** Power BI/Athena connectors; compatibility evaluation with existing business tooling; define alternate access where needed. **[NEW]** AWS → SEC1/SEC2 consumption pattern: SSIS pulls DMINE data from the Gold layer via Redshift/Athena ODBC (primary); S3 Outbound → Transfer Family (SFTP) → NAS → SSIS bulk load for bulk/history.
- **Governance:** Data lineage (Alation), DQ checks, RBAC (Lake Formation/IAM), encryption, auditability for analytics. *(unchanged)*
- **[REMOVED]** CAD datasets and workload migration of current SEC1/SEC2 for unified Fraud Data Lake platform.

**Out Of Scope**

- **Full DMINE decommission** (Phase 3). *(unchanged)*
- **[NEW]** SEC1/SEC2 decommission or re-platforming — outside this program.
- **[CHANGED]** New ingestion sources not already covered by Phase 1 unless required by priority use cases. *(was "…sources (except CAD)…")*

**Constraints & Dependencies**

- CE/EA **pattern finalization** (S3 Tables pattern cataloging; SageMaker Unified Studio availability **by May**). *(unchanged)*
- Stakeholder and Governance: UAT by support & vendors validating the current warehouse (DMINE) with the new data lake (unit testing, count, schema checks, etc.) and training on new platform capabilities. *(unchanged)*
- Vendor/Support Agreements: Establishment of escalation procedures and support for AWS Cloud Engineering and adoption teams. *(unchanged)*
- **[NEW]** Network path AWS → SEC hosts (Direct Connect / PrivateLink; SG/NACL allow-list) and approval of the Redshift/Athena ODBC driver on the SEC servers.

\newpage

# Slide 4 — Business View – Business Capabilities

*(unchanged — keep as is)*

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

**Diagram edits**

- **[REMOVED]** Data Sources box "CAD/BMA" (blue, highlighted) and its "Configure" path into Treo FileX 87356 L2. CAD/MBA stays on-premises feeding the SEC servers.
- Keep: ERE, Symcor, PEGA, TSYS, RSA, FIS, FICO … → Treo FileX 87356 → File Data Source → FCS 93230 Enhance L4.
- Keep: Data Sources XIM 1053, XST 1062, MECH 1547, EWS 91359, SNOW, IRIS, BOW 17568, DIS 7038, TRIPS 8812 → IDP OnPrem 13491 → File Data Source → TREO FileX 87356 L2 → FCS 93230.
- Keep: EDF 24996 L2 → Direct Kafka Feed → FCS 93230.
- **[NEW]** Downstream of FCS 93230: box "SEC Servers (SEC1/SEC2 – SQL Server)" with a **New** (blue) arrow from FCS 93230, labelled "DMINE data – Redshift/Athena ODBC or S3 outbound extract".

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
| **[NEW]** SEC Servers (SEC1/SEC2) | NA |

**Legend** — Existing (black), New (blue), Configure (green). *(unchanged)*

\newpage

# Slide 6 — Change Summary

| # | AppCat ID | Name | Summary of Change |
|---|---|---|---|
| 1 | 93230 | Fraud Data Lake | **New:** S3 Tables (over S3) – ACID, schema evolution, time-travel, multi-engine support. SageMaker Unified Studio – collaborative ML/analytics workspace; integration with Git/registries. Redshift Serverless/Spectrum for DW operations. **Enhance:** Glue Data Catalog / Lake Formation – expanded metadata, row/column security, fine-grained permissions. DQ & Lineage Controls – automated checks at Raw→Standard→Silver→Gold; traceability. Orchestration – MWAA for curated pipelines; CI/CD for workspace jobs. **Reuse:** S3, Glue, Athena, Lambda, Step Functions, IAM, KMS, Secrets Manager, CloudWatch/CloudTrail, Splunk – from Phase 1a stack. **[NEW]** Glue outbound-extract job and S3 Outbound bucket; Redshift external tables over Gold exposed to SEC; Transfer Family (SFTP) outbound. |
| 2 | 87356 | FileX | **[REMOVED]** ~~Reuse – CAD data will be copied to AWS FCU cloud data lake using FileX AWS SFTP services.~~ **[CHANGED]** Reuse – FileX continues to deliver IDP and Direct Feed files only (Phase 1a pattern). No CAD copy to AWS. |
| 3 | [SEC AppCat ID] | SEC Servers (SEC1/SEC2) | **[NEW]** Reuse with repoint – SQL Server with SSIS/SSAS/SSRS retained on-premises. SSIS packages loading DMINE data are re-pointed from Sybase IQ to AWS Gold: ① Redshift/Athena ODBC pull (primary); ② S3 Outbound → SFTP → NAS → SSIS bulk load (bulk/history). SSAS cubes, SSRS reports and downstream consumers unchanged. |

\newpage

# Slide 7 — Current State Data Warehouse

*(unchanged — keep the existing drawing)*

**[NEW] Speaker note / caption:** The red-dashed BMA Platform block (SEC1 & SEC2 — ~2000 tables, 10 sources, ~500 data processing jobs; Fraud Reporting, Banking Analytics, Card Analytics teams; ~250 DMINE extract jobs) is retained on-premises under CR-01. The ~250 DMINE extract jobs become the SSIS re-point work.

\newpage

# Slide 8 — Current Business Consumption Process

*(unchanged — this slide is the basis for the CR)*

- CAD/MBA → SEC Servers; DMINE (Sybase IQ) → SEC Servers.
- SEC Servers: SEC1, SEC2 — Microsoft SQL Server (SSAS, SSIS, SSRS).
- How it's used: SSIS – load, transform and standardize DMINE data into tables/views; SSAS – OLAP cubes; SSRS – reporting before Power BI; SEC server also includes data from IDP and CAD/MBA.
- Data Management (Alation, Informatica, ServiceNow) · Adhoc (DbVisualizer) · Analytics (Jupyter, Python, Dataiku, SAS) · Reporting (Power BI).

**[NEW] Caption:** After CR-01 the picture is identical except that the DMINE arrow originates from the AWS Gold layer (Redshift/Athena ODBC or S3 outbound extract) instead of Sybase IQ.

\newpage

# Slide 9 — Business Use Case

*(unchanged — keep as is)*

Set of example business use cases that will be enabled in Phase 2 of the FCU data platform: Synthetic Identity & High-Risk Profile Detection; Scam Detection & Intervention Framework; Cross-Channel Behavioral Analytics; Fraud Alert & Complaint Linkage; Unified Fraud Detection & Analyst Enablement; Insider Risk & Privileged Access Monitoring; Intelligent Fraud Analyst Enablement — all EFM / SageMaker, Redshift Spectrum.

\newpage

# Slide 10 — Application View

**Diagram edits**

- Lane A (Data Sources) — BMO Cloud / Digital Core: EDF (DMINE Topics). BMO Corporate Network (On-Prem): IDP (Netezza On-prem), Direct Feed (Batch Interface), Fraud Datastore (DMINE On-prem) → File Transfer (FileX). *(unchanged)*
- Lane G (Data Consumption) — BMO AWS Cloud: EFM Data Management (Data Catalogue, Data Quality). BMO Corporate Network: Analytics & Reporting Service (BI Visualization [Power BI], DB Visualizer [Jupyter/Dataiku]), MNS, Web & Windows Apps. **[NEW]** add component "SEC Servers (SEC1/SEC2 – SQL Server SSIS/SSAS/SSRS)" under BMO Corporate Network with a flow from Data Warehouse [Redshift Spectrum] (ODBC) and a dashed flow from a new "S3 Outbound → Transfer Family" element.
- Lane E (Curated Data Layer): **[NEW]** "S3 Outbound (SEC extracts)" beside Gold Layer.
- Lane D (Data Transformation): **[NEW]** one Glue Job "Outbound extract (Gold → S3 Outbound)".

**Right-hand notes**

- **A: Data Sources** — Reuse all Phase 1 sources: EDF (NRT), FileX (Batch), IDP, DMINE history. **[REMOVED]** ~~Additional datasets onboarded based on analytics use case needs (e.g., CAD)~~. **[NEW]** CAD/MBA remains on-premises feeding SEC directly.
- **B, C: Data Processing & Curated Zones** — EventBridge, Lambda, MWAA orchestrate curated pipelines. *(unchanged)*
- **D:** Glue ETL transforms Standard → Silver (Conformed models); Glue ETL builds Gold (Analytics-ready) datasets. **[NEW]** Glue job extracts Gold to S3 Outbound for SEC.
- **E:** Curated layers stored using S3 Tables for ACID + schema evolution. *(unchanged)*
- **F: Analytics & ML Layer** — SageMaker Studio Unified for notebooks, feature engineering, ML prototyping; Athena for SQL analytics and BI connectivity; feature sets prepared in Gold Zone. **[NEW]** Redshift Spectrum external tables over Gold are the ODBC endpoint for SEC SSIS.
- **Governance & Security** — Lake Formation fine-grained RBAC; IAM / Identity Center; KMS encryption; CloudTrail + CloudWatch → Splunk; SNS alerts. **[NEW]** AWS → SEC egress over Direct Connect / PrivateLink with SG/NACL allow-list to SEC hosts.

\newpage

# Slide 11 — Fraud Data Lake Phase (AWS) High Level

**Diagram edit (Consumption column only)**

- **[NEW]** Add a third box "On-prem SEC Servers (SQL Server – SSIS / SSAS / SSRS)" fed from Amazon Redshift (ODBC, solid) and from a new "S3 Outbound → Transfer Family" path (dashed). Annotate the link "Direct Connect / PrivateLink · TLS 1.2".
- All other elements (FCU Data Sources, Data Ingestion Layer, Data Lake/ETL, Compute, Visualization – Power BI, Analytics – SageMaker Unified Studio) unchanged.

\newpage

# Slide 12 — Technology View : Fraud Data Lake Architecture AWS – (Phase 2)

**Replace with the re-drawn Technology View (file: EA_TV_DMINE_SEC_OnPrem.png).** Changes versus v1.0:

- Lane A (FCU Data Sources): **[REMOVED]** CAD/BMA (Batch Interface) from the DMINE Data Source block; **[NEW]** small block "Stays on-prem → SEC Servers: CAD/MBA, IDP (SEC extract)".
- Lanes B–G: unchanged. **[NEW]** in D – Glue Job (SEC extract); in E – S3 Outbound (SEC extracts); in F – AWS Transfer Family (outbound).
- **[NEW]** Lane H — SEC Servers – SEC1/SEC2 (Microsoft SQL Server · SSIS/SSAS/SSRS), retained in BMO Corporate Network: How it's used; "Was: SSIS ← DMINE (Sybase IQ) / Now: SSIS ← AWS Gold (Redshift/Athena ODBC or S3 extract)"; Business Consumption (unchanged); Security: TLS 1.2, IAM role/STS, KMS, Azure AD SSO, SG/NACL allow-list over Direct Connect.
- Phase bands: PHASE-1 (A–E) · PHASE-2 (F) · **[CHANGED]** "ON-PREM – SEC SERVERS RETAINED (NO CHANGE TO CONSUMERS)" *(was PHASE-3)*.

\newpage

# Slide 13 — Fraud Data Lake Phase (AWS) Low Level

**Replace with the re-drawn Low Level view (file: EA_LL_DMINE_SEC_OnPrem.png).** Changes versus v1.0:

- **[REMOVED]** BMA/CAD (Batch Interface) and marker A′ from the TREO FileX inputs; remaining markers A (IDP) and A′ (Direct Feed), A‴ (EDF).
- **[NEW]** marker L″ — AWS Glue Job (Outbound extract for SEC SSIS) → S3 Outbound (SEC1/2 extracts).
- **[NEW]** Consumption → SEC1/SEC2 block: P – Redshift/Athena (Gold external tables); P′ – Transfer Family (outbound SFTP); Direct Connect / PrivateLink egress; security note (TLS 1.2, IAM role/STS, KMS, SG/NACL allow-list).
- **[NEW]** on-prem lane "BMO Corporate Network – SEC Servers (retained)": SEC1, SEC2, SQL Server; ① SSIS ← Redshift/Athena (ODBC pull); ② SSIS bulk load ← NAS ← SFTP (file); CAD/MBA + IDP → SSIS (on-prem, unchanged). Markers X (SEC lane) and Y (hybrid trunk).
- Governance & Control (N–S: Lake Formation, IAM, KMS, CloudWatch, IAM Identity Center, SNS) grouped in one box. *(content unchanged)*

\newpage

# Slide 14 — Information Lifecycle Mapping

- **Data Creation, Ingestion or Capture — Digital Core (Cloud/On-prem):** IDP (On-Prem Batch) Confidential — BOW, DIS, TRIPS, MECH, XIS, XST. EDF Confidential — PDFF, OLB, CARDBASE, SymcorIQ, PinDrop, OTP, CONNEX, MECH. FileX Confidential — **[REMOVED]** ~~CAD,~~ FRAUDCHEX, FraudTrack360, PEGA, RDR, RSA, RSABDB, RSAOLB, TSYSComm, TSYSCons, UFO, weblog, Cyber.
- **Data Processing:** AWS DataSync, AWS Lambda, AWS SFTP. **[NEW]** AWS Glue (outbound extract Gold → S3 Outbound).
- **Data Sharing or Publication — FCU Data Lake (Confidential):** list unchanged. **[NEW]** sharing target "SEC Servers (on-prem) via Redshift/Athena ODBC or S3 Outbound → Transfer Family"; classification Confidential preserved (TLS 1.2, KMS).
- **Archiving:** unchanged.

\newpage

# Slide 15 — AWS Technology Stack

**① FCU Data Sources:** Three primary data sources feed into the data lake:

- EDF Kafka Topic: near real-time data feeds on Kafka topic. *(unchanged)*
- **[CHANGED]** BMO Corporate Network: on-premises data including IDP OnPrem batch files and Direct Feeds batch files like TSYS, etc. *(was "…including CAD, IDP OnPrem…")* **[NEW]** CAD/MBA continues to load SEC on-premises and is not ingested to AWS.
- DMINE SybaseIQ DB history migration of approx. ~700 tables with max history of 7 years. *(unchanged)*

**② Data Ingestion Layer · ③ Landing Zone · ④ Data Processing** — unchanged. **[NEW]** under ④: Glue Job for outbound extract (Gold → S3 Outbound) to serve SEC SSIS.

**⑤ Curated Data Zones (NEW for Phase 2)** — unchanged.

**⑥ Data Analytics (NEW for Phase 2)** — unchanged. **[NEW]** Redshift Spectrum external tables over Gold also serve as the ODBC endpoint for SEC SSIS.

**⑦ Data Usage & Consumption (Phase 2/3)**

- API-based access for operational systems (Phase 3 readiness). *(unchanged)*
- **[CHANGED]** Unified analytics layer replacing the DMINE consumption path; **SEC1/SEC2 retained on-premises and fed from the Gold layer** (Redshift/Athena ODBC pull by SSIS; S3 Outbound → Transfer Family → NAS → SSIS for bulk/history). *(was "Unified analytics layer replacing DMINE/SEC1/SEC2 consumption paths")*
- Consumption parity for existing reporting/operational workloads — **[CHANGED]** achieved by retaining SEC in place.

**⑧ Governance & Control** — AWS LakeFormation, AWS SNS, AWS IAM, AWS KMS, IAM Identity Center, CloudTrail — unchanged. **[NEW]** AWS → SEC egress over Direct Connect / PrivateLink with SG/NACL allow-list for SEC hosts; dedicated IAM role for Redshift/Athena access; Secrets Manager for SSIS connection credentials.

\newpage

# Slide 16 — Non-Functional Requirement Assertions

*(all existing rows unchanged — Application Criticality L4; 99.00% availability; RTO 168 hrs; RPO 72 hrs; DR multi-region S3 replication; 7-year retention; auto-scaling; 15–20% growth)*

**[NEW] row**

| Attribute | Project Requirements | Assertions |
|---|---|---|
| SEC consumption (CR-01) | SEC SSIS loads of DMINE data must complete within the existing SEC batch window after cut-over from Sybase IQ. | Redshift external tables over Gold sized for SSIS pull volumes; S3 Outbound / Transfer Family path for bulk and history; reconciliation (row counts, checksums) against Sybase IQ during parallel run; Gold layer inherits the RTO/RPO above. |

\newpage

# Slide 17 — Security Standards and Assertions (1 of 2)

- **Access Control** — unchanged. **[NEW]** under Alignment: SSIS service account for Redshift/Athena is a managed service entity under the same EIAM pattern (Service Entity Access Management).
- **Data Security** — unchanged. **[NEW]** under Alignment: AWS → on-premises SEC egress is a private path (Direct Connect / PrivateLink), TLS 1.2, SG/NACL allow-list to SEC hosts only; S3 Outbound bucket restricted to the Transfer Family role; Confidential classification preserved on SEC.
- **Information Protection** — unchanged. **[NEW]** under Alignment: payload encryption and TLS controls apply equally in the reverse (AWS → on-prem) direction.

# Slide 18 — Security Standards and Assertions (2 of 2)

*(unchanged — Infrastructure Security, Security Monitoring, Secure Configuration & Change Management, Crypto Management)*

\newpage

# Slide 19 — Influencing Factors (Management Decisions / Architectural Considerations)

**Management Decisions** *(table was empty in v1.0)*

| # | AppCat ID | Decision Summary | Rationale for the Decision | Deviation From | Owner | Exception | T-Shirt Size |
|---|---|---|---|---|---|---|---|
| 1 | 93230 | **[NEW]** Retain SEC1/SEC2 on-premises; SEC consumes DMINE data from the AWS Gold layer | Preserves the existing business consumption process (SSIS/SSAS/SSRS, Power BI, Alation, Dataiku/SAS) during the DMINE migration; removes a second migration from Phase 2; SEC modernization to be assessed under a separate intake. | SA v1.0 Phase 2 scope | [Dibyendu Bhattacharya – STO] | NA | NA |

**Architectural Considerations**

| # | AppCat ID | Decision Summary | Rationale for the Decision | Owner | T-Shirt Size |
|---|---|---|---|---|---|
| 1 |  | **[CHANGED]** Leveraging the existing ingestion patterns (DataSync, SFTP) — including Transfer Family in the outbound direction for SEC extracts | **[CHANGED]** Approved ingestion patterns lead to a robust proven design. *(removed "for CAD data source")* | Andy Marsh | N/A |
| 2 | 93230 | Leverage existing security and network architecture | Approved security and network patterns lead to robust proved design. **[NEW]** Direct Connect / PrivateLink path to SEC hosts to be confirmed under the same patterns. | Noreen & Maurer, Chad | NA |
| 3 | 93230 | **[CHANGED]** Retain CAD/MBA datasets on SEC (on-premises); no CAD sourcing into the AWS lake | **[CHANGED]** CAD/MBA already serves SEC's consumption needs on-prem; with SEC retained, no CAD copy to AWS is required in Phase 2. *(was: sourcing CAD on-prem instead of DnA Data Lake)* | Dibyendu | NA |
| 4 |  | Redshift Spectrum/Serverless for DW operations | MS Fabric is not an approved DW tool in BMO yet; using the existing AWS native tool for DW operations. **[NEW]** Also the ODBC endpoint for SEC SSIS. | Andy Marsh | NA |
| 5 | 93230 | **[NEW]** SEC SSIS source re-point: Redshift/Athena ODBC pull (primary); S3 Outbound → SFTP file (bulk/history) | **[NEW]** Minimum change to existing SSIS packages; mirrors today's linked-server pattern; file path reuses Phase 1a SFTP tooling. Glue JDBC push rejected to keep ETL ownership within SEC. | Rakesh Sharma / Harun Rashid | NA |

\newpage

# Slide 20 — Influencing Factors (Business Considerations)

| # | AppCat ID | Decision Summary | Rationale for the Decision | Owner | T-Shirt Size |
|---|---|---|---|---|---|
| 1 | 93230 | Cost Optimization | Transition from CapEx infrastructure costs to OpEx pay-per-use model with 40-60% cost reduction through auto scaling and reserved instance pricing. | Dibendu |  |
| 2 | 93230 | Analytical Platform | Building Analytical platform for future AI/ML and Analytical/Reporting use cases. | Andy Marsh |  |
| 3 | 93230 | **[NEW]** Business continuity of consumption | **[NEW]** Retaining SEC1/SEC2 keeps SSRS reporting, SSAS cubes, Power BI, Alation/Informatica and the Adhoc / Analytics / Reporting teams on current tooling through the DMINE migration — no retraining or report re-build in Phase 2 and a single cut-over (DMINE) instead of two. SEC on-prem run cost continues at [$__ p.a.]. | [Business Owner – FCU] |  |

\newpage

# Slide 21 (NEW) — Approval Requested & Next Steps

**Approval requested for**

1. SA v1.1 — Phase 2 scope without SEC1/SEC2 workload migration and without CAD ingestion to AWS.
2. AWS → SEC consumption pattern: Redshift/Athena ODBC pull (primary) and S3 Outbound → Transfer Family file path (bulk/history) over Direct Connect / PrivateLink.
3. Phase 3 redefined as DMINE (Sybase IQ) decommission only; SEC modernization under a separate intake.
4. Management Decision #1 recorded in Influencing Factors.

**Next steps**

| Action | Owner | By |
|---|---|---|
| Confirm Direct Connect / PrivateLink path and SG/NACL allow-list to SEC hosts | Cloud Engineering · Network Security | [date] |
| Approve Redshift/Athena ODBC driver on SEC and SSIS service account under EIAM | Cyber Security · SEC administrators | [date] |
| Build Gold → S3 Outbound Glue job and Transfer Family outbound | DMINE Data Engineering | [date] |
| Re-point SSIS packages; parallel run and reconciliation vs Sybase IQ | SEC / Fraud Reporting team | [date] |
| Update SA v1.1 drawings (context, application view, high level, ILM) and circulate to TDRC | Rakesh Sharma | [date] |

Please confirm approval or raise objections by [date] so the Phase 2 build can proceed on the revised scope.
