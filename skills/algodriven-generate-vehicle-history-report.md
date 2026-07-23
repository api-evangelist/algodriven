---
name: Generate and retrieve a UAE vehicle history report
description: >-
  Generate a GCC/UAE vehicle history report for a VIN with AlgoDriven, then poll
  for the finished PDF report URL.
api: openapi/algodriven-vehicle-history-openapi.yml
operations:
  - generateGccHistoryReport
  - retrieveGccHistoryReport
generated: '2026-07-17'
method: generated
---

# Generate and retrieve a UAE vehicle history report

Use AlgoDriven's Vehicle History Report - UAE API to produce a PDF history
report for a vehicle by VIN. Generation is asynchronous, so this is a
two-step generate-then-poll flow.

## Authentication

Send your AlgoDriven API key in the `Authorization` header on every request.
The base host is `https://algodriven.io`.

## Steps

1. **Generate the report.** Call `generateGccHistoryReport`
   (`POST /v1/generateGccHistoryReport`) with a `multipart/form-data` body
   containing `vin` (the 17-character VIN). Capture the `reportId` from the
   response.

2. **Poll for the report.** Call `retrieveGccHistoryReport`
   (`POST /v1/retrieveGccHistoryReport`) with a `multipart/form-data` body
   containing the `reportId`. Repeat at **five second intervals** until a
   report `url` is returned. Generation can take up to **sixty seconds**, so
   allow at least a dozen poll attempts before treating it as failed.

3. **Deliver the PDF.** Once `retrieveGccHistoryReport` returns a `url`, that
   is the link to the generated PDF report; hand it to the user or download it.

## Rules and conventions

- Auth: API key in the `Authorization` header (see
  `authentication/algodriven-authentication.yml`).
- Async: do not expect the report on the generate call; always poll retrieve
  (see `conventions/algodriven-conventions.yml`).
- No idempotency-key contract is documented; avoid firing duplicate generate
  calls for the same VIN in a tight loop.
- Requests use `multipart/form-data`, not JSON bodies.
