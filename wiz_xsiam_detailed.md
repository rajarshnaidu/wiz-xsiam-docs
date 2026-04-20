# Palo Alto XSIAM + Wiz Integration — Expert Reference

## What is Wiz

Wiz is a Cloud-Native Application Protection Platform (CNAPP) — currently the highest-rated in the market (Forrester Wave CNAPP Leader Q1 2026, IDC MarketScape CNAPP Leader 2025). It was acquired by Google/Alphabet for $32 billion in 2025, the largest cybersecurity acquisition ever. Despite the acquisition, Wiz continues operating as an independent product with its own integrations ecosystem.

The core differentiator of Wiz vs every other cloud security tool: it is 100% agentless. It connects directly to cloud APIs (AWS, Azure, GCP, OCI, AKS, EKS, GKE) and scans thousands of resources within minutes with zero deployment overhead. No agents, no sensors, no configuration on workloads.

### What Wiz Actually Does — The Full Capability Stack

**CSPM — Cloud Security Posture Management**
Continuous scanning of cloud configurations across all accounts and regions. Detects misconfigurations against CIS, NIST, SOC2, PCI-DSS, HIPAA, ISO 27001 benchmarks. Every misconfigured storage bucket, overly permissive IAM role, publicly exposed database, unencrypted volume — Wiz finds it automatically and continuously.

**CWPP — Cloud Workload Protection Platform**
Scans VMs, containers, serverless functions, and Kubernetes clusters for vulnerabilities. OS-level CVEs, application package vulnerabilities, container image vulnerabilities. Does this without agents by pulling disk snapshots at the hypervisor level.

**CIEM — Cloud Infrastructure Entitlement Management**
Maps every identity (human and machine) to every permission across all cloud accounts. Identifies toxic combinations — e.g. a service account with admin access that is also internet-exposed. This is where Wiz finds lateral movement paths.

**DSPM — Data Security Posture Management**
Scans actual content of cloud storage (S3, Azure Blob, GCS, RDS, BigQuery, Snowflake) using regex classifiers and ML-based pattern matching to detect PII, PCI data, PHI. Result: you know which misconfigurations actually matter because the misconfigured bucket actually contains sensitive data.

**KSPM — Kubernetes Security Posture Management**
Ingests Kubernetes API server audit logs. Detects privileged containers, host network access, overpermissioned service accounts, missing network policies.

**WizDefend — Runtime Threat Detection (the most relevant for XSIAM)**
Wiz's real-time threat detection capability. This is what feeds alerts into XSIAM. Uses eBPF-based sensors (lightweight, kernel-level) to detect active threats in runtime: lateral movement, privilege escalation, crypto mining, command injection, reverse shells, suspicious process execution, data exfiltration attempts. This is where Wiz moves from posture management into active detection.

**IaC Security**
Scans Terraform, CloudFormation, Pulumi, ARM templates in CI/CD pipelines before deployment. Shift-left security — catch misconfigurations before they ever reach production.

**AI-SPM — AI Security Posture Management**
Discovers shadow AI — unmanaged AI models, training datasets, AI API endpoints (SageMaker, Azure OpenAI, Vertex AI) spun up without security review. Flags AI models with public inference endpoints and training data with sensitive content.

### The Security Graph — Wiz's Core Technology

The thing that makes Wiz genuinely different from point tools is the Security Graph. Instead of showing individual findings in isolation, Wiz builds a graph of your entire cloud environment — every resource, every identity, every connection, every data flow — and then overlays all security findings on top of it.

This enables **toxic combination detection**: finding combinations of risks that individually would be low priority but together create a critical attack path. Example: a VM has a medium severity CVE (not critical alone) + that VM's service account has S3 read access (not critical alone) + that S3 bucket contains PII (now it matters) + that VM is internet-exposed (now you have a full attack chain). Wiz surfaces this as a single critical "toxic combination" issue rather than 4 separate medium findings.

This is the primary reason Wiz integrates so powerfully with XSIAM — you're not getting noise, you're getting pre-contextualized, pre-correlated cloud attack chain findings.

---

## What is Palo Alto XSIAM

XSIAM (Extended Security Intelligence and Automation Management) is Palo Alto Networks' next-generation SOC platform. It is the successor to traditional SIEM. Currently surpassing $1B in cumulative bookings (2025), growing 200% YoY ARR.

Key capabilities: AI-driven threat detection across all telemetry, native XSOAR automation (1.2 billion playbook executions trained on), MITRE ATT&CK coverage dashboards, AgentiX for agentic AI workflows, unlimited data ingestion, real-time correlation across endpoint/network/cloud/identity/application telemetry.

XSIAM is relevant here because it is the SOC brain — Wiz is the cloud visibility engine. Together they close the gap between cloud posture and SOC response.

---

## The Wiz Content Pack on XSIAM Marketplace — What's Actually Available

The Wiz content pack (version 2.0.1, last updated October 2025) is available on the Cortex Marketplace and works on both XSOAR and XSIAM. Here is everything it provides:

### Two Integrations

**1. Wiz Integration (posture/issues)**
Handles Wiz Issues — the findings from CSPM, CWPP, CIEM, vulnerability management, misconfiguration detection. Bidirectional. You can pull issues from Wiz into XSIAM and push status updates back.

**2. WizDefend Integration (runtime threat detection)**
Added in pack version 2.0.0 (June 2025). This is the new one and the more operationally critical for a SOC. Handles real-time threat detections from WizDefend — active threats, not posture findings. This is your active attack surface — lateral movement, privilege escalation, runtime anomalies.

### What You Can Extract from Wiz into XSIAM

**From Wiz Issues (posture side):**
- Toxic combination findings with full attack chain context
- Vulnerability findings per resource (CVEs with CVSS scores, affected package, fix available)
- Misconfiguration findings (CIS benchmark violations, policy violations)
- Exposed secrets/credentials in cloud environments
- Identity and entitlement risks (overpermissioned roles, lateral movement paths)
- Data exposure risks (sensitive data in misconfigured storage)
- Issue severity, status, due dates, project ownership
- Resource metadata: cloud platform (AWS/Azure/GCP), account ID, account name, region, resource type, native resource ID

**From WizDefend (runtime/active threat side):**
- Real-time threat detections with full context
- Detection ID, Detection URL (links back to Wiz UI)
- Threat type classification (lateral movement, privilege escalation, crypto mining, etc.)
- Affected resource full context (what got hit)
- Cloud platform, account, region
- Severity and origin
- Filterable by: severity, platform, origin, cloud accounts

**Commands available (WizDefend):**
- wiz-defend-get-detections — fetch all current detections with filters
- wiz-defend-get-detection — get single detection detail
- wiz-defend-get-threat — get single threat detail
- wiz-defend-get-threats — list all threats
- wiz-defend-reopen-threat — reopen a closed threat
- wiz-defend-resolve-threat — resolve/close a threat (bidirectional status sync)
- wiz-defend-set-threat-in-progress — mark as being investigated

**Commands available (Wiz Issues):**
- wiz-get-issues — fetch issues with filters
- wiz-get-issue — single issue details
- wiz-set-issue-status — update status back to Wiz
- wiz-get-resources — fetch resource details (new in v2.0.0)
- wiz-reopen-issue
- wiz-reject-issue

### Incident Types Created in XSIAM
- Wiz Issue — for posture/misconfiguration/vulnerability findings
- WizDefend Detection — for active runtime threat detections

### Field Mapping (what enriches your XSIAM incidents)
Every Wiz incident in XSIAM carries: Wiz Cloud Platform, Wiz Cloud Account, Wiz Cloud Account Name, Wiz Resource ID, Wiz Resource Cloud ID, Wiz Resource Type, Wiz Resource Native Type, Wiz Resource Region, Wiz Issue ID, Wiz Issue URL, Wiz Issue Note, Wiz Issue Due Date, Wiz Issue Resolution Recommendation, Wiz Issue Projects, Wiz Project Owners, Wiz Project Security Champions, Wiz Detection ID, Wiz Detection URL, Wiz Details.

### Classifiers and Mappers
- Wiz Classifier — auto-classifies incoming Wiz issues by type
- Wiz Mapper — maps Issue fields to XSIAM incident fields
- Wiz Mapper Webhook — for webhook/push-based delivery (real-time)
- WizDefend Mapper — maps detection fields for automation playbooks

### Out-of-Box Playbooks
- **Assess Wiz Issues** — basic triage and assessment workflow for posture issues
- **Assess WizDefend Detections** — triage workflow for active runtime detections

### Ingestion Methods
1. **Pull (polling)** — XSIAM fetches issues/detections on a schedule via the built-in fetch mechanism
2. **Push (webhook)** — Wiz sends alerts in real-time via Generic Webhook integration. This is the preferred method for WizDefend — you want zero latency on active threat detections hitting your SOC

---

## Integration Architecture — How to Set It Up

**Authentication:** Wiz uses OAuth 2.0 client credentials. You create a Service Account in Wiz, get a client ID and secret, configure the integration instance in XSIAM with the Wiz tenant URL and credentials.

**For Wiz Issues (polling):**
Configure the integration instance with fetch enabled. Set fetch filters (severity threshold, issue types, status). XSIAM polls on your configured interval and creates incidents for new/updated issues.

**For WizDefend (webhook/push):**
Configure the Generic Webhook integration in XSIAM to receive inbound Wiz webhooks. Configure outbound webhook in Wiz's settings pointing to your XSIAM webhook URL. Select detection types and severity thresholds. This gives you real-time detection-to-incident creation with effectively zero latency.

**Bidirectional sync:** Both integrations support writing back to Wiz. When your SOC resolves an incident in XSIAM, the playbook can call wiz-defend-resolve-threat or wiz-set-issue-status to sync the state back to Wiz, keeping both platforms consistent without manual double-entry.

---

## What the Cyber Defense and Investigation Team Gets

### For Detection and Response (DFIR/SOC analysts)

1. **Pre-contextualized cloud incidents.** Instead of getting a raw alert that "process X was executed on instance Y", you get a WizDefend detection enriched with the full Wiz Security Graph context — the resource's known vulnerabilities, its IAM permissions, what data it has access to, whether it's internet-exposed, what lateral movement paths exist from it. Your analyst starts an investigation already knowing the blast radius.

2. **Toxic combination correlation.** A CSPM finding about an overpermissioned service account that was previously sitting in a posture backlog suddenly becomes a CRITICAL incident when WizDefend detects suspicious activity from that same resource. The combination creates the escalation, not either finding alone.

3. **Cloud-native IOC enrichment.** Every detection comes with cloud-specific context: cloud provider, account, region, resource type, native cloud resource ID. For AWS incidents this means you can immediately pivot to CloudTrail. For Azure you go to Activity Logs. The resource identifiers are already in the XSIAM incident — no manual lookups.

4. **Automated triage and response.** XSIAM playbooks can automatically: enrich the detection with additional Wiz resource context, check if the resource is part of a critical project (via Wiz Project data), cross-correlate with XSIAM endpoint and network telemetry for the same resource, notify the resource owner (Wiz Project Owner field), and take automated containment actions.

5. **Lateral movement detection.** WizDefend is specifically strong at detecting lateral movement in cloud environments — an attacker who has compromised one workload and is attempting to move to others via cloud APIs, assumed roles, or instance metadata service abuse. This is one of the hardest attack vectors to detect with traditional SIEM.

6. **Full attack chain from code to cloud.** If Wiz detects a vulnerability in a container image (IaC scanning), that same container gets deployed and WizDefend detects exploitation, you have the full chain: source code vulnerability → deployment → exploitation → lateral movement, all in one platform feeding into one XSIAM incident.

### For Investigation (Threat Hunting / Purple Team)

1. **Historical posture data for forensics.** What was the security posture of the compromised resource before the attack? Wiz maintains history of resource state, configurations, and finding status. In post-incident analysis this is critical — was this resource misconfigured before the breach? For how long? Who owned it?

2. **Attack path reconstruction.** The Wiz Security Graph allows you to reconstruct exactly which lateral movement paths were available to an attacker from their initial entry point. Even if they didn't take those paths, knowing what was exploitable helps scope the incident properly.

3. **Cross-account blast radius assessment.** In cloud environments, one compromised workload often has access to other accounts and regions via IAM roles. Wiz's CIEM data maps those access paths — critical for blast radius assessment in a major incident.

4. **wiz-get-resources command.** Added in v2.0.0, this lets XSIAM playbooks directly query Wiz for the current state of any cloud resource — pulling fresh context into an investigation even after the initial detection.

### For Compliance and Risk Teams

Wiz issues representing compliance violations (CIS, NIST, PCI, HIPAA) feed directly into XSIAM as tracked incidents with project ownership, due dates, and resolution recommendations already populated. This means the same SOC workflow handles both active threats and compliance findings with full audit trail.

---

## Wiz's Acquisition by Google — What It Means for the Integration

Google acquired Wiz for $32B in 2025. Wiz continues operating independently with its own roadmap and third-party integrations maintained. The XSIAM content pack was updated as recently as October 2025 post-acquisition, confirming the integration is actively maintained. However, over the medium term, Wiz will likely deepen its native integration with Google Cloud Security Command Center and Chronicle SIEM. Organizations running XSIAM should be aware this integration will remain valid but the strategic trajectory of Wiz is toward Google's ecosystem.

---

## Recommended Integration Configuration for a SOC Team

1. Install Wiz content pack from Cortex Marketplace (includes Wiz + WizDefend + all playbooks, classifiers, mappers)
2. Configure Wiz integration (polling) for posture issues — severity HIGH and CRITICAL only to manage volume
3. Configure WizDefend via webhook for real-time runtime detections — all severities, filter later in playbook
4. Set incident types: Wiz Issue → L2 analyst queue, WizDefend Detection → immediate L1 triage
5. Customise Assess WizDefend Detections playbook to: enrich with endpoint telemetry from XSIAM, check XSIAM threat intel for IOCs in the detection, notify cloud account owner via email/Slack, auto-tag affected resource in Wiz
6. Build custom correlation rule in XSIAM: when WizDefend Detection AND an existing open Wiz Issue for same resource ID → escalate to CRITICAL
7. Enable bidirectional sync so XSIAM incident closure syncs back to Wiz threat status

---

## Summary: What You're Getting from This Integration

Wiz handles the cloud visibility and cloud-native threat detection that traditional SIEM and EDR tools fundamentally cannot do. XSIAM handles the correlation, automation, and response orchestration that Wiz is not built for. Together they cover the full lifecycle: Wiz sees the cloud attack surface and active cloud threats, XSIAM correlates that with everything else in your environment (endpoints, network, identity, email) and drives the response workflow. The integration is bidirectional, actively maintained (latest update October 2025), and comes with out-of-box playbooks that you can use immediately and customise from there.
