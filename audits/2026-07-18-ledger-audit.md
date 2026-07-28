# Ledger evidence audit — 2026-07-18 (INC-010)

Every resolution in `Pythia/runs/ledger.jsonl` as of 2026-07-18 ~12:30 UTC, audited
against the archived briefs (the judge's evidence base). `label`: ok = evidence
concerns the claim and supports the verdict · weak = topically related but not
establishing the claim · mismatch = different subject/place or nonexistent signal ·
unverified = plausible but appears nowhere in the archive.

Revised verdicts were produced by the hardened judge (engine/relevance.py filter +
numbered-citation gate, temp 0) over archived in-window signals, then adjudicated
case-by-case; 10 judge YESes based on status/alert-level signals were overruled to
void (a status line is not an event). Voided = verdict `unresolved`, excluded from
the scorecard.

| # | id | p | verdict | label | revised → | claim | original evidence (truncated) |
|---|----|---|---------|-------|-----------|-------|-------------------------------|
| 0 | pred_20fed9784b | 0.25 | no | ok | (kept) | A Category 2 hurricane will make landfall in the Gulf Coast region within the ne | There is no mention of a Category 2 hurricane making landfall in the Gulf Coast region wit |
| 1 | pred_4838892625 | 0.65 | yes | weak | VOID | A major oil tanker will be delayed through the Bab el-Mandeb Strait due to rough | Chokepoint CRITICAL: Bab el-Mandeb — Bab el-Mandeb — 6.2M bpd oil / LIVE SHIPS: 0. |
| 2 | pred_954cba5c23 | 0.20 | yes | mismatch | VOID | A Category 1 (120 km/h) tropical cyclone will make landfall in the Philippines w | The current world snapshot includes a green notification for tropical cyclone ELIDA-26, in |
| 3 | pred_90cb4a9c62 | 0.61 | yes | unverified | VOID | A flash flood warning will be issued for the Houston/Galveston TX area | A Flash Flood Warning was issued for the Houston/Galveston TX area on July 15, matching th |
| 4 | pred_f7fcefd0a6 | 0.35 | yes | mismatch | VOID | A tropical cyclone will make landfall in Vietnam within the next 24 hours | Green notification for tropical cyclone ELIDA-26 indicates a tropical cyclone made landfal |
| 5 | pred_55fbc0b586 | 0.60 | yes | mismatch | VOID | A severe thunderstorm warning will be issued for the state of New York | Green notification for tropical cyclone ELIDA-26: Population affected by Category 1 (120 k |
| 6 | pred_dea8545f6f | 0.38 | yes | mismatch | VOID | A severe thunderstorm warning will be issued for the New York City metropolitan  | A green notification for a severe thunderstorm warning is mentioned in the Current World S |
| 7 | pred_c14c89f264 | 0.45 | yes | weak | VOID | Tropical Storm ELIDA-26 will make landfall in Mexico. | Green notification for tropical cyclone ELIDA-26 indicates a population affected by Catego |
| 8 | pred_118b129fa5 | 0.23 | no | ok | (kept) | A major cyberattack will occur on a US government agency. | There is no mention of a major cyberattack on a US government agency in the provided signa |
| 9 | pred_da18a0565b | 0.50 | yes | mismatch | VOID | A magnitude 5.0 earthquake will occur in the Mariana Islands region | A green earthquake notification with a magnitude of 5.9M in New Zealand was recorded on 20 |
| 10 | pred_8d725539eb | 0.73 | no | ok | (kept) | A M4.5+ earthquake will strike the Mariana Islands region | There is no mention of a M4.5+ earthquake striking the Mariana Islands region in the curre |
| 11 | pred_7ee8013c66 | 0.80 | yes | weak | VOID | Flash flooding will occur in the Phoenix metropolitan area | A Flash Flood Warning issued by NWS Tucson AZ on 2026-07-16 at 2:51PM MST until 5:45PM MST |
| 12 | pred_ca9fe5b2b7 | 0.51 | yes | mismatch | VOID | A magnitude 6.0 earthquake will occur in the Mariana Islands region | A magnitude 6.0 earthquake in the Mariana Islands region is reported as a M5.2 — 48 km E o |
| 13 | pred_360ed4bd26 | 0.43 | no | ok | (kept) | A M6.5 earthquake will strike off the coast of Japan within the next 24 hours | There is no mention of a M6.5 earthquake striking off the coast of Japan within the next 2 |
| 14 | pred_e12d91fe28 | 0.43 | yes | weak | VOID | Flash flooding will occur in the Grand Canyon area of Arizona, USA | Flash Flood Warning issued July 16 at 2:51PM MST until July 16 at 5:45PM MST by NWS Tucson |
| 15 | pred_d322a07d62 | 0.23 | yes | mismatch | VOID | A magnitude 6.0 earthquake will occur in the South Sandwich Islands within the n | A Green earthquake (Magnitude 6.0M, Depth: unknown) in South Sandwich Islands occurred on  |
| 16 | pred_15c31dacda | 0.70 | no | ok | (kept) | A M4.0 earthquake will occur within 24 hours of the current time in Alaska | There is no mention of a M4.0 earthquake occurring in Aleknagik, Alaska within the window. |
| 17 | pred_64c39eea7e | 0.28 | no | ok | (kept) | A Category 3 hurricane will make landfall in the southeastern United States with | There is no mention of a Category 3 hurricane making landfall in the southeastern United S |
| 18 | pred_276eb7d27d | 0.58 | no | ok | (kept) | A magnitude 6.5 earthquake will strike off the coast of Fiji within the next 24  | There is no mention of a magnitude 6.5 earthquake striking off the coast of Fiji within th |
| 19 | pred_79dbb89344 | 0.42 | yes | weak | VOID | A major flood will occur in Germany | A Flash Flood Warning was issued for Germany on July 16, indicating a major flood occurred |
| 20 | pred_bf36d3652c | 0.10 | no | ok | (kept) | A major earthquake will occur within the next 24 hours in the region around the  | There is no mention of a major earthquake occurring in the Strait of Hormuz within the nex |
| 21 | pred_62fda91e67 | 0.33 | yes | mismatch | VOID | A 4.5 magnitude earthquake will strike off the coast of Northern California | The forecast stated a 4.5 magnitude earthquake, but there is no mention of it in the CURRE |
| 22 | pred_571c6a4752 | 0.43 | yes | mismatch | no | A major cyberattack will hit a European government's IT infrastructure, cripplin | An Orange earthquake (Magnitude 7.3M, Depth:15.178km) in Mexico occurred on 17/07/2026 14: |
| 23 | pred_7ceeda76eb | 0.43 | no | ok | (kept) | A Category 3 hurricane will make landfall in the southeastern United States with | The forecast stated a Category 3 hurricane would make landfall in the southeastern United  |
| 24 | pred_11eec93d8f | 0.68 | no | ok | (kept) | A major oil tanker will be delayed in transit through the Bab el-Mandeb Strait | The forecast stated a major oil tanker would be delayed in transit through the Bab el-Mand |
| 25 | pred_89d185d394 | 0.08 | no | ok | (kept) | A major cyberattack will occur on a US hospital's computer system | There is no mention of a major cyberattack on a US hospital's computer system in the CURRE |
| 26 | pred_7b5f4ac6fd | 0.65 | no | ok | (kept) | A tropical cyclone will make landfall in Honduras | The forecast stated a tropical cyclone would make landfall in Honduras, but there is no me |
| 27 | pred_1686145f97 | 0.59 | yes | weak | VOID | Oil prices will surge due to a chokepoint disruption | Chokepoint CRITICAL: Bab el-Mandeb — Bab el-Mandeb — 6.2M bpd oil / LIVE SHIPS: 0. |
| 28 | pred_63757acd0e | 0.20 | yes | mismatch | VOID | A Category 2 hurricane will make landfall in the southeastern United States with | The forecast mentioned a Category 2 hurricane making landfall in the southeastern United S |
| 29 | pred_10c9c2e37b | 0.20 | yes | mismatch | VOID | A Category 1 hurricane will make landfall in the southeastern United States with | Green notification for tropical cyclone ELIDA-26 indicates a Category 1 hurricane made lan |
| 30 | pred_9a3cf4b2bb | 0.50 | yes | mismatch | VOID | A magnitude 6.5 earthquake will strike the northern coast of Peru | A Green earthquake (Magnitude 6M, Depth:10km) in Mexico occurred on 17/07/2026 15:20 UTC,  |
| 31 | pred_3e89b3236a | 0.16 | no | ok | (kept) | A tropical cyclone will make landfall in the southeastern United States within t | There is no mention of a tropical cyclone making landfall in the southeastern United State |
| 32 | pred_84aaae219b | 0.30 | no | ok | (kept) | A Category 2 hurricane will make landfall in the southern United States within t | The forecast stated a Category 2 hurricane making landfall within 24 hours, but there is n |
| 33 | pred_22cf4dacf5 | 0.35 | no | ok | (kept) | A 4.5 magnitude earthquake will strike the southern coast of Alaska | No earthquake with a magnitude of 4.5 or higher is reported in the seismic signals, which  |
| 34 | pred_ec3ed681a0 | 0.60 | no | ok | (kept) | The US dollar will strengthen against the euro by 1.5% within the next trading d | The forecast stated a strengthening of the US dollar against the euro by 1.5% within the n |
| 35 | pred_5359698287 | 0.18 | yes | mismatch | VOID | A Category 2 hurricane will make landfall in the southeastern United States with | A Green notification for tropical cyclone ELIDA-26 indicates a population affected by Cate |
| 36 | pred_17128a5648 | 0.45 | no | ok | (kept) | A Category 2 hurricane will make landfall in the southeastern United States | There is no mention of a Category 2 hurricane making landfall in the southeastern United S |
| 37 | pred_a768fd3dc5 | 0.50 | yes | mismatch | no | The US will impose new sanctions on Russia in response to ongoing conflict in Uk | Severe Thunderstorm Warning issued July 16 at 6:38PM MDT until July 17 at 7:45PM MDT by NW |
| 38 | pred_5ceb42d031 | 0.33 | no | ok | (kept) | A major cyberattack will occur on a prominent technology company, causing signif | There is no mention of a major cyberattack on a prominent technology company in the provid |
| 39 | pred_2178baf39a | 0.48 | yes | mismatch | no | A significant escalation of violence will occur between Israeli forces and Pales | The forecast stated a significant escalation of violence between Israeli forces and Palest |
| 40 | pred_8aa67da9ef | 0.45 | no | ok | (kept) | A Category 1 tropical cyclone will make landfall in Honduras. | There is no mention of a Category 1 tropical cyclone making landfall in Honduras, or any s |
| 41 | pred_c4966f4307 | 0.40 | yes | weak | VOID | The South Ukraine NPP will experience a significant drop in power output due to  | The South Ukraine NPP experienced a significant drop in power output due to maintenance is |
| 42 | pred_452b625216 | 0.65 | yes | unverified | VOID | A severe thunderstorm warning will be issued for the Caribou, ME area | A severe thunderstorm warning was issued for the Caribou, ME area on 2026-07-17. |
| 43 | pred_30098d512e | 0.42 | yes | mismatch | VOID | The Strait of Hormuz will experience a brief oil tanker congestion | A green notification for tropical cyclone ELIDA-26 is present, indicating a Category 1 win |
| 44 | pred_5e9dd0dec0 | 0.28 | yes | mismatch | VOID | The Strait of Hormuz will experience a brief but intense naval blockade by Irani | Chokepoint CRITICAL: Strait of Hormuz — 6.2M bpd oil / LIVE SHIPS: 0. Maritime chokepoint  |
| 45 | pred_b8851f2700 | 0.23 | yes | mismatch | VOID | A Category 2 tropical cyclone will make landfall in Honduras within the next 24  | Green notification for tropical cyclone ELIDA-26 indicates a Category 1 tropical cyclone m |
| 46 | pred_293bcbaa67 | 0.18 | yes | mismatch | VOID | A Category 2 tropical cyclone will make landfall in the Philippines | Volcanic eruption is on going for Mayon in Philippines — Volcano Mayon is emitting ash clo |
| 47 | pred_1f5f017622 | 0.50 | yes | mismatch | VOID | A severe thunderstorm will hit the Kansas City area in the next 24 hours. | Severe Thunderstorm Warning issued July 17 at 2:54PM CDT until July 17 at 3:30PM CDT by NW |
| 48 | pred_134db6d9c0 | 0.55 | yes | ok | (kept) | A severe thunderstorm will hit the Mid-Atlantic region in the US | Severe Thunderstorm Warning issued July 17 at 3:53PM EDT until July 17 at 4:30PM EDT by NW |
| 49 | pred_04523c3a13 | 0.38 | yes | mismatch | VOID | A severe thunderstorm warning will be issued for the New York City area. | Severe Thunderstorm Warning issued July 17 at 3:53PM EDT until July 17 at 4:30PM EDT by NW |
| 50 | pred_db578a759e | 0.41 | yes | mismatch | VOID | A severe thunderstorm warning will be issued for the Chicago area in the next 24 | A Severe Thunderstorm Warning was issued for the Chicago area on July 17 at 2:54PM CDT unt |
| 51 | pred_36ede9e99b | 0.20 | yes | weak | VOID | A Category 2 tropical cyclone will make landfall in Mexico within the next 24 ho | A Category 2 tropical cyclone made landfall in Mexico within the next 24 hours, as indicat |
| 52 | pred_2da2b372e2 | 0.55 | yes | mismatch | VOID | A severe thunderstorm warning will be issued for the Denver metropolitan area | Severe Thunderstorm Warning issued July 17 at 3:49PM EDT until July 17 at 4:30PM EDT by NW |
| 53 | pred_26cebc2856 | 0.85 | yes | weak | yes | Severe thunderstorms will hit the Billings area in Montana | Severe Thunderstorm Warning issued July 17 at 2:51PM MDT until July 17 at 3:45PM MDT by NW |
| 54 | pred_023f6629f5 | 0.92 | yes | ok | (kept) | Flash flooding will occur in Tucson, Arizona | A Flash Flood Warning was issued for Tucson, AZ by NWS Tucson AZ on July 17 at 1:51PM MST  |
| 55 | pred_bd885e0643 | 0.80 | yes | mismatch | no | A severe thunderstorm will cause a power outage in the Salt Lake City area | Severe Thunderstorm Warning issued July 17 at 7:55PM EDT until July 17 at 8:45PM EDT by NW |
| 56 | pred_fe6b35a2a5 | 0.78 | yes | ok | (kept) | A severe thunderstorm warning will be issued for the state of New Jersey. | A Severe Thunderstorm Warning was issued for New Jersey on July 17, within the forecasted  |
| 57 | pred_48a99fb9d3 | 0.20 | no | ok | (kept) | A tornado will touch down in a rural area of Texas. | There is no mention of a tornado touching down in Garden City, Texas, USA, in the current  |
| 58 | pred_9dab4d5bfe | 0.82 | yes | ok | (kept) | Flash Flood Warning issued for the area around Tucson AZ | A Flash Flood Warning issued for the area around Tucson AZ, indicating a forecasted flash  |
| 59 | pred_592bb64da8 | 0.48 | yes | ok | VOID | Volcanic eruption will intensify at Mayon volcano in Philippines | A Volcanic eruption is on going for Mayon in Philippines — Volcano Mayon is emitting ash c |
| 60 | pred_be38d15030 | 0.67 | yes | mismatch | VOID | A wildfire will break out in California due to heatwaves | Multiple reports of wildfires in California, including a large one near 'California' which |
| 61 | pred_e259807f45 | 0.35 | no | ok | (kept) | A magnitude 6.0 earthquake will strike near the coast of Japan within the next 2 | There is no record of a magnitude 6.0 earthquake occurring near the coast of Japan within  |
| 62 | pred_a5e6633e55 | 0.52 | no | ok | (kept) | A major cyberattack will occur on critical infrastructure in the United States w | No major cyberattack on critical infrastructure in the United States within the next week  |
| 63 | pred_8524277c16 | 0.44 | yes | mismatch | VOID | A magnitude 6.0 earthquake will strike near the coast of Japan | A Green earthquake (Magnitude 6M, Depth:10km) occurred in Mexico on 7/17/2026 3:20:06 PM,  |
| 64 | pred_40adcc6066 | 0.79 | yes | mismatch | yes | The US will launch additional airstrikes against Iran targets | A magnitude 7.3M earthquake occurred in Mexico on July 17, 2026, potentially affecting 60  |
| 65 | pred_904d408369 | 0.56 | yes | mismatch | yes | Heavy rain will cause flooding in Austria | A Flood Warning issued July 17 at 7:30PM EDT until July 18 at 3:00AM EDT by NWS Wilmington |
| 66 | pred_89eebb2600 | 0.69 | no | ok | (kept) | A major wildfire will break out in California, USA | There is no mention of a major wildfire in California, USA within the forecast window. |
| 67 | pred_862f59c697 | 0.62 | no | ok | (kept) | A major wildfire will break out in Greece | There is no mention of a major wildfire in Greece within the forecast window. |
| 68 | pred_5115bf3d5e | 0.47 | no | ok | (kept) | A 6.0 magnitude earthquake will strike near the South Sandwich Islands within th | There is no mention of a 6.0 magnitude earthquake striking near the South Sandwich Islands |
| 69 | pred_69c4dd5f71 | 0.40 | no | ok | (kept) | A major power outage will occur in the United States | There is no mention of a major power outage in the United States within its window, despit |
| 70 | pred_6b8d059b2d | 0.67 | yes | mismatch | VOID | Volcanic eruption on Mayon in Philippines will intensify | A green earthquake (Magnitude 7.3M, Depth:18.584km) in Mexico was reported on 2026-07-17 a |
| 71 | pred_446bb1b1e9 | 0.39 | yes | mismatch | VOID | Floods will worsen in Austria and Czech Republic | A Flood Warning was issued by NWS Northern Indiana on July 17 at 9:19PM EDT until July 18  |
| 72 | pred_5d0f701030 | 0.57 | yes | mismatch | VOID | The price of oil will rise due to supply chain disruptions | A Green earthquake (Magnitude 7.3M, Depth:18.584km) in Mexico occurred on 2026-07-17, pote |
| 73 | pred_ca20e16ad7 | 0.63 | no | ok | (kept) | A tropical cyclone will make landfall in the Philippines within the next 24 hour | The Flood Warning issued by NWS Northern Indiana, while a significant weather event, is to |
| 74 | pred_06e2ff0115 | 0.40 | yes | mismatch | VOID | A Green earthquake (Magnitude 6.0M) will strike the South Of Fiji Islands within | A Green earthquake (Magnitude 6M) in Mexico on 7/17/2026 at 15:20 UTC, potentially affecti |
| 75 | pred_a9113d039e | 0.46 | yes | mismatch | VOID | A tropical cyclone will make landfall in Mexico within the next 24 hours | A Green earthquake (Magnitude 7.3M, Depth:18.584km) in Mexico occurred within the window,  |
| 76 | pred_f076aac5ef | 0.20 | yes | mismatch | VOID | A tropical cyclone will make landfall in Mexico within the next 24 hours | A Green earthquake (Magnitude 7.3M, Depth:18.584km) in Mexico occurred within the window,  |
| 77 | pred_588b2c1a1c | 0.10 | no | ok | (kept) | A major earthquake will strike the East Coast of the United States within the ne | No major earthquake struck the East Coast of the United States within the next 24 hours, a |
| 78 | pred_dcfba0c070 | 0.36 | yes | mismatch | no | A moderate earthquake will strike the San Francisco Bay Area within the next 24  | A magnitude 6M earthquake occurred in Mexico on 2026-07-17, potentially affecting 1.2 mill |
| 79 | pred_f85083b7bb | 0.53 | yes | mismatch | VOID | A magnitude 6.0 earthquake will occur in the Pacific Ocean, west of New Zealand | A Green earthquake (Magnitude 6M, Depth:10km) in Mexico occurred on 7/17/2026 3:20:06 PM,  |
| 80 | pred_f83b1b0068 | 0.45 | yes | mismatch | no | The flood alert in France will be upgraded to an orange level warning | A magnitude 6M earthquake occurred in Mexico on 7/17/2026, potentially affecting 1.2 milli |
| 81 | pred_8c708df220 | 0.59 | no | ok | (kept) | The US will launch another round of airstrikes against Iran | The forecasted airstrikes against Iran do not appear in any of the archived signals or cur |
| 82 | pred_ecfe2ce709 | 0.20 | no | ok | (kept) | A Category 2 hurricane will make landfall in the southeastern United States with | No Category 2 hurricane made landfall in the southeastern United States within the next 24 |
| 83 | pred_23d5a0ace8 | 0.37 | yes | mismatch | VOID | A magnitude 6.0 earthquake will occur in the South Of Fiji Islands within the ne | A Green earthquake (Magnitude 6M, Depth:10km) in Mexico occurred on 7/17/2026 3:20:06 PM,  |
| 84 | pred_60652451a8 | 0.45 | no | ok | (kept) | A major oil tanker will be seized in the Strait of Hormuz due to Iranian naval a | The forecast stated a 'major oil tanker' would be seized, but there is no mention of such  |
| 85 | pred_d8f5150381 | 0.17 | no | ok | (kept) | A Category 2 hurricane will make landfall in the southeastern United States with | No Category 2 hurricane made landfall in the southeastern United States within the next 24 |
| 86 | pred_55b6d371fe | 0.14 | no | ok | (kept) | A significant earthquake will strike the East Coast of the United States within  | The forecast claimed a significant earthquake on the East Coast of the United States, but  |
| 87 | pred_653ca19af5 | 0.54 | no | ok | (kept) | A magnitude 6.5 earthquake will occur in the South Of Fiji Islands within the ne | The seismic activity during the window was significant, but there is no clear indication o |
| 88 | pred_fe77d727d9 | 0.66 | yes | mismatch | no | The US Federal Reserve will raise interest rates by 50 basis points | A magnitude 7.3M earthquake occurred in Mexico on 2026-07-17, which is within the window. |
| 89 | pred_0b324eda51 | 0.39 | no | ok | (kept) | A category 3 hurricane will make landfall in the southeastern United States with | There is no mention of a category 3 hurricane making landfall in the southeastern United S |
| 90 | pred_5889696241 | 0.72 | no | ok | (kept) | A tropical cyclone will make landfall in the Philippines within the next 24 hour | There is no mention of a tropical cyclone making landfall in the Philippines within the ne |
| 91 | pred_1d610739a8 | 0.40 | no | ok | (kept) | A major cyberattack will target the Microsoft SharePoint platform | There are no reports of a major cyberattack targeting the Microsoft SharePoint platform wi |
| 92 | pred_72b3a467aa | 0.45 | yes | mismatch | VOID | A significant increase in oil prices due to a Turkish Straits blockade | A Green earthquake of Magnitude 6M occurred in Mexico on 7/17/2026, potentially affecting  |
| 93 | pred_56be740426 | 0.51 | no | ok | (kept) | A Category 2 hurricane will make landfall in the southern United States within t | There is no mention of a Category 2 hurricane making landfall in the Gulf Coast, USA withi |
| 94 | pred_80135ef333 | 0.42 | yes | mismatch | VOID | A tropical cyclone will make landfall in Mexico within the next 24 hours | A Green earthquake (Magnitude 7.3M, Depth:18.584km) in Mexico occurred on 7/17/2026 2:48:3 |

## Outcome

- 95 audited: 43 ok, 9 weak, 41 mismatch, 2 unverified.
- 53 superseding records appended (3 yes, 7 no, 43 void) — includes entry 59, voided for consistency.
- Scorecard: 95 resolved / 52% hit / Brier 0.268 → **52 resolved / 67% hit / Brier 0.205 / 43 voided**.
- All 25 receipt sets quarantined to `receipts/quarantine/`; 7 audit-clean receipts regenerated.
- Full machine-readable table: this file's source data lives in the session scratchpad; the
  superseding ledger records (`revised: true`) are the durable audit trail.
