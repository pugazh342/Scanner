# VulnHunter Pro

VulnHunter Pro is organized into modular components that operate inside a single, stateful orchestration loop.

## 🏗️ Architecture Overview

Mermaid architecture diagram (renders on GitHub / Mermaid-capable viewers):

```mermaid
flowchart LR
  A[Reconnaissance] --> B[Business Logic Tester]
  B --> C[Orchestrator]
  C --> D[Verification Engine]
  D --> E[AI Pattern Learner]
  E --> B

  subgraph Runtime
    C
    D
  end
```

### Component Responsibilities
- **Reconnaissance**: fingerprint target tech stack, discover endpoints, enumerate workflows.
- **Business Logic Tester (Generator)**: converts WorkflowTemplate YAMLs into LogicAttack sequences (stateful multi-step requests).
- **Orchestrator**: executes attacks using a persistent httpx session, handles dynamic data extraction and state tracking.
- **Verification Engine**: applies success criteria against execution logs to determine True Positives and produces PoC artifacts.
- **AI Pattern Learner**: trains a lightweight Logistic Regression model on confirmed findings and produces a Priority Score for future attacks.

---

## 🔁 AI Feedback Workflow

Mermaid workflow diagram:

```mermaid
flowchart LR
  P[Prioritization - AI Learner] --> E[Execution - Orchestrator]
  E --> V[Validation - Verification Engine]
  V --> L[Learning - AI Retrain]
  L --> P
```

### Loop Description
- **Prioritization** — AI Pattern Learner scores generated attacks.
- **Execution** — Orchestrator runs high-priority attacks first.
- **Validation** — Verification Engine checks success criteria and marks True Positives.
- **Learning** — confirmed findings retrain the model to shift priorities.

---

## 🗂️ File Structure (important paths)
```
VulnHunter_Pro/
├─ src/
│  ├─ core/
│  │  └─ data_models.py          # Pydantic schemas (WorkflowTemplate, LogicAttack, etc.)
│  ├─ modules/
│  │  ├─ logic_tester.py         # BusinessLogicTester (generator)
│  │  ├─ orchestrator.py         # Executor
│  │  └─ validator.py            # Verification Engine
│  ├─ ai/
│  │  └─ learner.py              # AI Pattern Learner (Logistic Regression)
│  └─ ui/
│     └─ dashboard.py            # Streamlit dashboard
├─ attacks/
│  └─ templates/                 # WorkflowTemplate YAMLs
└─ ai_data/
   └─ confirmed_vulnerabilities.csv
```

---

## 🛠️ Extensibility Guide

### Adding new workflows
- Add a YAML file to `attacks/templates/` following the `WorkflowTemplate` schema in `src/core/data_models.py`.
- Use dynamic placeholders like `{cart_id}` to reference previous step outputs.

### Adding attack strategies
- Edit `src/modules/logic_tester.py`.
- Add a method like `_generate_price_manipulation()` returning `List[LogicAttack]`.
- Ensure it is called by `generate_all_attacks()` so attacks are included and scored.

---

## ✅ Verification & Evidence
The Verification Engine:
- Parses execution logs to apply success criteria (e.g., unauthorized state change, altered price, or final HTTP status).
- Produces a PoC script and attaches technical evidence (HTTP requests/responses, extracted variables).

---

## ⚙️ AI Learner Details
- **Model**: Lightweight Logistic Regression (scikit-learn).
- **Features**: attack_type, target_stack, previous_success_rates, template_tags (e.g., `checkout`, `admin-flow`).
- **Feedback loop**: Each confirmed finding is appended to `ai_data/confirmed_vulnerabilities.csv` and triggers a retrain.

---

## 🧪 Example Workflow Template (YAML snippet)
```yaml
name: ecommerce_checkout
steps:
  - id: create_cart
    method: POST
    path: /api/cart
    body:
      items: [{ "sku": "ABC", "qty": 1 }]
  - id: update_price
    method: PATCH
    path: /api/cart/{cart_id}/items/0
    body:
      price: 0.01
  - id: checkout
    method: POST
    path: /api/checkout
    body:
      cart_id: "{cart_id}"
success_criteria:
  - type: status_code
    value: 200
  - type: state_change
    description: "Order total less than expected"
```

---

## 🧾 Contribution & License
Contributions are welcome. Follow standard GitHub PR flow and include tests for new attack generators and validators.

Licensed under **MIT** — adapt as necessary for your org.

---

## Appendix — Diagrams (raw Mermaid)

**Architecture Diagram**
```mermaid
flowchart LR
  A[Reconnaissance] --> B[Business Logic Tester]
  B --> C[Orchestrator]
  C --> D[Verification Engine]
  D --> E[AI Pattern Learner]
  E --> B

  subgraph Runtime
    C
    D
  end
```

**Workflow Diagram**
```mermaid
flowchart LR
  P[Prioritization - AI Learner] --> E[Execution - Orchestrator]
  E --> V[Validation - Verification Engine]
  V --> L[Learning - AI Retrain]
  L --> P
```



---

## 🧾 Contribution & License
Contributions are welcome. Follow standard GitHub PR flow and include tests for new attack generators and validators.

Licensed under MIT — adapt as necessary for your org.

---

## Appendix — Diagrams (raw Mermaid)
- Architecture diagram: see above under **Architecture Overview**
- Workflow diagram: see above under **AI Feedback Workflow**

---



