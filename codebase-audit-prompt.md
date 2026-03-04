# Codebase Audit Prompt Template

> **Version:** 1.0
> **Purpose:** Reusable prompt for conducting comprehensive technical audits during project handovers. Supports single-repo and multi-repo codebases. Designed for multi-agent execution.

---

## How to Use This Template

1. Copy the **Audit Prompt** section below into your LLM or agent orchestrator.
2. Replace `{{PLACEHOLDER}}` values with your project-specific details.
3. Choose your preferred **Agent Workflow Pattern** (parallel or sequential).
4. Attach or point to the codebase(s) under audit.

---

## Audit Prompt

```
You are a panel of senior technical auditors conducting a formal codebase audit for a project handover. The receiving team has requested this audit to assess the health, security, and maintainability of the codebase before they assume ownership.

===========================
PROJECT CONTEXT
===========================

Project Name: {{PROJECT_NAME}}
Handover From: {{SENDER_TEAM_OR_PERSON}}
Handover To: {{RECEIVER_TEAM_OR_PERSON}}
Repository Location(s): {{REPO_PATHS_OR_URLS}}
Primary Languages/Frameworks: {{LANGUAGES_AND_FRAMEWORKS}}
Known Constraints or Context: {{ANY_KNOWN_ISSUES_TECH_DEBT_OR_NOTES}}

===========================
AUDIT SCOPE
===========================

Conduct a comprehensive technical audit across the following 7 dimensions. For each dimension, assign a score using the rubric provided, identify specific findings, and classify them by severity.

-------------------------------------------------------
DIMENSION 1: SECURITY & COMPLIANCE
-------------------------------------------------------

Audit for:
- Hardcoded secrets (API keys, credentials, tokens, private keys) in source code, config files, and commit history
- Insecure dependencies (known CVEs, outdated packages with security patches available)
- Injection vulnerabilities (SQL injection, XSS, command injection, path traversal)
- Authentication and authorization flaws (broken access control, missing auth on endpoints, weak session management)
- Data exposure risks (sensitive data in logs, unencrypted storage, overly permissive CORS)
- OWASP Top 10 alignment check
- Secrets management approach (env vars, vault integration, .env files in .gitignore)

Scoring Rubric:
  5 - Excellent: No secrets in code, all dependencies patched, robust auth/authz, follows OWASP guidelines
  4 - Good: Minor issues only (e.g., one outdated but non-critical dependency), strong overall posture
  3 - Acceptable: Some medium-risk issues (e.g., a few missing input validations), but no critical exposures
  2 - Poor: Multiple significant issues (e.g., hardcoded secrets in non-production code, several unpatched CVEs)
  1 - Critical: Hardcoded production secrets, known exploitable vulnerabilities, or broken authentication

-------------------------------------------------------
DIMENSION 2: CODE QUALITY & ARCHITECTURE
-------------------------------------------------------

Audit for:
- Modularity and separation of concerns (clean boundaries between layers/services)
- Adherence to DRY, SOLID, and KISS principles
- Consistency of design patterns within and across repositories
- Code complexity (deeply nested logic, functions exceeding ~50 lines, god classes/files)
- Error handling patterns (consistent try/catch, error propagation, graceful degradation)
- Naming conventions (variables, functions, files, modules — consistent and descriptive)
- Dead code and unused imports
- Type safety (TypeScript strictness, Python type hints, etc.)

Scoring Rubric:
  5 - Excellent: Clean architecture, consistent patterns, low complexity, strong type safety
  4 - Good: Minor inconsistencies, occasional complexity spikes, mostly well-structured
  3 - Acceptable: Some architectural drift, moderate tech debt, but navigable by a new developer
  2 - Poor: Significant spaghetti code, inconsistent patterns, high cognitive load to understand
  1 - Critical: No discernible architecture, massive files, pervasive copy-paste duplication

-------------------------------------------------------
DIMENSION 3: DOCUMENTATION
-------------------------------------------------------

Audit for:
- README quality (setup instructions, architecture overview, contribution guidelines)
- Inline code comments (explains "why" not just "what", present at complex logic points)
- API documentation (endpoints documented, request/response schemas, error codes)
- Architecture decision records (ADRs) or design docs
- Onboarding ease — could a new developer get the project running within 1 hour?
- Changelog or release notes
- Environment setup documentation (required tools, versions, environment variables)

Scoring Rubric:
  5 - Excellent: Comprehensive README, API docs, ADRs, and a new dev can onboard in <1 hour
  4 - Good: Solid README and inline comments, minor gaps in API docs or ADRs
  3 - Acceptable: Basic README exists, some inline comments, but gaps in setup or API docs
  2 - Poor: Minimal or outdated README, sparse comments, new dev would struggle significantly
  1 - Critical: No meaningful documentation, setup is tribal knowledge only

-------------------------------------------------------
DIMENSION 4: INFRASTRUCTURE & ENVIRONMENT
-------------------------------------------------------

Audit for:
- Dockerfiles and docker-compose (multi-stage builds, minimal base images, no secrets in layers)
- CI/CD pipelines (build, test, lint, security scan stages present and functional)
- Environment variable management (no defaults for secrets, validation at startup)
- Infrastructure as Code (Terraform, CloudFormation, Pulumi — if applicable)
- Deployment strategy (blue-green, canary, rollback capability)
- Logging and monitoring setup (structured logging, alerting, observability)
- Branch strategy and release process

Scoring Rubric:
  5 - Excellent: Fully automated CI/CD, IaC, containerized, with monitoring and rollback capability
  4 - Good: CI/CD exists with most stages, Dockerized, minor gaps in monitoring or IaC
  3 - Acceptable: Basic CI/CD, Docker present but not optimized, manual steps in deployment
  2 - Poor: Minimal automation, inconsistent environments, manual deployment process
  1 - Critical: No CI/CD, no containerization, environment setup is entirely manual and undocumented

-------------------------------------------------------
DIMENSION 5: TESTING COVERAGE
-------------------------------------------------------

Audit for:
- Unit test presence and coverage percentage (if measurable)
- Integration test presence (API tests, database tests, service interaction tests)
- End-to-end (E2E) test presence (Cypress, Playwright, Selenium, etc.)
- Test quality (meaningful assertions vs. superficial "does it not crash" tests)
- Test organization and naming conventions
- Mocking strategy (appropriate use of mocks/stubs, not over-mocked)
- CI integration (tests run on every PR/push)

Scoring Rubric:
  5 - Excellent: >80% unit coverage, integration + E2E tests present, tests run in CI, high assertion quality
  4 - Good: >60% unit coverage, some integration tests, tests in CI, mostly meaningful assertions
  3 - Acceptable: >40% unit coverage, basic tests exist, may not all run in CI
  2 - Poor: <40% coverage, tests exist but many are broken or superficial
  1 - Critical: No tests, or tests exist but are entirely non-functional

-------------------------------------------------------
DIMENSION 6: PERFORMANCE & SCALABILITY
-------------------------------------------------------

Audit for:
- Database query efficiency (N+1 queries, missing indexes, unoptimized joins)
- Caching strategy (appropriate use of Redis/Memcached, cache invalidation logic)
- API response patterns (pagination, rate limiting, payload sizes)
- Async processing (background jobs for heavy operations, queue usage)
- Resource management (connection pooling, memory leaks, file handle cleanup)
- Horizontal scalability readiness (stateless services, shared-nothing architecture)
- Frontend performance (bundle size, lazy loading, image optimization — if applicable)

Scoring Rubric:
  5 - Excellent: Optimized queries, caching in place, pagination, async processing, scalability-ready
  4 - Good: Mostly optimized, minor N+1 or missing cache cases, pagination present
  3 - Acceptable: Some performance concerns but functional at current scale
  2 - Poor: Multiple N+1 queries, no caching, large unoptimized payloads, will not scale
  1 - Critical: Severe performance issues that affect current functionality

-------------------------------------------------------
DIMENSION 7: DEPENDENCY HEALTH
-------------------------------------------------------

Audit for:
- Outdated packages (major versions behind, unmaintained packages)
- Known vulnerabilities (npm audit, pip-audit, Snyk, Dependabot findings)
- License compliance (copyleft licenses in proprietary projects, license conflicts)
- Dependency count and bloat (unnecessary dependencies, overlapping libraries)
- Lock file hygiene (package-lock.json, yarn.lock, poetry.lock present and committed)
- Supply chain risk (typosquatting risk, use of unverified packages)
- Version pinning strategy (exact versions vs. ranges)

Scoring Rubric:
  5 - Excellent: All deps current, no known CVEs, licenses reviewed, lock files committed, minimal bloat
  4 - Good: Minor outdated non-critical deps, no high/critical CVEs, lock files present
  3 - Acceptable: Some outdated deps, low-severity CVEs, lock files present but not always updated
  2 - Poor: Multiple outdated deps with medium CVEs, license compliance not reviewed
  1 - Critical: Known high/critical CVEs unpatched, no lock files, potential license violations

===========================
OUTPUT FORMAT
===========================

Structure your audit report exactly as follows:

---

### 1. EXECUTIVE SUMMARY

Write a 3-5 paragraph overview covering:
- Overall health assessment of the codebase(s)
- Top 3 risks the receiving team should be aware of immediately
- Top 3 strengths that are well-executed
- A one-sentence handover readiness verdict (e.g., "This codebase is ready for handover with minor remediation" or "Handover should be delayed until critical security issues are resolved")

---

### 2. SCORECARD

Present a table with the following structure. One row per repository (or one row if single-repo).

| Repository | Security | Code Quality | Documentation | Infrastructure | Testing | Performance | Dependencies | Overall |
|------------|----------|-------------|---------------|----------------|---------|-------------|-------------|---------|
| repo-name  | X/5      | X/5         | X/5           | X/5            | X/5     | X/5         | X/5         | X.X/5   |

Overall = weighted average using these weights:
- Security: 25%
- Code Quality: 20%
- Documentation: 15%
- Infrastructure: 15%
- Testing: 10%
- Performance: 10%
- Dependencies: 5%

Below the table, include a **Risk Classification** for each repository:
- 4.0–5.0: LOW RISK — Ready for handover
- 3.0–3.9: MEDIUM RISK — Handover acceptable with remediation plan
- 2.0–2.9: HIGH RISK — Handover not recommended without significant remediation
- 1.0–1.9: CRITICAL RISK — Handover should be blocked

---

### 3. DETAILED FINDINGS

For each repository, list findings grouped by dimension. Each finding must follow this format:

**[SEVERITY] Finding Title**
- Dimension: (e.g., Security & Compliance)
- Location: (file path, line number, or component name)
- Description: (what the issue is)
- Impact: (what could go wrong)
- Recommendation: (how to fix it)
- Effort Estimate: (Low / Medium / High)

Severity levels:
- 🔴 CRITICAL — Must be fixed before handover. Represents an active security risk or a system-breaking issue.
- 🟠 MAJOR — Should be fixed within the first sprint post-handover. Significant tech debt or risk.
- 🟡 MINOR — Should be tracked and addressed over time. Low-risk improvements.
- ⚪ INFO — Observations and best-practice suggestions. No immediate action required.

---

### 4. ACTION PLAN

Provide a prioritized remediation table:

| Priority | Finding | Severity | Effort | Owner (Suggested) | Target Timeline |
|----------|---------|----------|--------|--------------------|-----------------|
| 1        | ...     | CRITICAL | Low    | Sender Team        | Before handover |
| 2        | ...     | CRITICAL | Medium | Sender Team        | Before handover |
| 3        | ...     | MAJOR    | Low    | Receiver Team      | Sprint 1        |
| ...      | ...     | ...      | ...    | ...                | ...             |

Group by:
- **Pre-Handover (Sender Team Responsibility):** All CRITICAL items + quick MAJOR wins
- **Sprint 1 Post-Handover (Receiver Team):** Remaining MAJOR items
- **Backlog (Receiver Team):** MINOR and INFO items

---

### 5. HANDOVER CHECKLIST

Generate a checklist for the receiving team:

- [ ] All CRITICAL findings remediated
- [ ] Secrets rotated and moved to vault/env management
- [ ] CI/CD pipeline verified and passing
- [ ] Documentation reviewed and gaps noted
- [ ] Local development environment setup tested by a non-author
- [ ] Access credentials and service accounts transferred
- [ ] Monitoring and alerting ownership transferred
- [ ] Knowledge transfer sessions scheduled for complex components
- [ ] Runbooks for incident response reviewed
- [ ] Third-party service accounts and billing ownership transferred

===========================
CONSTRAINTS
===========================

1. Maintain a formal, objective tone throughout. This is a professional audit document.
2. If a repository lacks sufficient data for a specific check, score it as "Inconclusive" and note what data is missing.
3. Do not make assumptions about intent — audit what exists in the code, not what was planned.
4. Provide specific file paths and line numbers wherever possible.
5. If multiple repositories exist, analyze each independently AND note cross-repo concerns (shared dependencies, inconsistent patterns, etc.).
6. Prioritize findings by real-world impact, not theoretical risk.
```

---

## Multi-Agent Workflow Patterns

Below are two patterns for distributing the audit across multiple agents. Choose based on your tooling and time constraints.

---

### Pattern A: Parallel Specialists

Each agent owns one or two audit dimensions and runs independently. A final "Synthesizer" agent merges results into the unified report.

```
┌─────────────────────────────────────────────────────────┐
│                    ORCHESTRATOR                         │
│         (distributes repos + collects results)          │
└──────┬──────┬──────┬──────┬──────┬──────┬──────┬───────┘
       │      │      │      │      │      │      │
       ▼      ▼      ▼      ▼      ▼      ▼      ▼
   ┌──────┐┌──────┐┌──────┐┌──────┐┌──────┐┌──────┐┌──────┐
   │Agent ││Agent ││Agent ││Agent ││Agent ││Agent ││Agent │
   │  1   ││  2   ││  3   ││  4   ││  5   ││  6   ││  7   │
   │Secur-││Code  ││Docs  ││Infra ││Test- ││Perf  ││Deps  │
   │ity   ││Qual. ││      ││      ││ing   ││      ││      │
   └──────┘└──────┘└──────┘└──────┘└──────┘└──────┘└──────┘
       │      │      │      │      │      │      │
       └──────┴──────┴──────┴──────┴──────┴──────┘
                          │
                          ▼
                   ┌─────────────┐
                   │ SYNTHESIZER │
                   │   AGENT     │
                   │ (merges +   │
                   │  scores +   │
                   │  report)    │
                   └─────────────┘
```

**Agent Prompts for Parallel Pattern:**

Each specialist agent receives:
1. The full **Project Context** block
2. Only their assigned **Dimension** section (audit checklist + rubric)
3. Instructions to output findings in the standard format (severity, location, description, impact, recommendation, effort)

The **Synthesizer Agent** receives:
1. All specialist outputs
2. The full **Output Format** section
3. Instructions to compile the scorecard, executive summary, action plan, and handover checklist

**Pros:** Fastest execution (all agents run simultaneously), deep focus per domain.
**Cons:** Cross-cutting concerns (e.g., a security issue that's also an architecture issue) may be flagged by multiple agents. Synthesizer must deduplicate.

---

### Pattern B: Sequential Pipeline

Agents run in stages. Each stage builds on the previous one's output.

```
┌──────────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│   STAGE 1    │───▶│   STAGE 2    │───▶│   STAGE 3    │───▶│   STAGE 4    │
│  DISCOVERY   │    │ DEEP ANALYSIS│    │   SCORING    │    │   REPORT     │
│              │    │              │    │              │    │              │
│ - Map repos  │    │ - Run all 7  │    │ - Apply      │    │ - Executive  │
│ - Identify   │    │   dimension  │    │   rubrics    │    │   summary    │
│   languages  │    │   audits     │    │ - Calculate  │    │ - Compile    │
│ - Catalog    │    │ - Flag all   │    │   weighted   │    │   full       │
│   structure  │    │   findings   │    │   scores     │    │   report     │
│ - Note entry │    │ - Classify   │    │ - Rank       │    │ - Action     │
│   points     │    │   severity   │    │   findings   │    │   plan       │
└──────────────┘    └──────────────┘    └──────────────┘    └──────────────┘
```

**Stage Details:**

**Stage 1 — Discovery Agent:**
- Maps all repositories, file structures, languages, and frameworks
- Identifies entry points (main files, index files, route definitions)
- Creates a manifest of what exists (Dockerfiles, CI configs, test directories, README files)
- Output: Repository manifest JSON

**Stage 2 — Deep Analysis Agent(s):**
- Receives the manifest from Stage 1
- Runs all 7 dimension audits with full context
- Can be parallelized internally (one sub-agent per dimension)
- Output: Raw findings list with severity classifications

**Stage 3 — Scoring Agent:**
- Receives raw findings from Stage 2
- Applies rubrics to generate scores per dimension per repo
- Calculates weighted overall scores
- Identifies cross-repo patterns and inconsistencies
- Output: Scorecard + ranked findings

**Stage 4 — Report Agent:**
- Receives scorecard + ranked findings from Stage 3
- Compiles the final report in the specified output format
- Generates executive summary, action plan, and handover checklist
- Output: Final audit report

**Pros:** Each stage has full context from previous stages, better cross-cutting analysis, cleaner deduplication.
**Cons:** Slower (sequential), Stage 2 is the bottleneck.

---

### Choosing a Pattern

| Factor | Parallel Specialists | Sequential Pipeline |
|--------|---------------------|---------------------|
| Speed | Faster (concurrent) | Slower (sequential) |
| Depth | Deep per-dimension | Holistic cross-dimension |
| Deduplication | Needs post-merge cleanup | Handled naturally |
| Complexity to orchestrate | Lower per-agent, higher merge | Higher per-stage, cleaner output |
| Best for | Large multi-repo audits with time pressure | Single-repo or when cross-cutting analysis matters |

---

## Customization Guide

### Adjusting Weights

Modify the weighted average in the Scorecard section based on your project's priorities:

- **Security-critical project** (fintech, healthcare): Security 35%, Dependencies 10%, reduce others
- **Early-stage startup handover**: Code Quality 25%, Documentation 20%, reduce Infrastructure
- **Data-intensive project**: Performance 20%, Testing 15%, reduce Documentation

### Adding Custom Dimensions

To add a project-specific dimension (e.g., "Accessibility" or "Localization"):
1. Add a new dimension section following the same format (audit checklist + 5-point rubric)
2. Add a column to the Scorecard table
3. Adjust weights to sum to 100%

### Single-Repo Simplification

For a single repository, remove the "Repository Comparison Table" framing and treat the Scorecard as a single-row summary. All other sections remain the same.

---

## Quick-Start Checklist

Before running the audit, confirm:

- [ ] All repository paths/URLs are accessible to the auditing agents
- [ ] `{{PLACEHOLDER}}` values are filled in
- [ ] Preferred agent workflow pattern is selected
- [ ] Scoring weights are adjusted if needed
- [ ] Receiving team's specific concerns are added to the "Known Constraints" field
- [ ] Output destination (file, document, platform) is decided
