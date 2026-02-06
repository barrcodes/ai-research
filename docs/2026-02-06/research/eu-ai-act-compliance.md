# EU AI Act Article 10 Compliance: Versioned Training Data

| | |
|---|---|
| **Source** | r/MachineLearning & DoltHub |
| **URL** | [www.reddit.com/r/MachineLearning/comments/1qwp484](https://www.reddit.com/r/MachineLearning/comments/1qwp484/r_what_data_trained_this_model_shouldnt_require/) |
| **Researched** | 2026-02-06 |

## Overview

EU AI Act Article 10 mandates immutable audit trails for training data in high-risk systems (law enforcement, medical, critical infrastructure). The compliance pattern emerging in practice leverages Git-style version control applied to databases: tag data snapshots at model training boundaries, enabling deterministic proof of what data trained which model version. This shifts data governance from archaeological reconstruction to operational transparency.

## Key Points

- **Article 10 Core Requirement**: High-risk AI systems must demonstrate bias-free training data, document all data preparation operations (labeling, cleaning), and maintain queryable audit trails of data origin and provenance.

- **Versioned Data Pattern**: Each training data modification becomes a commit with author/timestamp metadata; model training creates a named tag (e.g., `model-2026-01-28`) that maps immutably to a specific dataset snapshot. This eliminates the "we don't know exactly what trained this" problem endemic to many deployments.

- **Compliance-Ready Tooling**: Version-controlled databases (e.g., Dolt) enable cell-level diffs, time-travel queries, and system tables (`dolt_log`, `dolt_diff_*`) that automatically surface exactly which dataset version was used, when biased records entered, who introduced them, and commit hashes for full traceability.

- **Article 14 Integration**: Human oversight requirements are satisfied through pull-request workflows where AI-proposed changes appear as branch diffs, awaiting human review and approval before merging to production. Rollback is Git-native (`revert`, `reset`, `checkout`).

## Technical Details

### High-Risk System Scope

The EU AI Act applies to systems in: biometrics, critical infrastructure, education, employment, government benefits, credit, health insurance, law enforcement, migration, and justice. General-purpose models (GPT-scale) face systemic risk regulations. Medium-risk systems below the threshold still require documentation and transparency.

### Dolt Pattern for Law Enforcement (Flock Safety Case Study)

Flock Safety (vehicle/plate recognition for law enforcement) uses Dolt to store all training images. A flag column (`has_person`) tracks whether humans appear in images—policy is to exclude these.

**Compliance scenario flow:**
1. Compliance asks: "What data trained this model?"
   - Answer: `model-2026-01-28` tag; run SQL queries on that snapshot.
2. Compliance asks: "Prove no biased data?"
   - Answer: `SELECT COUNT(*) FROM training_images AS OF 'model-2026-01-28' WHERE has_person=1` returns 0.
3. Post-deployment, a biased record surfaces in quality control.
   - Dolt diff + log join identifies: image added by labeler-smith@flock.com on 2026-02-03 (after training), commit hash a3f9c2d.
   - Action: Revert the commit, document incident, retrain labeler.

This is deterministic audit-trail-based governance, not forensic archaeology.

### Network Infrastructure Pattern (Nautobot/ISP Case Study)

Critical infrastructure ISPs use AI for network configuration management. Article 14 requires human oversight of all AI-proposed changes.

**Implementation:**
- AI detects network problem, proposes config fixes on a feature branch in Dolt.
- Web UI (Nautobot) renders the diff visually to the human operator.
- Operator reviews, then merges (approve), reverts (reject), or edits before merge.
- Production database (`main` branch) only reflects human-approved changes.

This operationalizes the "decide not to use the system" and "override the output" mandates.

## Implications

**For practitioners:**

1. **Architecture Decision**: Versioned databases are no longer optional for high-risk AI systems in EU-regulated markets. This is a foundational compliance requirement, not a nice-to-have.

2. **Data Governance Shift**: Compliance transitions from "audit after deployment" to "audit-trail-native data pipelines." Every data pipeline change must be tagged, and model training must record which tag it used.

3. **Tool Selection**: Standard data lakes (S3, data warehouses) are insufficient. You need queryable, branched, diff-enabled storage. Dolt is the marketed example, but the principle applies to any version-controlled data system.

4. **Integration with ML Ops**: Model registry/lineage systems (DVC, MLflow) must integrate with versioned data: model artifact references a data tag, not just a timestamp. This creates an immutable dependency graph.

5. **Human-in-the-Loop Cost**: Article 14's human oversight is expensive operationally. PR-style workflows (branch → review → merge) are the practical implementation, but this requires refactoring application architecture to use Dolt/Git-like database semantics rather than traditional transactional DBs.

6. **Medical/MDR Context**: One Reddit commenter (medical records specialist) notes that EU-MDR (medical device regulation) already mandates reproducibility and traceability—EU AI Act enforcement will converge these requirements, likely forcing all medical AI systems onto versioned data infrastructure.

7. **Post-Deployment Liability**: The ability to prove "this model trained on this exact dataset, which we certified bias-free" is a legal shield when model failures are challenged. The burden of proof is on the system builder, not the regulator.

## Sources

- [Reddit discussion: "What data trained this model?" shouldn't require archeology](https://www.reddit.com/r/MachineLearning/comments/1qwp484/r_what_data_trained_this_model_shouldnt_require/) - DoltHub announcement of EU AI Act compliance patterns
- [DoltHub Blog: EU AI Act Compliance](https://www.dolthub.com/blog/2026-02-02-eu-ai-act/) - Detailed technical breakdown of Article 10 (data governance) and Article 14 (human oversight) with case studies
- [EU AI Act Full Text](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32024R1689) - Legal reference (180 recitals, 113 articles, enforcement August 2026)
- [DoltHub Blog: Dolt + Flock Safety](https://www.dolthub.com/blog/2024-03-07-dolt-flock/) - Law enforcement high-risk AI system use case
