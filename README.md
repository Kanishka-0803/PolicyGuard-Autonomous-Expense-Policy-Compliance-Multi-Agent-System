# PolicyGuard: Autonomous Expense & Policy Compliance Multi-Agent System

Automating corporate expense verification and policy auditing using Retrieval-Augmented Generation (RAG), LangGraph multi-agent orchestration, and deterministic SQLite tool calling — eliminating manual review bottlenecks and policy non-compliance.

---

## Business Context

Manual expense auditing in corporate finance teams is slow, error-prone, and vulnerable to policy violations such as city-tier overspending, unauthorized software claims, and prohibited purchases. Relying on ungrounded LLMs alone introduces hallucination risks when enforcing strict financial thresholds.

**Objective:** Build a stateful, grounded multi-agent compliance auditor that retrieves exact corporate policy rules, evaluates incoming expense claims, executes deterministic database updates, and logs auditable justifications before reimbursement.

---

## Knowledge Base & Evaluation Suite

| Detail | Value |
| :--- | :--- |
| **Policy Domain** | Corporate Expense & Reimbursement Governance |
| **Indexed Policy Rules** | 4 Core Policy Sections (Meals, Travel, SaaS, Escalation) |
| **Benchmark Claims** | 6 End-to-End Edge-Case Scenarios (`CLM-101` to `CLM-106`) |
| **Target Outcomes** | `APPROVED`, `PENDING_MANAGER_REVIEW`, `REJECTED` |

The knowledge base encodes multi-dimensional corporate rules across **city tiers** (Tier-1 vs. Tier-2 meal caps), **employee roles** (Engineering vs. non-engineering SaaS eligibility), **spend thresholds** (cab limits and ₹5,000 high-value escalation), and **strict prohibitions** (zero-tolerance alcohol policy).

---

## Project Workflow

1. **Policy Indexing (RAG Setup)** — Chunked corporate expense policy guidelines and indexed them into a **ChromaDB** vector store using HuggingFace `all-MiniLM-L6-v2` embeddings.
2. **State & Tool Binding** — Designed a relational **SQLite** audit table (`expense_claims`) and bound three deterministic Python `@tool` functions (`approve_expense`, `flag_for_manager_review`, `reject_expense`) to restrict state mutations strictly to validated tool calls.
3. **Multi-Agent Orchestration (LangGraph)** — Built a two-node `StateGraph` separating context retrieval (`policy_rag_agent`) from compliance reasoning and execution (`compliance_auditor_agent`).
4. **Grounded Compliance Reasoning** — Configured `gemini-3.8-flash` (`temperature=0`) to evaluate claim metadata strictly against retrieved policy chunks and generate concise policy citations for every action.
5. **Benchmark Evaluation & Audit Logging** — Executed an automated 6-scenario test suite comparing predicted database states against ground-truth compliance outcomes while recording latency and audit trails.

---

## Benchmark Results

Evaluation across all six compliance test scenarios:

| Claim ID | Category | Role & City | Amount | Expected Status | Agent Decision | Result |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `CLM-101` | Client Meals | Software Developer (Bengaluru) | ₹2,500 | `APPROVED` | `APPROVED` | PASS |
| `CLM-102` | Client Meals | Sales Executive (Kochi) | ₹2,800 | `PENDING_MANAGER_REVIEW` | `PENDING_MANAGER_REVIEW` | PASS |
| `CLM-103` | Client Meals | Product Manager (Mumbai) | ₹1,800 | `REJECTED` | `REJECTED` | PASS |
| `CLM-104` | Software Subscriptions | Engineering Lead (Pune) | ₹2,200 | `APPROVED` | `APPROVED` | PASS |
| `CLM-105` | Software Subscriptions | HR Manager (Delhi) | ₹1,500 | `PENDING_MANAGER_REVIEW` | `PENDING_MANAGER_REVIEW` | PASS |
| `CLM-106` | Domestic Travel | Designer (Hyderabad) | ₹800 | `APPROVED` | `APPROVED` | PASS |

### Final System Performance: 100% Policy-Routing Accuracy (6/6 Passed)
Separating policy retrieval from tool-bound decision execution eliminated rule hallucinations. Every claim was routed to the exact compliance state with a grounded policy citation persisted in the SQLite audit log.

---

## Key Compliance Signals Handled

* **Geographic Tier Enforcement:** Automatically distinguishes Tier-1 city limits (₹3,000) from Tier-2 city limits (₹2,000) for client meals.
* **Strict Prohibition Detection:** Identifies prohibited line items (e.g., alcohol purchases) and triggers immediate rejection regardless of spend amount.
* **Role-Based Access & Spend Governance:** Auto-approves developer tools within budget for Engineering roles while routing non-engineering claims for manual manager verification.

---

## Business Impact

* **Zero-Hallucination Auditing:** Grounding decisions in ChromaDB policy chunks ensures consistent enforcement of financial controls.
* **Complete Audit Trail:** Every automated approval, flag, or rejection is persisted in SQLite with timestamps and policy citations for finance reviews.
* **Reduced Finance Triage Load:** Routine compliant claims are auto-approved instantly, allowing managers to focus exclusively on flagged exceptions.

---

## Tech Stack

* **Language:** Python
* **Agent Orchestration:** LangGraph (`StateGraph`), LangChain
* **LLM & Embeddings:** Google Gemini (`gemini-3.8-flash`), HuggingFace `all-MiniLM-L6-v2`
* **Vector Store & Database:** ChromaDB, SQLite, pandas
* **Techniques:** Retrieval-Augmented Generation (RAG), Deterministic Tool Calling, Stateful Multi-Agent Routing, Automated Benchmark Evaluation
