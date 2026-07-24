---
name: Share keys and grant access in Blind Insight
description: Register an identity, grant a user or team access to encrypted schemas, and exchange key material so they can decrypt.
api: openapi/blindinsight-openapi-original.yml
operations: [identities_create, grants_create, requests_create, requests_fulfill-list, materials_receive-list, materials_acknowledge_create]
---

# Share keys and grant access in Blind Insight

Because data is end-to-end encrypted, giving another principal access means sharing key
**material** to their cryptographic **identity**, not just setting a permission flag.

## Steps

1. **Register the recipient's identity** with `identities_create`
   (`POST /api/identities/`) so their public key/device is known.
2. **Grant access** with `grants_create` (`POST /api/grants/`) to authorize a user or
   team on a schema/dataset.
3. **Request or fulfill key material.** A recipient can open a request with
   `requests_create` (`POST /api/requests/`); an owner fulfills pending requests with
   `requests_fulfill-list` (`POST /api/requests/fulfill/`).
4. **Deliver material** to the recipient via `materials_receive-list`
   (`POST /api/materials/receive/`), and have them confirm with
   `materials_acknowledge_create` (`POST /api/materials/{id}/acknowledge/`).

## Rules

- Grants control authorization; key material controls the ability to actually decrypt —
  both are required.
- All calls require authentication (JWT Bearer recommended).
- Organizations/teams scope who can be granted (see data-model/blindinsight-data-model.yml).
