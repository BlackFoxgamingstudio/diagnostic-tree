# Standard Operating Procedure: Sovereign Diagnostic Tree

## 1. Service Health Verification
Run `sovereign-diagnostic-tree --health` to confirm the engine responds with status `HEALTHY`.

## 2. Webhook Adapter Operation
The webhook adapter listens on port `8791`:
```bash
python3 n8n/webhook_adapter.py
```
If port 8791 is occupied, check active processes:
```bash
lsof -i :8791
```

## 3. n8n Integration Testing
Send a probe POST request:
```bash
curl -X POST http://localhost:8791/api/v1/execute \
  -H "Content-Type: application/json" \
  -d '{"action": "health_ping", "payload": {"test": true}}'
```
