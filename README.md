# DineSync

**Restaurant table booking & pre-ordering — Software Engineering Labs 1–3**

| | |
|---|---|
| **Student** | Srijan Kumar |
| **SRN** | PES1UG24CS467 |
| **Institution** | PES University, Bangalore |
| **Course** | Software Engineering |
| **Problem Statement** | #36 – Restaurant Table Booking & Pre-Ordering App |

---

## About DineSync

DineSync lets diners pick a table from an interactive 2D floor plan, reserve it for a date and time, and pre-order food so the kitchen can have it ready on arrival. Restaurant managers see live table status across all floor terminals and can update or cancel reservations, while kitchen staff receive pre-order tickets timed to each diner's arrival.

This repository holds the full software-engineering lifecycle for DineSync across three labs: **requirements → Agile planning → architecture**.

### Actors

| Actor | Role |
|---|---|
| **Diner** | Registers, views tables, books, pre-orders and pays |
| **Restaurant Manager** | Manages reservations and table status |
| **Kitchen Staff** | Manages incoming pre-order tickets |
| **Payment Gateway** | Processes pre-order payments |

---

## Repository Structure

```text
MyProject_Dine_Sync/
├── README.md
├── Lab1/   Requirements Engineering & UML Use-Case Modelling
├── Lab2/   Jira – Kanban, Scrum & Bug Tracking
└── Lab3/   Software Architecture & Component Diagram
```

---

## Lab 1 – Requirements Engineering & UML Use-Case Modelling

📁 [`Lab1/`](Lab1)

### Requirements

| ID | Type | Requirement | Priority |
|---|---|---|---|
| FR-001 | Functional | Pick an open table from an interactive 2D floor plan and link pre-ordered items to the reservation | High |
| FR-002 | Functional | Show real-time table availability for the selected date and time | High |
| FR-003 | Functional | Enter reservation date, time and number of guests | High |
| FR-004 | Functional | Calculate kitchen preparation time from pre-order items and arrival time | High |
| FR-005 | Functional | Managers can view, update and cancel reservations and their orders | High |
| NFR-001 | Performance | Table status changes sync across all manager terminals in under 500 ms | High |
| NFR-002 | Security | Secure authentication and encrypted data transmission for diner information | High |

### Deliverables

| File | Description |
|---|---|
| `01_Requirements_Table.docx` | FRs and NFRs with priority, acceptance criteria and rationale |
| `02_Use_Case_Diagram.pdf` / `.drawio` | UML use-case diagram: 10 use cases, 4 actors, `«include»` and `«extend»` relationships |
| `03_Use_Case_Flow_One_Page.pdf` / `.docx` | UC-03 *Book Restaurant Table with Pre-Ordering*: main success scenario and alternate flows |
| `04_Activity_Diagram_Corrected.pdf` / `.drawio` | Activity diagram of the booking and pre-order flow |
| `05_Exception_Flow_Document.pdf` / `.docx` | Exception flows and error handling |

---

## Lab 2 – Agile Backlog & Sprint Simulation in Jira

📁 [`Lab2/`](Lab2)

The Lab 1 requirements were turned into Jira work items and managed in three Jira projects.

| Jira Project | Type | What it shows | File |
|---|---|---|---|
| Kanban (KRB36) | Kanban | Epic, 5 FR + 2 NFR stories in the backlog, implementation tasks and subtasks | `Kanban_proj.pdf` |
| Scrum (SRB36) | Scrum | User stories, priorities, Fibonacci story points (34 total), Sprint 1 (11 pts) and Sprint 2 (23 pts), burndown chart, reflection | `Scrum_proj.pdf` |
| Bug-log_Project | Bug tracking | Six logged bugs linked to requirements, with steps to reproduce, severity, priority and resolution workflow | `Bug_Reports.md` + video |

---

## Lab 3 – Software Architecture & Component Diagram

📁 [`Lab3/`](Lab3)

**Architecture style: Layered Architecture** (presentation → business logic → data access)

| Component | Responsibility |
|---|---|
| Diner / Manager Interface | User-facing screens for diners and managers |
| Order Manager | Coordinates reservations, pre-orders and the order workflow |
| Payment Service | Processes payments |
| Table & Menu Service | Table availability, table selection and menu data |
| Kitchen / Receipt Service | Kitchen tickets and receipts |

**Why layered:** it separates the user interfaces from reservation, ordering and payment logic, which makes the system easier to maintain and extend. Authentication and authorization sit in the application layer, and table and menu lookups can be tuned for performance independently.

| File | Description |
|---|---|
| `Component_Diagram.pdf` / `.drawio` | UML component diagram with interfaces and dependencies |
| `Architecture_Justification.pdf` | Justification covering the scenario, security and performance |

---

## Tools Used

- **draw.io:** UML use-case, activity and component diagrams
- **Microsoft Word:** requirements and specification documents
- **Jira Software:** Kanban, Scrum and bug tracking
- **GitHub:** version control and submission
