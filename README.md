<div align="center">

<img src=".github/logos/perkins_fund_logo.png" alt="" height="250" width="250">

</div>

### Available Tools

- [Traceix](TRACEIX/README.md)  
  Upload files for classification, generate CAPA/EXIF/YARA outputs, and retrieve past results by SHA-256. Includes public IPFS dataset listing and lookup.

- [Traceix Cortex Agents](CORTEX%20AGENTS/README.md)  
  Agent lifecycle + operations: check-in, fetch agent configuration/metadata, run queued analyses, poll job status, and submit/retrieve endpoint-driven alerts (ownership enforced per API key).

- [AURA](AURA/README.md)  
  Lightweight executable triage API: upload a file for a fast verdict, view the latest scans, or search historical scans by SHA-256 (PE/ELF supported; others return `unknown`).

- [Yara Rule Playground](YARA/README.md)  
  Build and validate YARA rules, test them against an uploaded sample, or scan against built-in benign/malware corpora to see hits and iterate quickly.

- [Ransom Note Comparison](RANSOM/README.md)  
  Identify likely ransomware families by ransom note similarity and search for known public decryptor links using keywords.


### Expected API Response Output

##### API BASE URL: `https://ai.perkinsfund.org`

```json
{
  "copyright": "(c) PCEF all rights reserved", (only shown if no API key is passed)
  "error": {
    "error_message": ... (if an error)
  },
  "request_timestamp": 1743098585.390719,
  "results": {
    ... || null
  },
  "sponsor": { (if invalid or no API key provided in request)
    "link": ...,
    "title": ...
  },
  "success": true || false (if an error)
}
```

##### Field descriptions

- `error` – Contains error messages if any occurred. Will be empty dictionary on success.
- `request_timestamp` – UNIX timestamp when the request was received.
- `results` – Contains the results from the received request.
- `sponsor` – A message and link to the sponsor (only shown to users without an API key).
- `success` – A boolean indicating if the request was successfully processed.

