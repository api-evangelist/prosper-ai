---
name: Export call logs and outcomes from a Prosper campaign
description: Retrieve, page through, and drill into Prosper Voice call logs (transcripts, QA results, outcomes) and monitor in-progress calls.
api: openapi/prosper-ai-voice-openapi.json
base_url: https://voice.getprosperapp.com
auth: API key (header) issued from the Prosper platform API Keys section
operations:
  - list_call_logs_api_v1_call_logs_get
  - get_call_log_api_v1_call_logs__call_log_id__get
  - get_live_call_api_v1_live_calls_get
---

# Export call logs and outcomes from a Prosper campaign

Use this skill to pull post-call data for analysis or to watch a call live.

## Steps

1. **List call logs.**
   `GET /api/v1/call-logs`
   (`list_call_logs_api_v1_call_logs_get`) with query params `campaign_id`,
   `page`, `limit`, `from_date`, `to_date`. The response is paginated
   (`items`, `total`, `page`, `page_size`); loop until you have all pages.

2. **Get full detail for a call.**
   `GET /api/v1/call-logs/{call_log_id}`
   (`get_call_log_api_v1_call_logs__call_log_id__get`) returns the full
   `CallLogExportInformation` — transcript, metadata, QA and outcome. Set
   `include_recordings=true` only when you need audio.

3. **Monitor an in-progress call (optional).**
   `GET /api/v1/live-calls`
   (`get_live_call_api_v1_live_calls_get`) returns a flat snapshot of a live call,
   looked up by `call_log_id`, `user_phone_number`, or a `key`/`value` pair.

## Rules

- Authenticate every request with your API key header (see
  `authentication/prosper-ai-authentication.yml`).
- Use `from_date`/`to_date` to bound large exports rather than paging the entire
  history each run.
- Validation failures return **422** with the `{"detail":[...]}` envelope
  (see `errors/prosper-ai-problem-types.yml`).
- Call-outcome classification lives in the subcategory enums (HumanOnly, IVRLoop,
  InputDataIssue, NotFound, ServiceNotAvailable, Transfer, UnprocessableByPhone) —
  read these to understand why a call did not complete.
