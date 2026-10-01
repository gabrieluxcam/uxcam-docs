---
title: Data Deletion API
excerpt: >-
  Programmatically erase users and sessions to honour GDPR and CCPA deletion
  requests
deprecated: false
hidden: false
metadata:
  title: Data Deletion API - UXCam
  description: >-
    REST API for programmatic erasure of UXCam users and sessions (GDPR Art. 17
    / CCPA)
  robots: index
next:
  description: ''
---

# Data Deletion API

Programmatic erasure of users and sessions, for privacy services that need to honour GDPR Art. 17 / CCPA requests without a person in the loop.

Deletion happens on **two clocks**. The moment we accept a request, the subject disappears from every dashboard, export and API. That is the part that satisfies an erasure request, and it is immediate. The physical purge of stored records and media follows and is quoted separately in the response.

The API therefore answers `202 Accepted` with `status: "suppressed"`, never `"deleted"`.

> ❗️ **Deletions are permanent and cannot be undone**
>
> Anyone holding the Deletion Key can erase this app's data without dashboard access. Store it as you would any production secret.

---

## Base URL

```
https://api.uxcam.com
```

All endpoints are served under `/v2/`, matching the [Data Access API](/docs/data-access-api), so an integration using both wires up one prefix, not two.

---

## Authentication

| Parameter | Where it goes | Description |
|-----------|---------------|-------------|
| `appid` | Query string | The application identifier |
| Deletion Key | `Authorization: Bearer <deletionApiKey>` header | Secret Data Deletion API key |

The Deletion Key is a secret. Send it only in the `Authorization` header, never in the URL or query string. A delete credential in a URL ends up in access logs, proxy logs and browser history.

Requests are rejected with `401` when the key is missing or wrong, and `403` when deletion is not enabled for the app.

### Getting a Deletion Key

UXCam Dashboard → **App Settings** → **Data deletion API** → **Generate Deletion Key**. Owner or admin only.

Deletion is **off by default** on every app and does nothing until a key is generated. Rotating or revoking the key breaks the old one immediately.

---

## Make Your First Request

```bash
curl -X POST 'https://api.uxcam.com/v2/deletion?appid=<appId>' \
  -H 'Authorization: Bearer <deletionApiKey>' \
  -H 'Content-Type: application/json' \
  -d '{
    "subjectType": "user",
    "selector": {
      "kind": "identifiers",
      "identifiers": [
        { "uxcamUserId": ["62a98b8aa5cb75001a839922"] }
      ]
    },
    "reason": "GDPR erasure request"
  }'
```

---

## Response Envelope

Every response carries the same envelope.

```json
{ "success": true, "data": { } }
```

```json
{ "success": false, "error": { "message": "...", "status": 400 } }
```

A `400` adds `error.detail`, naming the offending field. Because this endpoint is destructive, unknown fields are **rejected rather than ignored** at every level of the payload. A misspelled or misplaced flag is a `400`, never a silent no-op.

---

## Rate Limits

- Up to **5 requests per second**
- Up to **500 requests per hour**
- **2000 identifiers per request**, with **50** recommended
- An identical payload resubmitted within **7 days** is treated as a duplicate

**There is no daily or monthly quota.** The only ceilings are the two request rates above and the 2000 identifiers allowed in any one request. Erasure volume is driven by your users exercising a legal right, not by us. At 500 requests an hour carrying 2000 identifiers each, the hourly ceiling is a million identifiers.

The rates are counted against your Deletion Key in its own bucket, so a burst of deletions cannot throttle your analytics reads, and vice versa.

---

## Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/v2/deletion?appid=<appId>` | Submit users or sessions for erasure. `subjectType` decides which |
| `POST` | `/v2/deletion/status?appid=<appId>` | Check the progress of a submitted request |

---

## Delete Users

### Request

```bash
curl -X POST 'https://api.uxcam.com/v2/deletion?appid=<appId>' \
  -H 'Authorization: Bearer <deletionApiKey>' \
  -H 'Content-Type: application/json' \
  -d '{
    "subjectType": "user",
    "selector": {
      "kind": "identifiers",
      "identifiers": [
        { "uxcamUserId": ["62a98b8aa5cb75001a839922"] },
        { "userId": ["acct-1042", "acct-1043"] }
      ]
    },
    "reason": "GDPR erasure request"
  }'
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `subjectType` | string | yes | `user` or `session` |
| `selector.kind` | string | yes | `identifiers`, the only kind available today |
| `selector.identifiers` | array | yes | One object per identifier field. **Each value is a list, and each field appears once.** |
| `reason` | string | no | Free text, kept for audit only |

Which identifier fields are valid depends on `subjectType`:

| `subjectType` | Accepted fields |
|---------------|-----------------|
| `user` | `uxcamUserId`, `userId` |
| `session` | `sessionId` |

`userId` is your own account identifier, resolved against the user you sent it for (via `setUserIdentity`), so a privacy service can name its own subjects without knowing ours.

One identifier is still a list of one. The shape does not change with the size of the batch.

#### Idempotency

An optional `Idempotency-Key` header replays the original acknowledgement rather than deleting twice, for **24 hours**. Reusing one key for a *different* payload returns `422`.

After that window the payload itself still protects you: an identical request inside the 7-day duplicate window returns `200` with `status: "duplicate"` even if the key has expired or changed.

### Response

`202 Accepted`

```json
{
  "success": true,
  "data": {
    "requestId": "dr_1111111111111111111111111111aaaa",
    "status": "suppressed",
    "appId": "a11a11a11a11a11a11a11a11",
    "subjectType": "user",
    "received": 2,
    "accepted": 1,
    "notFound": 1,
    "duplicates": 0,
    "identifiers": [
      { "uxcamUserId": "b22b22b22b22b22b22b22b22", "status": "accepted" },
      { "userId": "acct-1042", "status": "not_found" }
    ],
    "visibleRemoval": "immediate",
    "erasureExpectedBy": "2026-09-15T12:55:05.999000Z"
  }
}
```

> 📘 **Keep the `requestId`**
>
> It is the only way to check progress later. A `Location` header in the response points at the status endpoint.

An identical payload resubmitted within the duplicate window returns `200` with `status: "duplicate"` and the original `requestId`. That is not an error: from your side the deletion is already done.

---

## Delete Sessions

Same endpoint and same body. `subjectType` is `session`, and the only accepted identifier field is `sessionId`.

### Request

```bash
curl -X POST 'https://api.uxcam.com/v2/deletion?appid=<appId>' \
  -H 'Authorization: Bearer <deletionApiKey>' \
  -H 'Content-Type: application/json' \
  -d '{
    "subjectType": "session",
    "selector": {
      "kind": "identifiers",
      "identifiers": [
        { "sessionId": ["62a98b8aa5cb75001a839944", "62a98b8aa5cb75001a839945"] }
      ]
    },
    "reason": "CCPA erasure request"
  }'
```

### Response

`202 Accepted`

```json
{
  "success": true,
  "data": {
    "requestId": "dr_2222222222222222222222222222bbbb",
    "status": "suppressed",
    "appId": "a11a11a11a11a11a11a11a11",
    "subjectType": "session",
    "received": 2,
    "accepted": 2,
    "notFound": 0,
    "duplicates": 0,
    "identifiers": [
      { "sessionId": "62a98b8aa5cb75001a839944", "status": "accepted" },
      { "sessionId": "62a98b8aa5cb75001a839945", "status": "accepted" }
    ],
    "visibleRemoval": "immediate",
    "erasureExpectedBy": "2026-09-15T12:55:05.999000Z"
  }
}
```

Deleting a session removes that recording and its media only. The user's other sessions are untouched. To erase the person, delete the user.

`sessionId` under `subjectType: "user"` is rejected with `400`, and so is `uxcamUserId` under `subjectType: "session"`. The identifier field must match the subject.

---

## Check Status

### Request

```bash
curl -X POST 'https://api.uxcam.com/v2/deletion/status?appid=<appId>' \
  -H 'Authorization: Bearer <deletionApiKey>' \
  -H 'Content-Type: application/json' \
  -d '{"requestId": "dr_1111111111111111111111111111aaaa"}'
```

Request ids are scoped to the app that created them. Asking another app about one returns `404`.

### Response

`200 OK`

```json
{
  "success": true,
  "data": {
    "requestId": "dr_1111111111111111111111111111aaaa",
    "appId": "a11a11a11a11a11a11a11a11",
    "subjectType": "session",
    "status": "purged",
    "counts": { "received": 1, "accepted": 1, "notFound": 0, "duplicates": 0 },
    "progress": {
      "sessionsPurged": 1,
      "usersPurged": 0,
      "rowsDeleted": 2
    },
    "queued": false,
    "visibleRemoval": "immediate",
    "liveDataDeleted": true,
    "analyticsLakePurge": "scheduled",
    "submittedAt": "2026-09-08T12:55:07.381000Z",
    "erasureExpectedBy": "2026-09-15T12:55:05.999000Z"
  }
}
```

`progress` counts what was erased. `rowsDeleted` is the total across our live stores; `sessionsPurged` and `usersPurged` count the subjects themselves. They are populated once the purge runs, so a request still at `suppressed` reports an empty `progress`.

---

## Deletion Attributes

### Identifier Dispositions

One entry per identifier you submitted, so you can tell exactly which ids we acted on. Grouping the request does not group the answer: you always learn *which* id was not found.

| Status | Meaning |
|--------|---------|
| `accepted` | Matched a record; suppressed now, purge queued |
| `not_found` | No such identifier in this app. Not an error, the batch still succeeds |
| `duplicate` | Already tombstoned by an earlier request; nothing further to do |

### Request Status

| `status` | Meaning |
|----------|---------|
| `suppressed` | Invisible everywhere; purge queued |
| `purging` | Physical removal in progress |
| `purged` | Live records and media are gone |
| `partial` | Some targets purged, others outstanding; the request is still being retried |
| `erased` | Analytics-lake copies are gone too, and we hold proof |
| `unverified` | Purged, but the analytics-lake evidence could not be confirmed |
| `failed` | The purge did not complete. Contact support. The subject remains suppressed throughout |

`analyticsLakePurge` reads `scheduled`, then `confirmed` or `unconfirmed`.

### Error Codes

| Code | Meaning |
|------|---------|
| `400` | Invalid payload: unknown `subjectType`, an identifier field that does not match it, more than 2000 identifiers, or a reserved selector kind |
| `401` | Missing or wrong `Authorization: Bearer` deletion key |
| `403` | Deletion is not enabled for this app, or this is a UXCam demo app |
| `404` | *(status only)* No such deletion request for this app |
| `422` | This `Idempotency-Key` was already used for a different payload |
| `429` | Rate limited. Retry after the `Retry-After` interval |
| `503` | Nothing was recorded and nothing was deleted. Safe to retry |

---

## Notes

> 🚧 **Stop recording erased users**
>
> If a user keeps using your app after requesting erasure, the SDK keeps sending their sessions and that new data arrives after we have processed the deletion. Stop starting UXCam for that user from the point they ask. See [Opt-in / Opt-out](/docs/opt-in-opt-out).

- **Deletion is off by default** on every app and stays off until someone generates a Deletion Key.
- **Demo apps are refused.** Their data is shared and their keys are published in our documentation.

---

## See Also

- [Data Access API](/docs/data-access-api) - Query and export sessions, users and events
- [Privacy and Compliance](/docs/privacy-and-compliance) - GDPR/CCPA handling