# I3 Week Plan — Initial Integration (Weeks 7–10)

> Scope: first physical integration on rail; interface verification.
> Entry: I2 Bench Proof (subsystem bench + ICD). Exit: rail assemblies
> talking through ICD interfaces with evidence, ready for I4.
> Main goal anchor: STM32F411 dongle + ESP32 stub + 32-B protocol +
> dashboard + open-loop anti-sway/path planning, validated on crane.

## Key dates (from slides/7.1)

- 11 Sep: Individual checkpoint due (all packages).
- 14 Sep: I3 begins — group ICD drafted and signed off in week 1.
- 27 Oct: Participation marks captured.
- 13 Nov: Final Demonstration. 16 Nov: Group Presentation + Portfolio.

## Week 7 — Freeze interfaces (ICD sign-off)

Objective: ICD_REII v1.0 signed; Sec 4 payload bytes frozen or marked
PROVISIONAL with owner + date.

Tasks:
1. D-01 payload workshop with MCTR/EEII (see DECISION_LOG.md D-01):
   downlink (state cmds, motion-profile coeffs, magnet, E-stop/mode)
   and uplink (X/Y/height/velocity, load, VFD current, errors).
   Time-box to Friday; fallback = REII-proposed map PROVISIONAL.
2. D-02 IO-board owner (EE2 vs MC2), ESP IO-header SPI pins, magnet
   current-sense conditioning owner. No rail power wiring until pinned.
3. Update System_FFBD.drawio + Sys_Architecture.drawio to ICD v1.0
   (P2 slide style, .drawio + PNG paired).
4. Branch: feature/i3-icd-freeze. Evidence: signed ICD PDF + notes.

Exit: ICD signed PDF committed in Increment 3/.

## Week 8 — Bench integration (off-rail)

Objective: both ends talk bench-to-bench through Table 2 config.

Tasks:
1. STM32CubeIDE: CDC echo loop first (isolates USB), then SPI1 ->
   radio.begin() OK + isPVariant + Table 2 init-table read-back
   (RF_CH 77, 2 Mbps, PA_MAX, ARC 5, ARD 500 us, 5 B, CRC-16).
   SPI <= 4 MHz on Dupont, 10uF+100nF, antenna-before-power,
   PA_LOW under 2 m (LNA saturation per notes to future me.txt).
2. ESP32: complete RX stub -> RX mode -> ACK-payload telemetry.
   Bench PING->PONG + run_test.py --n 200 smoke at ch 77.
3. Dashboard: 32-B pack/unpack + fault-display timer harness
   (inject fault packet, measure <= 50 ms).
4. Evidence: serial captures, smoke CSV in results/i3_bench/.

Exit: bench PONG + 0.00% ACK smoke + fault timer screenshot.

## Week 9 — Rail integration (first on-rail)

Objective: dongle<->PC<->gantry on rail at 5–10 m ops envelope.

Tasks:
1. Mount controller + nRF pair on rail; dongle USB-tethered at
   operator station. Antennas vertical, >= 0.5 m separation.
2. Run INTERFACE_MATRIX.md rows: USB power, SPI read-back, ch77/2M
   link at 5/10 m rail positions, ACK-payload continuity, E-stop
   halt <= 1.5 s magnet-held, fault display <= 50 ms.
3. Re-survey rail RF position (channel_analysis.py) — rail RF != bench.
4. First sway run: flat vs shaped (S-curve) profile from dashboard;
   log position vs time; quantify vs 5-degree bound.
5. Evidence: rail CSVs, packet_loss_vs_distance_rail.png, sway plots,
   E-stop timing log + video.

Exit: matrix all-pass or NCRs raised with owners.

## Week 10 — Harden + document for I4

Objective: I4 handoff pack complete.

Tasks:
1. Close NCRs; confirm ARC 5 still caps worst-case < 50 ms minus
   USB/dashboard latency (4.1 ms radio + measured USB).
2. Fill docs/05_recommended_config.md measured columns from rail.
   ICD v1.1 only if pins/timings changed (never silent).
3. Write I3_HANDOFF.md: init tables, frozen headers, dashboard
   build, sway model + generator, decision/test log pointers.
4. Evidence: handoff pack + PR to main with P1/P2 trace.

## Branch/commit discipline

feature/i3-planning (this pack) -> feature/i3-icd-freeze ->
feature/i3-bench -> feature/i3-rail-integration. Atomic REII:
commits, verify (pio run, dry-run, draw.io reopens), push + PR.
