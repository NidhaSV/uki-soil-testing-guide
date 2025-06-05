# uki-soil-testing-guide
Part 1 - UKI Engineering Intern Assignment – Beckn Protocol Soil Testing Use Case
# 🌾 Soil Testing Use Case – Beckn Implementation Guide

## 📘 1. Introduction

This document serves as an implementation guide for integrating an agri-soil testing service with the Unified Krishi Interface (UKI) open network powered by the Beckn Protocol. It is designed to help developers understand the interaction between BAP and BPP roles in the context of a soil testing workflow.

The use case supports farmers in discovering soil testing services, placing orders, tracking fulfillment, and receiving digital test reports via a Beckn-compliant network.

---

## 🧱 2. BAP and BPP Roles

| Role | Description |
|------|-------------|
| **BAP (Beckn Application Platform)** | An application or platform through which farmers search for and request soil testing services. |
| **BPP (Beckn Provider Platform)** | A service provider (e.g., soil testing lab) that offers and fulfills soil testing services. |

---

## 👥 3. Entities and Roles on the Network

| Entity             | Role in Network           | Beckn Component  |
|--------------------|---------------------------|------------------|
| Farmer             | Requests soil test         | BAP User        |
| Agriculture App    | Helps to find soil test labs and place orders for test | BAP   |
| Soil Testing Lab   | Provides testing service   | BPP User  |
| Aggregator         | Platform where soil testing labs can register and receive orders | BPP |
| Extension Agent    | (Optional)Guidance based on test results for farmers | BPP / Post-fulfillment |

---

## 🔄 4. High-Level DOFP Flow

### DOFP Stages:
- **Discovery**: Farmer searches for nearby soil testing services in the Agriculture App.
- **Order**: Farmer selects lab, test type, and confirms the request.
- **Fulfillment**: Lab schedules pickup, tests sample, and uploads report.
- **Post-Fulfillment**: Farmer receives the report and can give feedback. Farmer can also initiate order for further expert analysis of the report.

---

## 🧪 5. API Call Sequence and Descriptions

| API Call     | Triggered By | Description |
|--------------|--------------|-------------|
| `search`     | BAP          | Farmer searches for soil testing labs |
| `on_search`  | BPP          | Lab(s) respond with available services |
| `select`     | BAP          | Farmer selects a service |
| `on_select`  | BPP          | Lab confirms availability for sample pickup |
| `init`       | BAP          | Farmer initiates the order |
| `on_init`    | BPP          | BPP acknowledges and returns order preview |
| `confirm`    | BAP          | Farmer confirms order |
| `on_confirm` | BPP          | Lab confirms and schedules collection |
| `status`     | BAP          | Farmer checks order status |
| `update`     | BPP          | Lab updates sample pickup/testing/report status |
| `rating`     | BAP          | Farmer rates the service |
| `support`    | BAP          | Farmer raises issue/feedback |

---

## 📄 6. Sample JSON Payloads

### 🔍 `search` Request
```json
{
  "context": {
    "domain": "agri.soil_testing",
    "action": "search",
    "version": "1.1.0",
    "city": "std:080",
    "country": "IND",
    "timestamp": "2025-06-05T10:00:00Z",
    "transaction_id": "abc-123",
    "message_id": "msg-001"
  },
  "message": {
    "intent": {
      "item": {
        "descriptor": { "name": "Soil Testing" }
      },
      "fulfillment": {
        "start": {
          "location": {
            "gps": "12.9716,77.5946"
          }
        }
      }
    }
  }
}
```

### ✅ `on_search` Response (Partial)
```json
{
  "context": {
    "action": "on_search",
    "domain": "agri.soil_testing",
    "transaction_id": "abc-123",
    "message_id": "msg-002"
  },
  "message": {
    "catalog": {
      "providers": [
        {
          "id": "lab_001",
          "descriptor": { "name": "GreenSoil Labs" },
          "items": [
            {
              "id": "soil_test_npk",
              "descriptor": { "name": "NPK - Soil Test" },
              "price": { "currency": "INR", "value": "299" }
            }
          ]
        }
      ]
    }
  }
}
```


## 🏷️ 7. Taxonomy and Tagging Assumptions
Item Name Tags: "Basic Soil Test", "Advanced NPK Analysis"
Service Attributes: Turnaround time, pickup availability, report format
Location Tags: GPS-based tagging for farms
Report Format: PDF + structured JSON

## ⚠️ 8. Assumptions and Challenges
Internet access may be limited in rural areas.
Sample pickup logistics may require agent coordination.
Reports must be multilingual (local language support).
Testing services may have variable turnaround times.

## 🧑‍💻 9. Error Handling and Edge Cases
No labs found (on_search empty): show fallback message.
Lab unavailable after select: retry or suggest alternatives.
Network failure: retry with exponential backoff.
Test delayed: send proactive update using update call.

## 🔗 10. Developer Notes and References
Beckn Protocol: https://becknprotocol.io
Schema Reference: https://docs.google.com/document/d/1dAXXhoMpTKnluYe_LKUFBM66K0IfHRVTHMzH_ECKNDA/edit
Use ISO 8601 for all timestamps.
Secure all APIs with token-based authentication.

## 📚 11. Appendix
Glossary:
BAP: Beckn Application Platform
BPP: Beckn Provider Platform
DOFP: Discovery, Order, Fulfillment, Post-Fulfillment

## 🔧 12. Tools Used
- draw.io
- VS Code
