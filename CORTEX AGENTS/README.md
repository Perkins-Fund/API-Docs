# Traceix Cortex Agents API & SDK Endpoints

Cortex Agents are lightweight "remote triage workers" that let your client check in, fetch its configuration, submit files for analysis, poll for results, and emit alerts — all under a consistent response envelope and strict ownership enforcement per API key.

* [Common Response Envelope](#common-response-envelope)
* [Agent Check-in](#agent-check-in)
* [Get Agent Metadata](#get-agent-metadata)
* [Run Agent](#run-agent)
* [Check Agent Run Status](#check-agent-run-status)
* [Submit Agent Alert](#submit-agent-alert)
* [Get Agent Alerts](#get-agent-alerts)
* [SDKs](#sdks)

### API Base URL

`https://ai.perkinsfund.org`

---

## Common Response Envelope

All endpoints return this top-level structure:

```json
{
  "error": {},
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768241023.740849,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": {},
  "success": true
}
```

On failure:

```json
{
  "error": {
    "error_message": "..."
  },
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768241028.700035,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": null,
  "success": false
}
```

### Authentication

All endpoints require:

* `X-Api-Key: YOUR_API_KEY`

### Ownership rule (important)

Where an endpoint accepts `agent_uuid`, the `agent_uuid` **must belong to the authenticated user (API key owner)**. Otherwise, the request fails (e.g., “Invalid agent UUID” / “Agent does not exist”).

---

## Agent Check-in

Endpoint: `/api/traceix/agent/checkin`
Request type: `POST`
Content type: JSON

Headers:

* `X-Api-Key: YOUR_API_KEY`
* `content-type: application/json`

Body:

* `agent_uuid`: string

### Example request

```bash
curl -X POST \
  -H "content-type: application/json" \
  -H "x-api-key: YOUR_API_KEY" \
  --data '{"agent_uuid":"AGENT_UUID"}' \
  https://ai.perkinsfund.org/api/traceix/agent/checkin
```

### Expected outputs

Success:

```json
{
  "error": {},
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768240753.374693,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": { "ok": true },
  "success": true
}
```

Failure (invalid agent UUID):

```json
{
  "error": { "error_message": "Invalid agent UUID" },
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768240797.701423,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": null,
  "success": false
}
```

---

## Get Agent Metadata

Endpoint: `/api/traceix/agent/metadata`
Request type: `POST`
Content type: JSON

Headers:

* `X-Api-Key: YOUR_API_KEY`
* `content-type: application/json`

Body:

* `agent_uuid`: string

### Example request

```bash
curl -X POST \
  -H "content-type: application/json" \
  -H "x-api-key: YOUR_API_KEY" \
  --data '{"agent_uuid":"AGENT_UUID"}' \
  https://ai.perkinsfund.org/api/traceix/agent/metadata
```

### Expected outputs

Success:

```json
{
  "error": {},
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768241023.740849,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": {
    "agent_config": {
      "accepted_file_type": "windows",
      "agent_features": ["classification", "exif"],
      "agent_uuid": "agnt-...",
      "max_file_size": 3145728,
      "requested_delivery_style": "raw"
    },
    "agent_metadata": {
      "agent_created_at": "2026-01-12T16:27:11.903000+00:00",
      "agent_name": "Traceix Windows Fast Triage",
      "agent_stats": { "processed_files": 1 },
      "agent_tags": ["triage", "windows", "soc"]
    }
  },
  "success": true
}
```

Failure (not owned / not accessible / doesn’t exist):

```json
{
  "error": { "error_message": "Agent does not exist" },
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768241028.700035,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": null,
  "success": false
}
```

---

## Run Agent

Endpoint: `/api/traceix/agent/run`
Request type: `POST`
Content type: `multipart/form-data`

Headers:

* `X-Api-Key: YOUR_API_KEY`
* `X-Agent-Id: AGENT_UUID`

Body:

* `file`: the file to submit (multipart form)

> **NOTE:** This endpoint queues an analysis job and returns a `uuid` you will poll using `/api/traceix/agent/status`.

### Example request

```bash
curl -X POST \
  -H "x-api-key: YOUR_API_KEY" \
  -H "x-agent-id: AGENT_UUID" \
  -F "file=@/path/to/file.exe" \
  https://ai.perkinsfund.org/api/traceix/agent/run
```

### Expected outputs

Success (queued):

```json
{
  "error": {},
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768241074.539515,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": {
    "status": "PENDING",
    "uuid": "67fa2864-5c58-4533-b626-3da9dd48bb76"
  },
  "success": true
}
```

Failure (agent does not exist / not accessible):

```json
{
  "error": { "error_message": "Agent does not exist" },
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768241082.298161,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": null,
  "success": false
}
```

---

## Check Agent Run Status

Endpoint: `/api/traceix/agent/status`
Request type: `POST`
Content type: JSON

Headers:

* `X-Api-Key: YOUR_API_KEY`
* `content-type: application/json`

Body:

* `uuid`: string (the job UUID returned from `/api/traceix/agent/run`)
* `agent_uuid`: string (the agent UUID used to launch the job)

> **NOTE:** You must provide **both** `uuid` and `agent_uuid`.

### Example request

```bash
curl -X POST \
  -H "content-type: application/json" \
  -H "x-api-key: YOUR_API_KEY" \
  --data '{"uuid":"RUN_UUID","agent_uuid":"AGENT_UUID"}' \
  https://ai.perkinsfund.org/api/traceix/agent/status
```

### Expected outputs

Success can return **either** a pending status **or** completed results.

Success (pending):

```json
{
  "error": {},
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768241148.871836,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": {
    "status": "PENDING",
    "uuid": "67fa2864-5c58-4533-b626-3da9dd48bb76"
  },
  "success": true
}
```

Success (completed example):

```json
{
  "error": {},
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768241143.9729,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": {
    "classification": "safe",
    "exif": {
      "FileDescription": "Windows Calculator"
    }
  },
  "success": true
}
```

Failure example:

```json
{
  "error": { "error_message": "Invalid agent UUID" },
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768241401.4666727,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": null,
  "success": false
}
```

---

## Submit Agent Alert

Endpoint: `/api/traceix/agent/alert`
Request type: `POST`
Content type: JSON

Headers:

* `X-Api-Key: YOUR_API_KEY`
* `content-type: application/json`

Body (required):

* `agent_uuid`: string
* `client_id`: string
* `classification`: string (example: `safe`, `malicious`, etc.)
* `file_path`: string
* `sha256_hash`: string

Body (optional / nullable):

* `capa`: array or null
* `exif`: object or null
* `yara`: string or null

### Example request

```bash
curl -X POST \
  -H "content-type: application/json" \
  -H "x-api-key: YOUR_API_KEY" \
  --data '{
    "agent_uuid":"AGENT_UUID",
    "client_id":"test_id",
    "classification":"safe",
    "file_path":"C:\\Windows\\System32\\calc.exe",
    "sha256_hash":"ed369187681a62247e38d930320f1cd771756d0b7b67072d8ec655ef99e14aeb"
  }' \
  https://ai.perkinsfund.org/api/traceix/agent/alert
```

### Expected outputs

Success:

```json
{
  "error": {},
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768240926.097389,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": { "ok": true },
  "success": true
}
```

Failure (invalid agent UUID):

```json
{
  "error": { "error_message": "Invalid agent UUID" },
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768240895.260689,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": null,
  "success": false
}
```

---

## Get Agent Alerts

Endpoint: `/api/traceix/agent/alerts/get`
Request type: `POST`
Content type: JSON

Headers:

* `X-Api-Key: YOUR_API_KEY`
* `content-type: application/json`

Body:

* `agent_uuid`: string

### Example request

```bash
curl -X POST \
  -H "content-type: application/json" \
  -H "x-api-key: YOUR_API_KEY" \
  --data '{"agent_uuid":"AGENT_UUID"}' \
  https://ai.perkinsfund.org/api/traceix/agent/alerts/get
```

### Expected outputs

Success (example shape):

```json
{
  "error": {},
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768240989.672061,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": [
    {
      "agent_uuid": "agnt-...",
      "alert_id": "alrt_...",
      "alert_timestamp": "2026-01-12T18:02:06.062701+00:00",
      "classification": "safe",
      "client_id": "test_id",
      "event_id": "evt_...",
      "file_path": "C:\\Windows\\System32\\calc.exe",
      "sha256_hash": "ed36...",
      "is_reviewed": false,
      "last_check_in": null,
      "capabilities": null,
      "exif_data": null,
      "yara_rule": null
    }
  ],
  "success": true
}
```

Failure (invalid agent UUID):

```json
{
  "error": { "error_message": "Invalid agent UUID" },
  "request_metadata": {
    "request_id": "req_...",
    "request_timestamp": 1768241001.276291,
    "schema_version": "traceix.agent_output.v1"
  },
  "results": null,
  "success": false
}
```

---

## SDKs

* GitHub: `https://github.com/Perkins-Fund/Traceix-SDK`
* More languages: coming soon…
