---
name: Manage Mithril storage volumes and access keys
description: Create SSH keys and API keys, and provision persistent storage volumes for compute workloads.
api: openapi/mithril-compute-openapi-original.json
operations:
- create_api_key_v2_api_keys_post
- create_ssh_key_v2_ssh_keys_post
- get_ssh_keys_v2_ssh_keys_get
- create_volume_v2_volumes_post
- get_volumes_v2_volumes_get
- delete_volume_v2_volumes__volume_fid__delete
---

# Manage Mithril storage volumes and access keys

Set up the access + storage prerequisites for running GPU workloads.

## Auth
- Base URL: `https://api.mithril.ai/v2`
- Header: `Authorization: Bearer fkey_<key>`

## Steps
1. **API key** — call `create_api_key_v2_api_keys_post` to mint a programmatic key (returns the full `fkey_...` once; store it securely).
2. **SSH key** — call `create_ssh_key_v2_ssh_keys_post` to register a public key (or generate one); list with `get_ssh_keys_v2_ssh_keys_get`. Instances use these for SSH access.
3. **Volume** — call `create_volume_v2_volumes_post` with a name, region, `capacity_gb`, and interface (file share / block). Capture the volume `fid`.
4. **Attach** — reference the volume when creating a bid or reservation so it mounts to the instances (volumes carry `bids`/`reservations`/`attachments`).
5. **List / clean up** — `get_volumes_v2_volumes_get` to inventory; `delete_volume_v2_volumes__volume_fid__delete` to remove.

## Rules
- The API key secret is shown only at creation time.
- Storage is billed per GB/month; a volume's region must match the compute region.
- Errors: 401, 400, 404, 422 validation. See `errors/mithril-problem-types.yml`.
