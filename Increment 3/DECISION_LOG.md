# I3 Decision Log — decisions + justifications + evidence

> Rule: no decision without all columns. Status: proposed / accepted /
> provisional (with owner + revisit date). Trace: P1 clause, P2 slide,
> P3 measurement, P1-star report section.

## Carried-in (do not re-litigate without new data)

| ID | Decision | Alternatives | Justification | Evidence | Impact on main goal | Status |
|----|----------|--------------|---------------|----------|---------------------|--------|
| D-00a | RF_CH 77 (2477 MHz), 2 Mbps, PA_MAX, ARC 5, ARD 500 us, 5 B addr, CRC-16 | 250 kbps/1 Mbps; ch 4/50/73/81; ARC 15 | P1-star Table 2 + Sec 3-5: 0.00% raw PER at 5-10 m at 2 Mbps; ch 77 quiet gap above WiFi 2/6/11; 4.1 ms worst-case << 50 ms | REII327...40496325.docx Sec 3-5; results/summary.md rows 5/10 m; docs/05 | Minimal airtime, meets fault-display budget | accepted |
| D-00b | No app-layer ARQ; HW Auto-ACK + ACK-payload uplink telemetry | App ARQ/heartbeat; separate NOACK uplink | P1-star Sec 6: ACK 0.00% delivery masks raw drops at ops range; avoids over-engineering | summary.md ACK vs NOACK gap (e.g. 5 m 250k 5.6% raw -> 0.00% ACK) | Keeps latency + firmware simple | accepted |
| D-00c | Control link ch 80 / 250 kbps / PA_MAX / ARC 15 / ARD 1500 us reserved | Same-as-test channel; fewer retries | docs/03 + 06: most robust commanding when test config fails; ch 80 above WiFi 1/6/11/13 | 06_progress_summary.md Sec 3.2 PONG,1,3 | Test commanding survives range edge | accepted |
| D-00d | Wiring generated from pin tables; SPI <= 4 MHz Dupont; 10uF+100nF; antenna-before-power; PA_LOW < 2 m | Hand-drawn; 10 MHz SPI; no caps | docs/01 + 02 + notes to future me.txt (LNA saturation) | wiring/*.drawio+png; board_pins.h | Prevents #1 failure modes | accepted |

## New I3 decisions

| ID | Decision | Alternatives | Justification | Evidence | Impact | Status |
|----|----------|--------------|---------------|----------|--------|--------|
| D-01 | Freeze 32-B downlink/uplink byte maps (structs + version byte) | (a) fixed structs [REC]; (b) TLV/dynamic; (c) nanopb | ICD Sec 4 TBD must close for firmware; fixed = simplest, matches static 32-B ShockBurst, shared STM32/ESP32 header; SoW item 2 open design space | Increment 3/ICD_REII_v1.0_signed.pdf; loopback pack/unpack log | Unblocks O2/O3/O4 | proposed -> accepted Week 7 |
| D-02 | Telemetry via ACK-payload only | (a) ACK-payload [REC]; (b) separate NOACK; (c) hybrid heartbeat fallback | ICD Sec 3; zero extra airtime; proven by retry stats; keeps 4.1 ms budget | rail ACK vs NOACK CSV comparison | Dashboard live telemetry free | proposed |
| D-03 | Rate locked 2 Mbps at ops envelope; adaptive fallback only if rail NLOS fails | (a) locked 2 M [REC]; (b) adaptive 2M->1M->250k | P1-star Sec 3 conclusion for 5-10 m; adaptivity = complexity + risk | rail NLOS rows in TEST_LOG.md | Deterministic latency | proposed |
| D-04 | Sway shaping: S-curve baseline, ZV shaper if freq identified | (a) S-curve [REC start]; (b) ZV shaper; (c) raw trapezoidal baseline | Kickoff Step 5 + SoW item 4; open-loop only per slide 7; MCTR-I drive compatible | sway_model.py + rail sway plots vs 5 deg | Sway <= 5 deg req | proposed |
| D-05 | Dongle bring-up order: CDC-echo first, then SPI, then RF | (a) CDC first [REC]; (b) SPI first | Kickoff Step 2; isolates USB vs RF faults; matches STM32CubeIDE flow | CDC echo capture; printPrettyDetails | Faster fault isolation | proposed |
| D-06 | IO-board owner + ESP IO-header SPI pins + current-sense owner | EE2 vs MC2 ownership | Slide 7 OPEN must resolve before ICD drafted; bench-verify isolation before live gate drivers | signed ICD Sec 1; isolation test log | Safety + schedule | proposed Week 7 |
| D-07 | Rail RF re-survey at dongle position; guard >= 4 | Reuse bench survey | docs/04: rail multipath/WiFi != bench; coexistence with 4 other groups | channel_survey_rail PNG + group plan | Avoids rail-day interference | proposed Week 9 |

## Template for new rows

ID | Date | Decision | Alternatives | Justification (P1/P2/P3/P1-star) | Evidence file | Impact on main goal | Status
