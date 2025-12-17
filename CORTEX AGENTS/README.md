# Cortex Agents API & SDK Endpoints

* [Get Agent Metadata](#get-agent-metadata)
* [Run Agent](#run-agent)
* [Check Agent Run Status](#check-agent-run-status)
* [SDKs](https://github.com/Perkins-Fund/Traceix-SDK)

> THIS FEATURE IS NOT RELEASED YET

### API BASE URL: `https://ai.perkinsfund.org`

---

### Get Agent Metadata

Endpoint: `/api/traceix/agent/metadata`

Request type: POST

Data type: JSON

Headers:
- `X-Api-Key: API KEY`
- `content-type: application/json`

##### NOTE: The `agent_uuid` must belong to the authenticated user (API key owner). If the agent is not in the user’s saved agents, the request should fail.

#### Example request

```bash
curl -X POST \
  -H "content-type: application/json" \
  -H "x-api-key: YOUR_API_KEY" \
  --data '{"agent_uuid":"AGENT_UUID"}' \
  https://ai.perkinsfund.org/api/traceix/agent/metadata
````

#### Expected outputs

Success:

```json
{
  "error": {},
  "request_timestamp": 1765994865.1905847,
  "results": {
    "agent_uuid": "AGENT UUID",
    "cortex_agent": {
      "agent_config": {
        "accepted_file_type": "FILE TYPES",
        "delivery_style": "raw OR summary OR minimal",
        "features": "feature1,feature2,...",
        "max_file_size": SIZE IN BYTES
      },
      "metadata": {
        "name": "...",
        "stats": {
          "processed_files": TOTAL FILES
        },
        "tags": "tag1,tag2,...",
        "timestamp": "2025-12-16T19:57:13.443000+00:00"
      }
    },
    "schema_version": 1
  },
  "success": true
}
```

Unsuccessful (agent does not exist / not owned / not accessible):

```json
{
  "error": {
    "error_message": "..."
  },
  "request_timestamp": 1765991415.251004,
  "results": null,
  "success": false
}
```

---

### Run Agent

Endpoint: `/api/traceix/agent/run`

Request type: POST

Data type: File (multipart form)

Headers:

* `X-Api-Key: API KEY`
* `X-Agent-Id: AGENT UUID`

Body:

* `file=@/path/to/file`

##### NOTE: This endpoint queues an analysis job and returns a `uuid` you will use to poll status.

#### Example request

```bash
curl -X POST \
  -H "x-api-key: YOUR_API_KEY" \
  -H "x-agent-id: AGENT_UUID" \
  -F "file=@/path/to/file.exe" \
  https://ai.perkinsfund.org/api/traceix/agent/run
```

#### Expected outputs

Success (queued):

```json
{
  "error": {},
  "request_timestamp": 1765991270.2533052,
  "results": {
    "status": "PENDING",
    "uuid": "UUID"
  },
  "success": true
}
```

Unsuccessful (invalid API key):

```json
{
  "error": {
    "error_message": "Unauthorized"
  },
  "request_timestamp": 1765991395.2504053,
  "results": null,
  "success": false
}
```

---

### Check Agent Run Status

Endpoint: `/api/traceix/agent/status`

Request type: POST

Data type: JSON

Headers:

* `X-Api-Key: API KEY`
* `content-type: application/json`

Body:

* `uuid`: string (the job UUID returned from `/api/traceix/agent/run`)
* `agent_uuid`: string (the agent UUID used to launch the job)

##### NOTE: You must provide **both** `uuid` and `agent_uuid`. A valid `uuid` from a prior run is required.

#### Example request

```bash
curl -X POST \
  -H "content-type: application/json" \
  -H "x-api-key: YOUR_API_KEY" \
  --data '{"uuid":"RUN_UUID","agent_uuid":"AGENT_UUID"}' \
  https://ai.perkinsfund.org/api/traceix/agent/status
```

#### Expected outputs

Success (completed output example):

```json
{
  "error": {},
  "request_timestamp": 1765991351.5976613,
  "results": {
    "output": {
      "capa": [
        {
          "attack": [],
          "catalog": [
            ["Dynamic Analysis Evasion", "B0003.003"]
          ],
          "name": "Delay Execution"
        },
        ...
      ],
      "classification": "CLASS",
      "exif": {
        "FileDescription": "Windows Calculator",
        ...
      },
      "yara": "rule TraceixRuleGenerator_... { ... }"
    }
  },
  "success": true
}
```

Unsuccessful (invalid agent UUID):

```json
{
  "error": {
    "error_message": "Invalid agent UUID"
  },
  "request_timestamp": 1765991401.4666727,
  "results": null,
  "success": false
}
```

---

## SDKs

SDKs: coming soon...

