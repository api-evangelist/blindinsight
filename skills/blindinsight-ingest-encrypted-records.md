---
name: Ingest encrypted records into Blind Insight
description: Authenticate, define a schema, and ingest records that are encrypted client-side by the Blind Proxy, monitoring the job to completion.
api: openapi/blindinsight-openapi-original.yml
operations: [token_create, datasets_create, schemas_create, records_ingest, jobs_upload, jobs_status, jobs_websocket]
---

# Ingest encrypted records into Blind Insight

All record data is encrypted **client-side by the local Blind Proxy** before it reaches
the API. Point requests at the proxy (`http://localhost:8080`) or the cloud API
(`https://api.app.blindinsight.io`).

## Steps

1. **Authenticate.** For production use JWT: call `token_create` (`POST /api/token/`)
   with your credentials to obtain an access token, then send
   `Authorization: Bearer <access_token>`. HTTP Basic
   (`Authorization: Basic <base64(email:password)>`) also works and is the default
   for the local proxy.
2. **Create a dataset** with `datasets_create` (`POST /api/datasets/`) to hold your schemas.
3. **Define a schema** with `schemas_create` (`POST /api/schemas/`). The root type must
   be `"object"`. Integer fields require `minimum`/`maximum`; number fields also require
   `precision`. These bounds **cannot be changed after records are ingested** without
   re-encrypting.
4. **Ingest.** For up to 100 records use `records_ingest` (`POST /api/records/ingest/`)
   with an array of `{schema, data}` objects. For larger batches use `jobs_upload`
   (`POST /api/jobs/upload/`), which returns `X-Job-ID`.
5. **Monitor the job** with `jobs_status` (`GET /api/jobs/{job_id}/`) or subscribe to
   `jobs_websocket` (`GET /api/ws/jobs/{job_id}/`) for `status`/`processed`/`total`
   progress until `status` is `completed`.

## Rules

- Max 100 records per `records_ingest` call (HTTP 413 otherwise).
- 422 means the Blind Proxy failed to encrypt/ingest — check the proxy is running.
- Errors are `application/json` (see errors/blindinsight-problem-types.yml).
