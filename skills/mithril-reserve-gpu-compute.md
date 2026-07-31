---
name: Reserve GPU compute on Mithril
description: Quote, check availability for, and create a GPU compute reservation, then monitor the resulting instances.
api: openapi/mithril-compute-openapi-original.json
operations:
- get_reservation_instance_types_v2_reservation_instance_types_get
- get_reservation_quote_v2_reservation_quote_get
- get_availability_v2_reservation_availability_get
- create_reservation_v2_reservation_post
- get_instances_v2_instances_get
- get_quotas_v2_quotas_get
---

# Reserve GPU compute on Mithril

Provision reserved GPU capacity (e.g. A100/H100/H200) on the Mithril omnicloud.

## Auth
- Base URL: `https://api.mithril.ai/v2`
- Header: `Authorization: Bearer fkey_<key>` (create/manage keys under Compute API > API Keys).

## Steps
1. **Check quota** — call `get_quotas_v2_quotas_get` to confirm the project can add the desired GPU count.
2. **Pick an instance type** — call `get_reservation_instance_types_v2_reservation_instance_types_get` for reservable GPU types + specs.
3. **Check availability** — call `get_availability_v2_reservation_availability_get` for the instance type, quantity, region, and time window.
4. **Quote** — call `get_reservation_quote_v2_reservation_quote_get` to get the price before committing.
5. **Create** — call `create_reservation_v2_reservation_post` with the instance type, quantity, region, and start/end time. Capture the returned reservation `fid`.
6. **Watch instances** — poll `get_instances_v2_instances_get` until the reservation's instances reach a ready status.

## Rules
- Identifiers are opaque `fid` strings; never construct them.
- No idempotency key is supported — do not blindly retry `create_reservation` on a network timeout; re-list reservations first to avoid duplicates.
- Errors: 401 invalid credentials, 400 invalid/precondition (e.g. reservation already ended), 404 not found, 422 validation (`detail[]`), 501 unsupported. See `errors/mithril-problem-types.yml`.
