---
name: Search and decrypt Blind Insight records
description: Run searchable-encryption queries over encrypted records and decrypt results through the Blind Proxy.
api: openapi/blindinsight-openapi-original.yml
operations: [token_create, schemas_list, records_search, records_decrypt, records_list]
---

# Search and decrypt Blind Insight records

Blind Insight supports searching over data while it stays encrypted: exact-match on
string fields and range queries on integer/number fields defined in the schema.

## Steps

1. **Authenticate** (`token_create`, then `Authorization: Bearer <access_token>`).
2. **Find the schema** you want to query with `schemas_list` (`GET /api/schemas/`);
   note its `id`/`url`.
3. **Search** with `records_search` (`POST /api/records/search/`), scoping by schema and
   supplying field predicates. String fields match exactly; integer/number fields honor
   the `minimum`/`maximum`/`precision` bounds set on the schema.
4. **Decrypt results** with `records_decrypt` (`POST /api/records/decrypt/`) — the local
   Blind Proxy performs the decryption client-side. A 422 indicates a proxy decrypt failure.
5. To page through all records instead of searching, use `records_list`
   (`GET /api/records/?schema=<id>&limit=&offset=`) with limit/offset pagination.

## Rules

- Queries only work on fields the schema marked as searchable/ranged.
- Decryption requires the appropriate keys in your keyring (see the key-sharing skill).
- Pagination is limit/offset with `count`/`next`/`previous`/`results` (conventions/blindinsight-conventions.yml).
