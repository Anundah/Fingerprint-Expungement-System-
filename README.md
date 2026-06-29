# Fingerprint-Expungement-System-
Microservices system to automate fingerprint expungement from DCI AFIS following court acquittals, withdrawals, and nolle prosequi outcomes in Kenya.
# Fingerprint Expungement System
### Automated Biometric Record Removal Following Court Acquittals — Kenya

[

![Status](https://img.shields.io/badge/Status-Prototype%20in%20Development-blue)

]()
[

![Stack](https://img.shields.io/badge/Stack-Spring%20Boot%20%7C%20PostgreSQL%20%7C%20Keycloak%20%7C%20Docker-brightgreen)

]()
[

![Author](https://img.shields.io/badge/Author-Hillary%20Anunda-informational)

]()

---

## The Problem

When a person is **acquitted**, their case **withdrawn**, or a **nolle prosequi** entered in a Kenyan court, their fingerprints remain in the Directorate of Criminal Investigations' Automated Fingerprint Identification System (AFIS) unless they physically carry certified court documents to the DCI to request removal.

In practice:
- Most acquitted persons are unaware this step is required
- Many cannot afford the time or logistics to pursue it
- The result is innocent persons carrying a latent criminal biometric record

This violates **Articles 49 and 50 of the Constitution of Kenya**, which guarantee rights of accused persons and the presumption of innocence.

---

## The Solution

An **automated, API-driven integration** between the Judiciary's case management system and DCI AFIS — so that when a magistrate or judge enters a final disposition in the court system, a verified expungement request is automatically dispatched to DCI, with no physical document delivery required from the accused.

---

## Microservices Architecture

| Service | Responsibility |
|---|---|
| `court-outcome-service` | Receives and validates case disposition events from the Judiciary |
| `eligibility-verification-service` | Confirms outcome is final and biometric reference exists |
| `expungement-request-service` | Dispatches verified request to DCI AFIS, maintains audit trail |
| `notification-service` | Confirms expungement completion to court, ODPP, and accused |
| `api-gateway` | Single authenticated entry point for all inter-agency communication |
| `mock-judiciary-service` | Simulates Judiciary JEMS for POC demonstration |
| `mock-dci-afis-service` | Simulates DCI AFIS receiving and confirming expungement requests |

---

## Technology Stack

- **Spring Boot** — microservices framework
- **PostgreSQL** — audit database (full expungement event trail)
- **Keycloak** — inter-agency authentication via client credentials flow
- **Docker / Docker Compose** — containerised deployment (`docker-compose up` runs the full system)
- **RabbitMQ** — event queue between services

---

## Database Schema

```sql
cases (
  case_number, accused_name, biometric_ref_id,
  outcome, outcome_date, court, presiding_officer
)

expungement_requests (
  id, case_number, requested_at, status,
  dci_confirmation_ref, confirmed_at
)

audit_log (
  id, event_type, triggered_by, timestamp, details
)
