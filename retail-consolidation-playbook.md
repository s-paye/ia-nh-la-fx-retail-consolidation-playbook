# Retail Consolidation Project Playbook
## IA / LA / NH Rebuilds — Lead: Saydu Paye (s.paye@draftkings.com)

> **Your role:** Infrastructure lead. You own the infra sequencing, are the primary stakeholder interface, and are responsible for keeping the project ahead of blockers — not reacting to them.

---

## 1. Project Charter

| Field | Detail |
|---|---|
| Projects in scope | Iowa (IA), Louisiana (LA), New Hampshire (NH) — plus **Foxwoods** (separate track, iGaming/tribal requirements) |
| Driver | EOL hardware, security/patching gaps, stability risk, cost optimization |
| Architecture | Shared hardware / virtualization layer; **separate OSB and Retail clusters** (due to existing retail dependencies) |
| Infra start target | **June 2026** (~1–1.5 sprints, assuming proper planning and resourcing) |
| Physical scope | **No physical site moves** — workloads remain within existing data centers |
| OSB impact | No major OSB impact expected outside of planned maintenance-window cutovers |
| Compliance base | Existing NJ/Michigan documentation reused and updated per jurisdiction |
| Goal | Migrate retail workloads to supported infra, reduce operational overhead, simplify environments, optimize hardware utilization |
| Your accountability | Timeline, RAID log, milestone tracking, Go/No-Go, Release Mgmt coordination |
| Critical path team | **Retail Engineering** — engage first, confirm scope & capacity before anything else |

### Key People (confirmed from pre-kickoff meeting)

| Name | Role | Jira account |
|---|---|---|
| Saydu Paye | Lead — regulatory + tribal engagement (IA/LA/NH + Foxwoods) | s.paye@draftkings.com |
| Andrii Protsenko | Retail epic/workstream coordination | a.protsenko@draftkings.com |
| LaDarius Owens | Retail epic/workstream coordination + regulatory/tribal (Foxwoods) | l.owens@draftkings.com |
| Mike Emler | Compliance/regulatory documentation (update + distribute) | m.emler@draftkings.com |
| Eric Beisecker | Infra lead/assignee on all 4 sagas (IA/LA/NH + Foxwoods) | TBD |
| Infra Leadership | Cutover sequencing + timeline confirmation | TBD |

### Foxwoods — Separate Track

Foxwoods is **not** part of the IA/LA/NH consolidation. It runs as a parallel but independent workstream due to iGaming and tribal compliance requirements. Owner: LaDarius Owens (PM), Eric Beisecker (assignee). Do not let Foxwoods dependencies block the IA/LA/NH cutover.

Child epics under INFRAENG-519:
- INFRAENG-1213 — DBI - FX Rebuild Data Center (Backlog, due Jun 21 2026)
- INFRAENG-7543 — FXDC02 Foxwoods Rebuild (NetOps) — In Progress
- INFRAENG-8100 — Cluster Deployment: CT02NX100-PE OSB/Retail — In Progress [Now]
- INFRAENG-12638 — FXDC02 Rebuild (Kilo) — Backlog ⚠️

---

## 2. Stakeholder Map

| Team | What they need from you | What you need from them | Scope-creep risk |
|---|---|---|---|
| PM | Status updates, RAID log, milestone hits | Business objective, cutover date | Low |
| DC Ops | Ticket + timeline | Capacity confirmation, hardware readiness | Low |
| Net Ops | Firewall/routing requirements, timeline | Connectivity validation sign-off | Medium — can block infra |
| Core Infra | Provisioning request, architecture diagram | Environment readiness sign-off | Low |
| DB Ops | DB provisioning request | Infra validation, migration plan | Medium — SYSDBA dependency |
| SYSDBA | Migration plan hand-off | DB access/perf validation, rollback plan | High — migration is complex |
| PaaS | App dependency list (Kafka, Vault, k8s) | Services deployed + secrets configured | High — endpoint changes trigger reg review |
| BI | Source/target data endpoints | Reporting validation sign-off | Low |
| Retail Engineering | Full app scope EARLY, config changes needed | Testing complete, cutover sign-off | **CRITICAL** — most common scope expansion vector |
| Release Management | RCM ticket, cutover plan, testing evidence | Maintenance window, CAB/reg approval | High — approval timelines vary by jurisdiction |
| Regulatory/Compliance | Arch diagram, endpoint changes, hosting changes | Approval confirmation per jurisdiction (IA/LA/NH differ) | High — slowest approval cycle |

**Scope creep watchpoints:**
- Any endpoint change → triggers regulatory review (do not let PaaS or RE make changes without flagging you)
- IP whitelisting changes → Net Ops ticket required, may need reg notification
- SFTP → treat as a **separate workstream**, do not let it block Day 1 cutover
- Post-cutover follow-up items → track explicitly; do not let them slip into the cutover scope

---

## 3. Execution Phases & Your Action Items

### Phase 0 — Project Kickoff (Before anything else)

**Your personal checklist — do these in the first week:**

- [ ] Confirm business objective with PM in writing (email/Jira comment)
- [ ] Document source and target environments for IA, LA, NH
- [ ] Confirm regulatory path for each jurisdiction — IA, LA, NH have different approval timelines
- [ ] Engage **Retail Engineering immediately** — get scope defined before infra work starts
- [ ] Engage **Release Management immediately** — do not wait until cutover is near
- [ ] Identify and book maintenance window (align with other planned downtime if possible)
- [ ] Create project tracking: Jira epic + Airtable board
- [ ] Establish RAID log (Risks, Assumptions, Issues, Decisions)
- [ ] Get architecture diagrams assigned or drafted
- [ ] Select cutover date with business awareness (avoid high-traffic events, regulatory blackout windows)
- [ ] Create Slack channel for project comms and add all team leads

---

### Phase 1 — Infrastructure Readiness (Parallel: DC Ops + Net Ops + Kilo + DBI)

> **Gate:** Retail consolidation Phase 1 cannot close until the infra sagas below are complete enough to host workloads. IA (INFRAENG-6912) and NH (INFRAENG-6909) are already past due as of May 2026. Confirm revised completion dates from Eric Beisecker before committing to a cutover date.

**Active infra epics — track these weekly:**

| Epic | Jurisdiction | Team | Status |
|---|---|---|---|
| INFRAENG-8135 | IA — Cluster Deployment: IA01NX100-PE (OSB/Retail) | Kilo | 🟡 In Progress [Now] |
| INFRAENG-8287 | IA — IADC02 Rebuild (NetOps) | NetOps | 🟡 In Progress |
| INFRAENG-9187 | IA — IADC02 Rebuild (Kilo) | Kilo | ⚪ Backlog ⚠️ |
| INFRAENG-9091 | IA — IA Rebuilds (DBI) | DBI | ⚪ Backlog ⚠️ |
| INFRAENG-8150 | LA — Cluster Deployment: LA01NX100-PE (OSB/Retail) | Kilo | 🟡 In Progress [Now] |
| INFRAENG-8311 | LA — LADC02 Rebuild (NetOps) | NetOps | 🟡 In Progress |
| INFRAENG-9189 | LA — LADC02 Rebuild (Kilo) | Kilo | ⚪ Backlog ⚠️ |
| INFRAENG-10082 | LA — LA DC Rebuilds (DBI) | DBI | ⚪ Backlog ⚠️ |
| INFRAENG-8142 | NH — Cluster Deployment: NH01NX100-PE (OSB/Retail) | Kilo | 🟡 In Progress [Now] |
| INFRAENG-8299 | NH — NHDC02 Rebuild (NetOps) | NetOps | 🟡 In Progress |
| INFRAENG-9188 | NH — NHDC02 Rebuild (Kilo) | Kilo | ⚪ Backlog ⚠️ |
| INFRAENG-10081 | NH — NH DC Rebuilds (DBI) | DBI | ⚪ Backlog ⚠️ |

**Pattern to watch:** Cluster deployments and NetOps are actively in flight across all 3 jurisdictions. All Kilo rebuild epics and all DBI rebuild epics are sitting in Backlog — these need to be started before the retail workloads can be hosted. Raise with Eric Beisecker and the DBI team if no movement by end of May.

**DC Ops actions (you drive, they execute):**
- [ ] Confirm IADC02, LADC02, NHDC02 capacity and rack assignment with DC Ops (target environment for Nutanix clusters)
- [ ] Confirm physical hardware readiness per jurisdiction
- [ ] Get written sign-off: DC Ops complete

**Net Ops actions (you drive, they execute):**
- [ ] Reference INFRAENG-8287 (IA), INFRAENG-8311 (LA), INFRAENG-8299 (NH) for network requirements already in flight
- [ ] Provide Net Ops with full firewall rules, routing, and traffic paths for retail workload cutover (separate from the Nutanix rebuild scope)
- [ ] Confirm any IP changes → flag to regulatory if applicable
- [ ] Validate connectivity from new environment before Phase 2 starts
- [ ] Get written sign-off: Net Ops complete

**Kilo/Core Infra actions (you drive, they execute):**
- [ ] Confirm INFRAENG-9187 (IA), INFRAENG-9189 (LA), INFRAENG-9188 (NH) Kilo epics have a start date — currently Backlog ⚠️
- [ ] Kilo rebuild completion is a hard prerequisite for retail workload hosting; do not assume cluster deployment alone is sufficient
- [ ] Get written sign-off: Kilo/Core Infra complete

**DBI actions (you drive, they execute):**
- [ ] Confirm INFRAENG-9091 (IA), INFRAENG-10082 (LA), INFRAENG-10081 (NH) DBI epics have a start date — currently Backlog ⚠️
- [ ] DBI (Database Infrastructure) readiness is a prerequisite for Phase 2 DB Ops provisioning
- [ ] Get written sign-off: DBI complete

> **You stay ahead by:** Treating any Net Ops delay as a blocker. Network issues that surface during cutover are the #1 source of rollbacks. Push Net Ops to validate early and completely. The 6 Backlog epics (Kilo + DBI across IA/LA/NH) are your highest infra risk right now — they haven't started.

---

### Phase 2 — Core Infra + DB Ops Provisioning (Parallel)

**Core Infra actions:**
- [ ] Submit provisioning request with architecture diagram attached
- [ ] Define compute spec per jurisdiction (IA, LA, NH may differ)
- [ ] Validate environment readiness (access, connectivity, health checks)
- [ ] Get written sign-off: Core Infra complete

**DB Ops actions:**
- [ ] Submit DB provisioning request
- [ ] Confirm SYSDBA has migration plan in draft
- [ ] Validate DB access, performance baseline, and replication/mirroring setup
- [ ] Confirm rollback approach documented before migration begins
- [ ] Get written sign-off: DB Ops + SYSDBA complete

---

### Phase 3 — PaaS Platform Setup

- [ ] Provide PaaS with full list of Kafka topics, Vault secrets, and k8s resources needed per jurisdiction
- [ ] Confirm all endpoint changes are logged — any change triggers regulatory review
- [ ] Validate Kafka, Vault, Kubernetes resources are deployed and accessible
- [ ] Confirm secrets configured and tested by Retail Engineering
- [ ] Get written sign-off: PaaS complete

---

### Phase 4 — BI Validation

- [ ] Confirm BI has source and target data endpoints documented
- [ ] BI executes reporting pipeline validation in non-prod
- [ ] Confirm data accuracy and pipeline integrity
- [ ] Get written sign-off: BI complete

---

### Phase 5 — Retail Engineering Development + Testing (CRITICAL PATH)

> **This is the most likely phase to blow up your timeline.** Watch it closely.

- [ ] RE has confirmed full application scope (do not proceed without this)
- [ ] RE has all configuration changes documented (endpoints, secrets, Kafka topics)
- [ ] RE testing executed in non-prod for all three jurisdictions
- [ ] All critical flows validated: bet placement, transactions, event streams
- [ ] All defects resolved or formally acknowledged
- [ ] Test evidence provided (logs, screenshots, Jira updates)
- [ ] Get written sign-off: Retail Eng testing complete

**Scope creep watchpoints for RE:**
- New configuration requests after Phase 0 → evaluate impact to timeline before accepting
- "One more thing" endpoint changes → require a RAID log entry and regulatory check
- Testing blockers that require infra changes → escalate immediately, don't let them queue silently

---

### Phase 6 — Release Management Readiness

**RCM ticket must include (you are accountable for completeness):**
- [ ] Planned release date
- [ ] Change level
- [ ] Impacted products and jurisdictions (IA, LA, NH each listed)
- [ ] Downtime confirmation (duration, window)
- [ ] CAB approval obtained
- [ ] Regulatory approval per jurisdiction (IA, LA, NH — confirm each separately)
- [ ] All linked Jira tickets attached
- [ ] Rollback plan documented and reviewed
- [ ] Cutover plan attached
- [ ] Testing plan with evidence attached
- [ ] Communication plan attached
- [ ] Go/No-Go checklist attached

> **Regulatory note:** IA, LA, and NH are separate jurisdictions. Each may have different approval requirements and timelines. **Start regulatory engagement in Phase 0**, not here. If you wait until Phase 6 you will miss your cutover date.

---

### Phase 7 — Cutover Execution

**Pre-cutover (T-20 minutes):**
- [ ] Maintenance notification sent to all stakeholders
- [ ] All team leads on bridge call
- [ ] Final Go/No-Go confirmation from each team lead (verbal + Jira comment)

**Go/No-Go gate — all must be true:**
- [ ] All testing completed with evidence
- [ ] All teams signed off (DC Ops, Net Ops, Core Infra, DB Ops, SYSDBA, PaaS, BI, RE, RM, Regulatory)
- [ ] Backup and migration readiness confirmed
- [ ] Rollback plan validated and owner named
- [ ] No critical open issues (P1/P2 blockers = No-Go)
- [ ] RCM approved

**Execution sequence (your run-of-show):**

| Step | Action | Owner | Confirm |
|---|---|---|---|
| 1 | Enable maintenance mode | RE | [ ] |
| 2 | Enable UM pages | RE | [ ] |
| 3 | Enable PagerDuty maintenance window | You / RM | [ ] |
| 4 | Stop retail services | RE | [ ] |
| 5 | Pause platform dependencies if needed | PaaS | [ ] |
| 6 | Execute DB migration (dump/restore) | SYSDBA | [ ] |
| 7 | Validate migration completion | SYSDBA / DB Ops | [ ] |
| 8 | Redirect applications to new environment | RE | [ ] |
| 9 | Scale down old / scale up new | Core Infra | [ ] |
| 10 | Post-switch app validation | RE | [ ] |
| 11 | Validate critical flows (betting, transactions) | RE | [ ] |
| 12 | Confirm Kafka/event streams | PaaS | [ ] |
| 13 | Restart applications | RE | [ ] |
| 14 | Validate onsite (casino/property if required) | RE / on-site | [ ] |
| 15 | Remove maintenance pages | RE | [ ] |
| 16 | Disable PagerDuty maintenance | You / RM | [ ] |
| 17 | Send completion communication | You / RM | [ ] |

**Rollback triggers (pre-defined, non-negotiable):**
- DB migration validation fails → immediate rollback
- Critical flow (bet placement) fails post-switch → immediate rollback
- Rollback decision must be made within the maintenance window — no extensions without CAB
- Rollback owner: **SYSDBA** (DB), **RE** (app), **Core Infra** (env)

**Rollback actions:**
1. Revert endpoints to original environment (RE)
2. Restart legacy services (RE)
3. Restore DB mirroring/data flow (SYSDBA)
4. Scale down new environment (Core Infra)
5. Remove maintenance mode (RE)
6. Send rollback communication (You)

---

### Phase 8 — Post-Cutover Validation

All must pass before calling cutover complete:
- [ ] Retail product availability confirmed (all three jurisdictions: IA, LA, NH)
- [ ] Transaction flows operational (bet placement, payouts)
- [ ] Application health checks green
- [ ] Database connectivity confirmed
- [ ] Platform services healthy (Kafka, Vault, k8s)
- [ ] Network connectivity confirmed
- [ ] Reporting pipelines validated by BI
- [ ] No data loss or event disruption confirmed

---

### Phase 9 — Follow-up & SFTP (Separate Workstream)

- [ ] SFTP changes tracked separately in Jira — do not block cutover on SFTP
- [ ] SFTP endpoint changes confirmed with regulatory if applicable
- [ ] IP whitelisting updated
- [ ] User migration and access reset completed

---

### Phase 10 — Decommissioning

- [ ] Confirm all post-cutover validation passed
- [ ] DC Ops ticket: decommission legacy hardware for IA, LA, NH
- [ ] Confirm decommission completed and hardware released
- [ ] Update architecture diagrams to reflect new state
- [ ] Close all RAID log items

---

### Phase 11 — Closure & Retrospective

- [ ] System stable for agreed stabilization period (recommend: 1 week minimum)
- [ ] All follow-up Jira tickets tracked and assigned
- [ ] Decommission confirmed complete
- [ ] Retrospective conducted with all teams
- [ ] Lessons learned documented back into Confluence playbook

---

## 4. RAID Log Template

| ID | Type | Description | Probability | Impact | Mitigation | Owner | Status |
|---|---|---|---|---|---|---|---|
| R01 | Risk | Retail Eng bandwidth insufficient for 3 concurrent rebuilds | High | Critical | Confirm capacity in Phase 0; escalate to PM if < 100% allocated | You | Open |
| R02 | Risk | Regulatory delays for IA, LA, or NH | High | High | Engage regulatory in Phase 0; track each jurisdiction separately | You / RM | Open |
| R03 | Risk | DB migration failure during cutover window | Medium | Critical | SYSDBA to validate rollback approach before cutover; dry-run if feasible | SYSDBA | Open |
| R04 | Risk | Endpoint changes trigger unexpected regulatory review | Medium | High | Log all endpoint changes; require regulatory check before finalizing | You / Reg | Open |
| R05 | Risk | SFTP changes block cutover | Medium | Medium | Separate workstream; SFTP not on Day 1 critical path | You | Open |
| R06 | Risk | Net Ops delays block infra readiness | Low-Medium | High | Engage Net Ops in Phase 1 immediately; validate early | Net Ops | Open |
| R07 | Risk | Infra saga completion (INFRAENG-6912 IA, INFRAENG-6909 NH) already past due as of May 2026 — retail consolidation Phase 1 gated on these closing | High | Critical | Confirm revised completion dates from Eric Beisecker; add to weekly sync agenda | You / Eric Beisecker | Open |
| R08 | Risk | 6 Backlog epics (Kilo + DBI for IA, LA, NH) have not started — INFRAENG-9187, 9188, 9189, 9091, 10081, 10082 | High | High | Escalate to Eric Beisecker; require start date commitment before end of May | You / Eric Beisecker | Open |
| R09 | Risk | IPS-424 (NH retail workload migration to NHDC02) still in Discovery — retail testing cannot proceed until this is in Delivery | Medium | High | Track IPS-424 status weekly; flag to RE team that target environment may not be ready | You / PaaS | Open |
| A01 | Assumption | Each jurisdiction (IA, LA, NH) has independent regulatory approval path | — | — | Confirm with Regulatory in Phase 0 | Regulatory | Open |
| A02 | Assumption | Cutover date is aligned with no major betting events | — | — | Confirm with business before finalizing | PM | Open |
| A03 | Assumption | Cluster deployment epics (INFRAENG-8135, 8150, 8142) completing in May/June is sufficient for retail workload hosting — Kilo rebuild epics must also complete | — | — | Confirm with Eric Beisecker that Kilo epics are prerequisite, not optional | You / Eric Beisecker | Open |
| I01 | Issue | DBI rebuild epics for all 3 jurisdictions (INFRAENG-9091 IA, INFRAENG-10081 NH, INFRAENG-10082 LA) are in Backlog with no assignee or start date | — | — | Raise with DBI team lead — must be scheduled before Phase 2 DB Ops work begins | You | Open |
| D01 | Decision | SFTP treated as separate post-cutover workstream | — | — | Agreed in kickoff | You | Closed |
| D02 | Decision | Eric Beisecker confirmed as infra lead/assignee across all 4 sagas (IA, LA, NH, Foxwoods) | — | — | Confirmed via Jira saga scan | You | Closed |

---

## 5. Deliverables Tracker

| Deliverable | Owner | Due | Status |
|---|---|---|---|
| Project plan (Jira epic) | You | Phase 0 | [ ] |
| RAID log | You | Phase 0 | [ ] |
| Architecture diagrams (IA, LA, NH) | Core Infra | Phase 1 | [ ] |
| RCM ticket | You / RM | Phase 6 | [ ] |
| Cutover plan | You | Phase 6 | [ ] |
| Testing documentation (per jurisdiction) | RE | Phase 5 | [ ] |
| Rollback plan | SYSDBA / RE | Phase 6 | [ ] |
| Validation checklist (completed) | All | Phase 8 | [ ] |
| Status reports | You | Weekly | [ ] |
| Retrospective doc | You | Phase 11 | [ ] |
| Eric Beisecker revised completion dates for IA + NH infra sagas | You | This week | [ ] |
| Start date confirmation for 6 Backlog epics (Kilo + DBI, all jurisdictions) | You | End of May | [ ] |
| IPS-424 status confirmation (NH retail workload migration) | You | Phase 0 | [ ] |

---

## 5a. Infra Epic Reference Map

All epics currently live under the DC Hardware Replacement Programme (INFRAENG-11189). Use this as your weekly health-check reference — if any of these slip, the retail consolidation timeline moves with them.

### Iowa (Saga: INFRAENG-6912) — ⚠️ Past Due

| Epic | Summary | Team | Status |
|---|---|---|---|
| [INFRAENG-8135](https://draftkingsofficial.atlassian.net/browse/INFRAENG-8135) | Cluster Deployment: IA01NX100-PE — OSB/Retail | Kilo | 🟡 In Progress [Now] |
| [INFRAENG-8287](https://draftkingsofficial.atlassian.net/browse/INFRAENG-8287) | IADC02 — Iowa Rebuild (NetOps) | NetOps | 🟡 In Progress |
| [INFRAENG-9187](https://draftkingsofficial.atlassian.net/browse/INFRAENG-9187) | IADC02 — Iowa Rebuild (Kilo) | Kilo | ⚪ Backlog ⚠️ |
| [INFRAENG-9091](https://draftkingsofficial.atlassian.net/browse/INFRAENG-9091) | IA Rebuilds (DBI) | DBI | ⚪ Backlog ⚠️ |

### Louisiana (Saga: INFRAENG-6918) — On Track

| Epic | Summary | Team | Status |
|---|---|---|---|
| [INFRAENG-8150](https://draftkingsofficial.atlassian.net/browse/INFRAENG-8150) | Cluster Deployment: LA01NX100-PE — OSB/Retail | Kilo | 🟡 In Progress [Now] |
| [INFRAENG-8311](https://draftkingsofficial.atlassian.net/browse/INFRAENG-8311) | LADC02 — Louisiana Rebuild (NetOps) | NetOps | 🟡 In Progress |
| [INFRAENG-9189](https://draftkingsofficial.atlassian.net/browse/INFRAENG-9189) | LADC02 — Rebuild (Kilo) | Kilo | ⚪ Backlog ⚠️ |
| [INFRAENG-10082](https://draftkingsofficial.atlassian.net/browse/INFRAENG-10082) | LA DC Rebuilds (DBI) | DBI | ⚪ Backlog ⚠️ |

### New Hampshire (Saga: INFRAENG-6909) — ⚠️ Past Due

| Epic | Summary | Team | Status |
|---|---|---|---|
| [INFRAENG-8142](https://draftkingsofficial.atlassian.net/browse/INFRAENG-8142) | Cluster Deployment: NH01NX100-PE — OSB/Retail | Kilo | 🟡 In Progress [Now] |
| [INFRAENG-8299](https://draftkingsofficial.atlassian.net/browse/INFRAENG-8299) | NHDC02 — New Hampshire Rebuild (NetOps) | NetOps | 🟡 In Progress |
| [INFRAENG-9188](https://draftkingsofficial.atlassian.net/browse/INFRAENG-9188) | NHDC02 — Rebuild (Kilo) | Kilo | ⚪ Backlog ⚠️ |
| [INFRAENG-10081](https://draftkingsofficial.atlassian.net/browse/INFRAENG-10081) | NH DC Rebuilds (DBI) | DBI | ⚪ Backlog ⚠️ |

### Foxwoods (Saga: INFRAENG-519) — On Track — LaDarius Owens / Eric Beisecker

| Epic | Summary | Team | Status |
|---|---|---|---|
| [INFRAENG-1213](https://draftkingsofficial.atlassian.net/browse/INFRAENG-1213) | DBI — FX Rebuild Data Center | DBI | ⚪ Backlog (due Jun 21) ⚠️ |
| [INFRAENG-7543](https://draftkingsofficial.atlassian.net/browse/INFRAENG-7543) | FXDC02 — Foxwoods Rebuild (NetOps) | NetOps | 🟡 In Progress |
| [INFRAENG-8100](https://draftkingsofficial.atlassian.net/browse/INFRAENG-8100) | Cluster Deployment: CT02NX100-PE — OSB/Retail | Kilo | 🟡 In Progress [Now] |
| [INFRAENG-12638](https://draftkingsofficial.atlassian.net/browse/INFRAENG-12638) | FXDC02 — Rebuild (Kilo) | Kilo | ⚪ Backlog ⚠️ |

> **Cross-cutting observation:** The pattern is identical across all 4 jurisdictions — cluster deployments and NetOps are running, Kilo rebuilds and DBI work are all in Backlog. This is a systemic gap, not a per-jurisdiction anomaly. Raise it with Eric Beisecker as a single cross-cutting issue, not four separate ones.

---

## 6. Anti-Patterns / Scope Creep Triggers to Watch

These are the most common ways retail consolidations go sideways. Watch for all of them:

1. **RE scope expands after Phase 0** — new apps, new services, new topics added mid-project. Each addition resets the testing timeline. Push back hard; require PM approval for any scope change.
2. **Endpoint changes made without regulatory flag** — PaaS or RE makes a config change; no one tells you. Build a standing agenda item in every sync: "any endpoint changes this week?"
3. **SFTP gets added to Day 1 scope** — SFTP is a separate workstream. It is not on the critical path unless explicitly decided otherwise.
4. **Regulatory approval assumed, not confirmed** — IA, LA, NH each have their own compliance timelines. Don't let RM tell you "we'll get approval" without a date.
5. **Go/No-Go gets pressured by business** — If any Go/No-Go criteria is unmet, the answer is No-Go. Document your decision in Jira.
6. **Post-cutover validation is skipped or rushed** — All 8 validation items must be explicitly confirmed. Get them in writing from each team.
7. **Decommission is deprioritized** — Old hardware left running creates shadow infrastructure. Set a firm decommission date at kickoff.

---

## 7. Stakeholder Communication Cadence

| Communication | Audience | Frequency | You send |
|---|---|---|---|
| Status update | PM, stakeholders | Weekly | RAID summary, milestone status, blockers |
| Team sync | All workstream leads | Weekly or bi-weekly | Agenda: blockers, scope changes, timeline review |
| Cutover readiness briefing | All teams | T-1 week | Go/No-Go criteria status per team |
| Pre-cutover bridge | All teams | Day of | Run-of-show, final Go/No-Go |
| Completion communication | All stakeholders | Post-cutover | "Cutover complete, validation status, any follow-ups" |
| Rollback communication (if needed) | All stakeholders | Immediately | "Rollback initiated, reason, next steps" |
