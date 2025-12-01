# Traceix API & SDK Endpoints

* [AI Prediction Upload](#ai-prediction-upload)
* [Check Upload Status](#check-upload-status)
* [CAPA Extraction](#capa-extraction)
* [EXIF Extraction](#exif-extraction)
* [Search CAPA by Hash](#search-capa-by-hash)
* [Search EXIF by Hash](#search-exif-by-hash)
* [List Public IPFS Datasets](#list-public-ipfs-datasets)
* [Get Public IPFS Dataset](#get-public-ipfs-dataset)
* [Search IPFS Dataset by Hash](#search-ipfs-dataset-by-hash)
* [SDKs](https://github.com/Perkins-Fund/Traceix-SDK)

### API BASE URL: `https://ai.perkinsfund.org`

---

### AI Prediction Upload

Endpoint: `/api/traceix/v1/upload`

Request type: POST

Data type: File

Headers: `X-Api-Key: API KEY`

#### Example request

```bash
curl -H "x-api-key: YOUR_API_KEY" \
     -F "file=@/path/to/file.exe" \
     https://ai.perkinsfund.org/api/traceix/v1/upload
```

#### Expected outputs

Success:

```json
{
  "success": true,
  "error": {},
  "request_timestamp": 1764610085.25132,
  "results": {
    "class": "safe",
    "time_taken": 0.46030490286648273,
  },
  "sponsor": {
    "link": "https://example.com/",
    "title": "Sponsor text"
  },
  "copyright": "(c) PCEF all rights reserved"
}
```

Unsuccessful:

```json
{
  "success": false,
  "error": {
    "error_message": "MESSAGE"
  },
  "results": {},
  "request_timestamp": 1764610085.25132
}
```

---

### Check Upload Status

Endpoint: `/api/v1/traceix/status`

Request type: POST

Data type: JSON

Headers: `X-Api-Key: API KEY`

##### NOTE: A valid `uuid` from a prior upload is required.

#### Example request

```bash
curl -X POST -H "x-api-key: YOUR_API_KEY" \
     -H "content-type: application/json" \
     --data '{"uuid": "YOUR_UUID_HERE"}' \
     https://ai.perkinsfund.org/api/v1/traceix/status
```

#### Expected outputs

Success:

**NOTE:** When the status is completed, related classification and metadata can be retrieved using the hash search and extraction endpoints below.

```json
{
  "success": true,
  "error": {},
  "results": {
    "uuid": "YOUR_UUID_HERE",
    "status": "queued"  // or "processing", "completed", "error"
  },
  "request_timestamp": 1764610085.25132
}
```

Unsuccessful (missing UUID, invalid UUID, etc.):

```json
{
  "success": false,
  "error": {
    "error_message": "You did not provide a UUID required by the endpoint"
  },
  "results": {}
}
```

---

### CAPA Extraction

Endpoint: `/api/traceix/v1/capa`

Request type: POST

Data type: File

Headers: `X-Api-Key: API KEY`

#### Example request

```bash
curl -H "x-api-key: YOUR_API_KEY" \
     -F "file=@/path/to/file.exe" \
     https://ai.perkinsfund.org/api/traceix/v1/capa
```

#### Expected outputs

Success:

```json
{
  "success": true,
  "error": {},
  "results": {
    "sha256": "FILE_SHA256",
    "capabilities": [
      {
        "name": "persistence via registry run key",
        "attack_id": "T1060",
      }
    ]
  }
}
```

Unsuccessful:

```json
{
  "success": false,
  "error": {
    "error_message": "MESSAGE"
  },
  "results": {}
}
```

---

### EXIF Extraction

Endpoint: `/api/traceix/v1/exif`

Request type: POST

Data type: File

Headers: `X-Api-Key: API KEY`

#### Example request

```bash
curl -H "x-api-key: YOUR_API_KEY" \
     -F "file=@/path/to/file.exe" \
     https://ai.perkinsfund.org/api/traceix/v1/exif
```

#### Expected outputs

Success:

```json
{
  "success": true,
  "error": {},
  "results": {
    "sha256": "FILE_SHA256",
    "metadata": {
      ...
    }
  }
}
```

Unsuccessful:

```json
{
  "success": false,
  "error": {
    "error_message": "MESSAGE"
  },
  "results": {}
}
```

---

### Search CAPA by Hash

Endpoint: `/api/traceix/v1/capa/search`

Request type: POST

Data type: JSON

Headers: `X-Api-Key: API KEY`

##### NOTE: Uses the file SHA256 hash to retrieve previously extracted CAPA data.

#### Example request

```bash
curl -X POST -H "x-api-key: YOUR_API_KEY" \
     -H "content-type: application/json" \
     --data '{"sha256": "FILE_SHA256"}' \
     https://ai.perkinsfund.org/api/traceix/v1/capa/search
```

#### Expected outputs

Success:

```json
{
  "success": true,
  "error": {},
  "results": {
    "sha256": "FILE_SHA256",
    "capabilities": [
      {
        "name": "persistence via registry run key",
        "attack_id": "T1060"
      }
    ]
  }
}
```

Unsuccessful:

```json
{
  "success": false,
  "error": {
    "error_message": "No matching record for provided SHA256"
  },
  "results": {}
}
```

---

### Search EXIF by Hash

Endpoint: `/api/traceix/v1/exif/search`

Request type: POST

Data type: JSON

Headers: `X-Api-Key: API KEY`

##### NOTE: Uses the file SHA256 hash to retrieve previously extracted EXIF/metadata.

#### Example request

```bash
curl -X POST -H "x-api-key: YOUR_API_KEY" \
     -H "content-type: application/json" \
     --data '{"sha256": "FILE_SHA256"}' \
     https://ai.perkinsfund.org/api/traceix/v1/exif/search
```

#### Expected outputs

Success:

```json
{
  "success": true,
  "error": {},
  "results": {
    "sha256": "FILE_SHA256",
    "metadata": {
      ...
    }
  }
}
```

Unsuccessful:

```json
{
  "success": false,
  "error": {
    "error_message": "No matching record for provided SHA256"
  },
  "results": {}
}
```

---

### List Public IPFS Datasets

Endpoint: `/api/traceix/v1/ipfs/listall`

Request type: POST

Data type: JSON (no body required)

Headers: *(API key not required, but accepted)*

#### Example request

```bash
curl -X POST \
     https://ai.perkinsfund.org/api/traceix/v1/ipfs/listall
```

#### Expected outputs

Success:

```json
{
  "copyright": "(c) PCEF all rights reserved",
  "error": {},
  "request_timestamp": 1762267116.2708333,
  "results": [
    { "cid": "...", "sha256": "..." },
    ...
  ],
  "sponsor": {
    "link": "...",
    "title": "..."
  },
  "success": true
}
```

Unsuccessful:

```json
{
  "copyright": "(c) PCEF all rights reserved",
  "error": {
    "error_message": "MESSAGE"
  },
  "request_timestamp": 1762267116.2708333,
  "results": [],
  "success": false
}
```

---

### Get Public IPFS Dataset

Endpoint: `/api/traceix/v1/ipfs/search`

Request type: POST

Data type: JSON

Headers: *(API key not required, but accepted)*

##### NOTE: Requires the dataset `cid`.

#### Example request

```bash
curl -X POST -H "content-type: application/json" \
     --data '{"cid": "bafybeigdyr..."}' \
     https://ai.perkinsfund.org/api/traceix/v1/ipfs/search
```

#### Expected outputs

Success:

```json
{
  "copyright": "(c) PCEF all rights reserved",
  "error": {},
  "request_timestamp": 1762267143.519864,
  "results": {
    "decrypted_training_data": {
      ...
    },
    "metadata": {
      "model_information": {
        "model_accuracy": "91.64%",
        "model_version": "o1"
      },
      "payment_transaction": {
        "amount_paid": "...",
        "payment_tx": "...",
        "payment_tx_url": "https://solscan.io/tx/...",
        "thrt_price_at_payment": "..."
      },
      "upload_information": {
        "file_sha_hash": "...",
        "license": "CC BY 4.0",
        "upload_timestamp": "..."
      }
    },
    "model_classification_info": {
      "date_classified_on": "...",
      "identified_class": "...",
      "verdict_seconds": "..."
    }
  },
  "sponsor": {
    "link": "...",
    "title": "..."
  },
  "success": true
}
```

Unsuccessful:

```json
{
  "copyright": "(c) PCEF all rights reserved",
  "error": { "error_message": "Invalid CID provided" },
  "request_timestamp": 1762267194.2231338,
  "results": null,
  "sponsor": {
    "link": "...",
    "title": "..."
  },
  "success": false
}
```

---

### Search IPFS Dataset by Hash

Endpoint: `/api/traceix/v1/ipfs/find`

Request type: POST

Data type: JSON

Headers: *(API key not required, but accepted)*

##### NOTE: Uses `sha_hash` to locate which public dataset a file appears in.

#### Example request

```bash
curl -X POST -H "content-type: application/json" \
     --data '{"sha_hash": "FILE_SHA256"}' \
     https://ai.perkinsfund.org/api/traceix/v1/ipfs/find
```

#### Expected outputs

Success:

```json
{
  "data": {
    "file_data": {
      "file_size": "2251",
      "sha_hash": "10b740b968f59cb9d8a4167f72b1773f5e69269b1430a917a6e3e762d1a7526c"
    },
    "metadata": {
      "cid": "...",
      "rid": "..."
    }
  },
  "success": true
}
```

Unsuccessful:

```json
{
  "data": {},
  "success": false
}
```
