# Ransom Note Comparison

- [Compare Notes](#compare-notes)
- [Search for Decryptors](#latest-scans-endpoint)

### API BASE URL: `https://ai.perkinsfund.org`

---

### Compare Notes

Endpoint: `/api/tool/ransomnotes`

Request type: POST

Data type: File

Headers (optional): X-Api-Key: API KEY

##### NOTE: It is possible to get more than one item per list

#### Example request

```bash
curl -F"file=@FILENAME" https://ai.perkinsfund.org/api/tool/ransomnotes
```

#### Expected outputs

Success:
```json
{
  ...
  "results": [
    [
      "8base",
      "91%"
    ]
  ],
  ...
}
```

Unsuccessful:
```json
{
  ...
  "error": {
    "error_message": "MESSAGE"
  },
  ...
}
```

---

### Search Decryptors

Endpoint: `/api/tool/ransomnotes/links`

Request type: POST

Data type: JSON

Headers (optional): X-Api-Key: API KEY

##### NOTE: It is possible to get more than one item per list

#### Example request

```bash
curl -XPOST -H "content-type: application/json" --data '{"search_term": "akira"}' https://ai.perkinsfund.org/api/tool/ransomnotes/links
```

#### Expected outputs

Success:
```json
{
  ...
  "results": [
    "https://files.avast.com/files/decryptor/avast_decryptor_akira64.exe"
  ],
  ...
}
```

Unsuccessful:
```json
{
  ...
  "error": {
    "error_message": "No search term specified?"
  },
  ...
}
```