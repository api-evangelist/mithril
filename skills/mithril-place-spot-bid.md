---
name: Place a spot GPU bid on Mithril
description: Check spot auction availability, place a price-limited spot bid, and monitor or cancel it.
api: openapi/mithril-compute-openapi-original.json
operations:
- get_auctions_v2_spot_availability_get
- create_bid_v2_spot_bids_post
- get_bid_v2_spot_bids__bid_fid__get
- get_bid_status_v2_spot_bids__bid_fid__status_get
- cancel_bid_v2_spot_bids__bid_fid__delete
---

# Place a spot GPU bid on Mithril

Run asynchronous workloads on interruptible spot capacity with a price ceiling.

## Auth
- Base URL: `https://api.mithril.ai/v2`
- Header: `Authorization: Bearer fkey_<key>`

## Steps
1. **Survey auctions** — call `get_auctions_v2_spot_availability_get` to see spot capacity and current clearing prices by instance type/region.
2. **Place bid** — call `create_bid_v2_spot_bids_post` with instance type, quantity, region, a `limit_price`, and a launch specification. Capture the bid `fid`.
3. **Poll status** — call `get_bid_status_v2_spot_bids__bid_fid__status_get` (or `get_bid_v2_spot_bids__bid_fid__get`) until instances are allocated.
4. **Handle interruptions** — spot instances can be reclaimed; design the workload to checkpoint (see docs: handling-spot-interruptions).
5. **Cancel** — call `cancel_bid_v2_spot_bids__bid_fid__delete` to release the bid and terminate its instances.

## Rules
- Set `limit_price` deliberately — you pay up to it as the market moves.
- `fid` identifiers are opaque.
- Errors: 401, 400, 404, 409 (not-ready), 501 (spot not yet supported for some paths), 422 validation. See `errors/mithril-problem-types.yml`.
