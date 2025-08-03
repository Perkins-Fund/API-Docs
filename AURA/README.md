# AURA API Documentation

- [File Prediction](#file-prediction-endpoint)
- [Latest Scans](#latest-scans-endpoint)
- [Search Scans](#search-scans-endpoint)

### API BASE URL: `https://ai.perkinsfund.org`

---

### File Prediction Endpoint

Endpoint: `/api/predict`

Request type: POST

Data type: File

Headers (optional): X-Api-Key: API KEY

##### NOTE: The AI currently only classifies ELF and Windows PE executable file formats. Others will be "unknown".

#### Example request

```bash
curl -F"filename=@/path/to/file" https://ai.perkinsfund.org/api/predict
```

#### Expected outputs

Success:
```json
{
  ...,
    "results": {
    "class": "safe",
    "created_at": 1743103981.5979574,
    "file_hash": "ed369187681a62247e38d930320f1cd771756d0b7b67072d8ec655ef99e14aeb",
    "time_taken": 0.37021786579862237
  },
  ...
}
```

Unsuccessful:
```json
{
  ...,
  {
   "error": {
      "error_message": "The message"
   }, 
   ...
}
```

---

### Latest Scans Endpoint

Endpoint: `/api/latest`

Request type: POST

Data type: 

Headers (optional): X-Api-Key: API KEY

##### NOTE: This POST request does not take any parameters, it will return the last 50 scans

#### Example request

```bash
curl -X POST https://ai.perkinsfund.org/api/latest
```

#### Expected outputs

Success:
```json
{
  ...,
  [
    {
      "class": "unknown",
      "created_at": 1743103290.500192,
      "error": {},
      "file_hash": "008632ed4a903dbc95d7a0d042d77df9bf651f10a26a8d4557eb9d2533193ad1",
      "time_taken": 0.0004930980503559113
    },
    {
      "class": "unknown",
      "created_at": 1743099196.4861066,
      "error": {},
      "file_hash": "f5f5ac913edc3fa09bc71ff99d6b84fa63c5987f9713fdb1d55f590ac283b263",
      "time_taken": 0.000505556003190577
    }
  ],
  ...
}
```

Unsuccessful:
```json
{
  ...,
  {
    "error_message": {"error_message": "..."},
    "results": []
  },
  ...
}
```

---

### Search Scans Endpoint

Endpoint: `/api/search`

Request type: POST

Data type: JSON

Headers (required): X-Api-Key: API KEY

##### NOTE: This endpoint requires an API key. You can get an API key here: https://perkinsfund.org/login

#### Example request

```bash
curl -XPOST -H "x-api-key: USER KEY" \
  -H "content-type: application/json" \
  --data '{"sha256":"USER SHA256 HASH"}' \
  https://ai.perkinsfund.org/api/search
```

#### Expected outputs

Success:
```json
{
  ...,
  "results": {
    "class": "safe",
    "created_at": 1742832170.874544,
    "error": {},
    "file_hash": "975d2ab9067a1b21a46ce9d87e6ab589636d128fcba4f49e53392815ebc72d77",
    "time_taken": 0.7284463751129806
  },
  ...
}
```

Unsuccessful:
```json
{
  ...,
  "error": {
    "error_message": "SHA256 hash did not pass heuristics check, is it a hash?" OR "invalid API key supplied"
  },
  ...
}
```
