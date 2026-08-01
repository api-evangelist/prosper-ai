---
name: Add targets and launch calls in a Prosper campaign
description: Add contact targets to an existing Prosper Voice campaign and execute the outbound phone calls, then confirm the targets landed.
api: openapi/prosper-ai-voice-openapi.json
base_url: https://voice.getprosperapp.com
auth: API key (header) issued from the Prosper platform API Keys section
operations:
  - get_required_variables_api_v1_campaigns__campaign_id__variables_get
  - add_targets_api_v1_campaigns__campaign_id__targets_post
  - campaign_calls_api_v1_campaigns__campaign_id__calls_post
  - get_campaign_targets_api_v1_campaigns__campaign_id__targets_get
---

# Add targets and launch calls in a Prosper campaign

Use this skill to load contacts into a Prosper Voice campaign and place the calls.
You need an existing `campaign_id` and an API key from the Prosper platform.

## Steps

1. **Discover the required variables.**
   `GET /api/v1/campaigns/{campaign_id}/variables`
   (`get_required_variables_api_v1_campaigns__campaign_id__variables_get`) returns
   the JSON of variables each target must supply for this campaign. Build every
   target payload to satisfy these.

2. **Add the targets.**
   `POST /api/v1/campaigns/{campaign_id}/targets`
   (`add_targets_api_v1_campaigns__campaign_id__targets_post`) with an
   `AddTargetsRequest` body. Each target carries its phone number and the required
   input data from step 1.

3. **Execute the calls.**
   `POST /api/v1/campaigns/{campaign_id}/calls`
   (`campaign_calls_api_v1_campaigns__campaign_id__calls_post`) to launch calling
   for the campaign's targets.

4. **Verify.**
   `GET /api/v1/campaigns/{campaign_id}/targets`
   (`get_campaign_targets_api_v1_campaigns__campaign_id__targets_get`), paginating
   with `page`/`limit` and filtering by `status`, to confirm the targets are queued.

## Rules

- Authenticate every request with your API key header (see
  `authentication/prosper-ai-authentication.yml`).
- A malformed body returns **422** with a `{"detail":[{loc,msg,type}]}` envelope
  (see `errors/prosper-ai-problem-types.yml`) — fix the flagged field and retry.
- No idempotency key is supported; do not blindly re-POST `add_targets` on a
  network error before checking `get_campaign_targets` for what already landed.
