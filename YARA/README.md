# Yara Rule Playground API documentation

- [Build Yara Rule](#building-yara-rules-endpoint)
- [Test Yara Rule](#testing-yara-rules-endpoint)
- [Scan with Yara Rule](#scanning-yara-rules-endpoint)

### API BASE URL: `https://ai.perkinsfund.org`

---

### Building Yara Rules Endpoint

Endpoint: `/api/yara/build`

Request type: POST

Data type: JSON

Headers (optional): X-Api-Key: `API KEY`

##### NOTE: Compiles the provided Yara rule and returns whether the rule is valid or not.

#### Example request

```bash
curl -X POST -H "Content-Type: application/json" \
   -d '{"rule": "rule dummy { condition: true }"}' \
   https://ai.perkinsfund.org/api/yara/build
```

#### Expected outputs

Success:
```json
{
  ...,
   "results": {
    "success": "YARA rule compiled successfully"
  },
  ...
}
```

Unsuccessful:
```json
{
  ...,
  "error": {
    "error_message": "YARA rule syntax error: line 1: syntax error, unexpected identifier, expecting  <condition>"
  },
   ...
}
```

---

### Testing Yara Rules Endpoint

Endpoint: `/api/yara/test`

Request type: POST

Data type: multipart/form-data

Headers (optional): X-Api-Key: `API KEY`

##### NOTE: Tests a Yara rule against a provided uploaded file.

#### Example request

```bash
curl -F "yara_rule=@rule.yar" \
  -F "test_file=@sample.exe" \
  https://ai.perkinsfund.org/api/yara/test
```

#### Expected outputs

Success:
```json
{
  ...,
  "results": [
    {
      "matched_strings": [],
      "rule": "test"
    }
  ],
  ...
}
```

Unsuccessful:
```json
{
  ...,
  "error": {
    "error_message": "YARA rule syntax error: line 2: syntax error, unexpected identifier, expecting <condition>" OR "No matches found using YARA rule"
  }
   ...
}
```

---

### Scanning Yara Rules Endpoint

Endpoint: `/api/yara/scan`

Request type: POST

Data type: JSON

Headers (optional): X-Api-Key: `API KEY`

Headers (required): X-File-Type: benign or malware

##### NOTE: Runs the provided Yara rule against built-in files of the specific type specified in the X-File-Type header.

#### Example request

```bash
curl -X POST -H "Content-Type: application/json" \
  -H "x-file-type: benign" \
  -d '{"rule": "rule dummy { condition: true }"}' \
  https://ai.perkinsfund.org/api/yara/scan
```

#### Expected outputs

Success:
```json
{
  ...,
  "results": [
    "4c2765686236234db7693c622373c44cda172536f0066820fed70a1ca86519dd",
    "6312581860b1873dedeb8c2cd916fb27baa061f62b0cde44167f4cefebe0628a",
    "862588b2f549abd71bc219dab6e3627ec792cefd2ee6ff0857ccc4fd2e552cc6",
    "8109d3330a58d934fc0bf989b06ca612a317e2085f6477a8b8f8296b452d1ed0",
    "a4940a198a5d2ef2d68e3d643058ed1ee123ce3f8ef6e9e923360d80f81b684d",
    "52f95f4a253c431221852fa0e0fb9ab94752fdee597750949460c74242887b09",
    "d1b807df597b47d162d7235d8389e366d24a2cc2e379f48bebbe7855f420e660",
    ... 
  ],  
  ...
}
```

Unsuccessful:
```json
{
  ...,
  "error": {
    "error_message": "YARA rule syntax error: line 2: syntax error, unexpected identifier, expecting <condition>" OR "No matches found for YARA rule in 50 samples"
  }
   ...
}
```

