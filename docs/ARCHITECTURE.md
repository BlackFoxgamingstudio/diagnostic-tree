# Architecture: Sovereign Diagnostic Tree

## Overview

**Package ID:** `PKG-013`  
**Domain:** Expert Systems & Diagnostic Bots  
**Microservice Port:** `8791`  
**n8n Webhook Path:** `diagnostic-tree-trigger`  
**GitHub:** [BlackFoxgamingstudio/diagnostic-tree](https://github.com/BlackFoxgamingstudio/diagnostic-tree)

Rule-based and ML-augmented diagnostic decision-tree engine for field technicians. Supports HVAC, mechanical, electrical, and IoT fault trees.

---

## System Architecture

```
                     ┌──────────────────────────────────┐
                     │       Sovereign Diagnostic Tree     │
                     │       Port: 8791            │
                     ├──────────────┬───────────────────┤
   n8n Webhook ────▶ │  REST API    │   Core Engine     │
   HTTP POST         │  /api/v1/*   │   Dispatcher      │
                     └──────┬───────┴────────┬──────────┘
                            │                │
              ┌─────────────▼────────────────▼─────────┐
              │          Component Layer                 │
              │  DecisionTreeEng | FaultMapper     | SymptomParse  │
              └────────────────────────┬────────────────┘
                                       │
              ┌────────────────────────▼────────────────┐
              │      n8n Central Event Bus (:5678)       │
              └─────────────────────────────────────────┘
```

## Core Components

### `DecisionTreeEngine`
Handles all decisiontree operations. Exposes async methods callable from the core dispatcher.

### `FaultMapper`
Handles all faultmapper operations. Exposes async methods callable from the core dispatcher.

### `SymptomParser`
Handles all symptomparser operations. Exposes async methods callable from the core dispatcher.

### `RuleBaseManager`
Handles all rulebase operations. Exposes async methods callable from the core dispatcher.

### `DiagnosticReporter`
Handles all diagnosticreporter operations. Exposes async methods callable from the core dispatcher.

---

## API Contract

All interactions follow the SBB standard envelope:

```http
POST /api/v1/execute
Content-Type: application/json
X-SBB-API-Key: <api-key>

{
  "action": "<operation>",
  "payload": {},
  "trace_id": "optional-uuid"
}
```

**Success Response (HTTP 200):**
```json
{
  "status": "success",
  "data": {},
  "trace_id": "...",
  "timestamp": "2025-01-01T00:00:00Z"
}
```

**Health Check:**
```http
GET /health
→ {"status": "healthy", "service": "sovereign-diagnostic-tree", "port": 8791}
```

## Integration Matrix

| System | Protocol | Direction | Purpose |
|--------|----------|-----------|---------|
| n8n Event Bus (:5678) | HTTP POST | Outbound | Event forwarding |
| n8n Webhook | HTTP POST | Inbound | Trigger execution |
| SBB Codebase Vault (:8766) | HTTP | Outbound | Code analysis |
| SBB Patterns Bible (:8794) | HTTP | Outbound | Standards validation |
| External APIs | HTTPS | Outbound | Domain-specific data |

## Deployment Architecture

```yaml
# docker-compose excerpt
sovereign-diagnostic-tree:
  image: sovereign-diagnostic-tree:latest
  ports: ["8791:8791"]
  healthcheck:
    test: curl -f http://localhost:8791/health
    interval: 30s
```

## Security Model

| Control | Implementation |
|---------|---------------|
| Authentication | `X-SBB-API-Key` header (env: `SBB_API_KEY`) |
| Rate Limiting | 100 req/min per client IP |
| Input Validation | Pydantic models (strict mode) |
| Container Security | Non-root user (`appuser:1001`) |
| Secrets | Environment variables only (never hardcoded) |
| TLS | Terminate at reverse proxy (nginx/caddy) |

## Tags
`expert-system`, `diagnostics`, `decision-tree`, `field-tech`
