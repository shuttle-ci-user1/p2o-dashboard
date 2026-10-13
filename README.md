# p2o-dashboard
P2O Analysis Dashboard — rebuilt entirely from Snowflake for report date 12 Oct 2026.

## P2O_Dashboard_Snowflake.html

A self-contained HTML dashboard (no external dependencies; opens directly in
any browser) covering the report date **12-10-2026**:

- **KPI cards** — same-day P2O %, onboarded DEs, same-day paid users, last-week
  average P2O %, best 6-week P2O % (with date).
- **Integrated filters** — report date, segment (India 1 A / 1 B / 2 A / 2 B /
  rise / unclassified), city (top 20), and single-select channel
  (from SNO waterfall CHANNEL values).
- **Daily trend chart** — Overall / by-segment / top-cities P2O % over 6+ weeks
  (SVG, no JS libraries).
- **Segment & city tables** — top 8 cities by payments, with P2O % and share.
- **Working-hours table** — Ameyo call attempts/connects by hour, hour >= 19
  flagged non-working.
- **Contribution-to-drop waterfall** — leads -> onboarded -> from-leads ->
  paid & called -> paid users.
- **Attempts & connects** — by segment with connect rate.
- **Standard disposition analysis** — L2 categories (OCR/Aadhaar not uploaded,
  selfie not uploaded, dedupe, doc not verified, System RNR, zone/RC failure,
  rejoiner, minor, other).
- **Delhi docs** — Aadhaar / Selfie / RC upload and verification rates for
  Delhi payment users.

All numbers are extracted from Snowflake (Swiggy Mumbai):

| Metric | Source |
| --- | --- |
| Payments | `CDC.CDC_DDB.DE_BAZAAR_ORDERS` — first ONLINE payment per `DPHASHEDUSER_ID` with `USER_META:fleet_type = 2` and order status active/completed/shipped/cancelled |
| Same-day onboarding | `DE.SWIGGY.DE_INFO` (`RECORD_TIMESTAMP::DATE` = report date) joined to `DE.SWIGGY.DELIVERY_BOYS`; fleet nicknames indicating Insta/DDE/FTC/Temp/3PL excluded |
| Segments | `ANALYTICS.PUBLIC.CITY_ATTRIBUTES` (`SECONDARY_CLASSIFICATION`) joined via `DE.SWIGGY.CITY` |
| Cities | `DE.SWIGGY.CITY` |
| Leads / channels | `TEMP.PUBLIC.SNO_LEAD_NEW_WATERFALL` (DPH / USER_ID bridge) |
| Calls (working hours, RNR) | `STREAMS.PUBLIC.AMEYO_LEADS_CALL_DETAILS` |
| Doc events (disposition, Delhi docs) | TNS validation events — `STREAMS.PUBLIC.TNS_VALIDATION_REQUEST_DP_EVENT` is access-restricted for the CI role, so the equivalent `ANALYTICS.PUBLIC.TNS_VALIDATION_REQUEST_DP_EVENT_SSU_SUBSET` mirror is used |

Report-date default: 12-10-2026. Data window: 2026-08-31 through 2026-10-12
(6+ weeks of history for benchmarks and trend).
