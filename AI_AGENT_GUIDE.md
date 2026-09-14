# AI Agent Guide - Design-Git-AI

> Read this first, every task, every agent. This is the interaction contract.
> If a generic best-practice conflicts with this file, this file wins.
> Root: d:\DESIGN GIT\Design-Git-AI | Branch: main

## 1. What this repo is

EngDes26 REII: PC Dashboard + STM32F411 dongle + ESP32 gantry
controller + nRF24L01+ link. Steel-plate hoist: 2000 kg, 5 m
clearance, IP54, fail-safe brake, 50 ms fault display.

Layout:

- AI_AGENT_GUIDE.md = THIS FILE (P0 authority).
- System_FFBD.drawio = live system FFBD (editable).
- Sys_Architecture.drawio = live system architecture (editable).
- What Exactly You Need to Start Doin.txt = 5-step kickoff.
- Requirements/ = NORMATIVE SysRS, SoWs, board docs, general_requirements.txt.
- slides/ = NORMATIVE format + process (lectures + 7.1 Checkpoint Breakdown).
- Increment 2/ICD_REII.docx = NORMATIVE interfaces.
- last year examples/ = REFERENCE ONLY. Never copy, never cite.
- .vscode/extensions.json = recommended extensions.
- REII_nRF24_Link_Characterisation/ = main sub-project:
  - REII327 nRF24L01 Link Bring-Up and Characterisation 40496325.docx = IMPORTANT final report (source).
  - REII327 nRF24L01 Link Bring-Up and Characterisation 40496325.pdf = same report (export, keep synced).
  - Sub.docx = earlier draft. Do not confuse with final.
  - README.md = plan + build and flash quick ref.
  - notes to future me.txt = lab note (LNA saturation at PA HIGH, short range).
  - docs/01-06 .md + wiring/ = decisions, procedures, diagrams.
  - firmware/ (PlatformIO) = platformio.ini, src/main.cpp, lib/linktest/.
  - tools/*.py + requirements.txt = run_test, plots, channel analysis, gen wiring.
  - results/*.csv, png, raw, md = measured data + summary.md.
  - datasheets/*.pdf = Nano, ESP32-C3, ESP32-WROOM-32, NRF24L01.
  - .gitignore = excludes results/synthetic/, tools/__pycache__/.

## 2. Authority hierarchy

- P0: This guide. On ambiguity ask one question with 2-5 options. Never freelance.
- P1: Requirements SysRS v1.0, SoW PDFs, Increment 2 ICD_REII.docx. Normative. Never contradict. Trace output to clause.
- P1-star: REII327 ... 40496325 docx/pdf. Proven sub-project deliverable. Quote its Section 6 decisions. See Section 7 for edit rules.
- P2: slides/*.pptx. Approved FORMAT + PROCESS. Every diagram, FFBD, architecture, schematic, presentation MUST match slide style and structure. See 4A.
- P3: datasheets, docs/*.md, README, results/. Engineering ground truth. Never invent pins or values.
- P4: System_FFBD.drawio, Sys_Architecture.drawio, kickoff txt. Live model. Edit in place, keep consistent.
- P5: last year examples/. Inspiration only. NEVER copy text or figures verbatim, never cite as justification, never override P1-P4.

User example generalised: draw.io schematics -> slides show the approved format.
Workflow: open relevant slides first, match symbols, title blocks, colours,
hierarchy, decomposition, then draw. Same source-first pattern for every
request type in Section 4.

## 3. Startup ritual (before any edit)

1. Read this guide + task-relevant P1/P1-star/P2/P3 files. Never assume.
2. Run git status --short --branch and git log --oneline -10. Protect user data.
3. Confirm toolchain: PlatformIO envs (rx_nodemcu32s, scanner_nodemcu32s,
   tx_esp32c3, tx_esp32c3_uart, tx_nano), Python pyserial/numpy/matplotlib,
   draw.io + PNG export, python-docx for .docx.
4. .docx via python-docx (styles, tables, inline_shapes). Never binary-edit.
   .pptx and large PDFs: report what cannot be diffed; edit only on instruction.


## 4. Playbook - every possibility

### 4A. Draw.io (schematics, wiring, FFBD, architecture, SCD)
P2 governs. Open slides 3.1 FBA, 3.2 Synthesis, 0 Intro, 7.1
Checkpoint plus existing docs/wiring/*.drawio and root FFBD and
Architecture for house style FIRST. Output: valid editable .drawio
XML (diagrams.net) + PNG export side-by-side. GPIO numbers as
labels. Include decoupling + antenna warnings where relevant.
Wiring is GENERATED: docs/02_wiring.md + lib/linktest/board_pins.h
-> tools/gen_wiring_drawio.py -> .drawio -> PNG. Never hand-diverge
generated files; change pin table + regenerate. Verify: reopen
.drawio, PNG matches, consistent with 02_wiring.md.

### 4B. Requirements
Templates: general_requirements.txt (9-part functional, 3-part
non-functional) + kickoff strict parse Actor/Condition/Shall/
Action/Object/Constraint. Numbered, testable, traced to SysRS/SoW
clause. Unacceptable: vague words (fast, robust) or untraced text.

### 4C. Firmware (PlatformIO, Arduino + RF24 1.4.9)
One codebase, -DROLE_RX/_TX/_SCANNER. SPI max 4 MHz on Dupont.
WiFi and BT OFF in tx/rx roles. 3V3 ONLY to nRF24 VDD. 10uF + 100nF
at module. Antennas on before power. PA_LOW or MIN under 2 m
(PA_MAX saturates LNA per notes to future me.txt). Control link:
ch 80, 250 kbps, PA_MAX, ARD 1500 us, ARC 15. Verify: pio run,
radio.begin() OK, isPVariant, printPrettyDetails read-back,
PING -> PONG, run_test.py smoke with --n 200.

### 4D. Python tools
Only pyserial, numpy, matplotlib. Keep --help working. Never change
results CSV schema (docs/03_test_procedure.md) without updating the
doc. Never commit results/synthetic/ or __pycache__/. Dry-run with
tools/make_synthetic_data.py before hardware runs.

### 4E. RF tests, results, plots
Follow docs/03 + 04. Payload 32 B static, CRC 16-bit, 5-B address.
Bursts N=1000 (200 smoke). Control config fixed. Distances 1-50 m
LOS + 3 NLOS. Committed outputs: packet_loss_DATE.csv,
channel_survey_DATE files, plots, summary.md. Then fill 05 config
measured + decision columns.

### 4F. Reports, docs, presentations
Structure per slides 7.1 Checkpoint + lecture slides. .docx is
source, .pdf is export (keep synced). No Office lockfiles
(~$*.docx, ~WRL*.tmp) in commits. Sub.docx is an old draft; the
REII327 ... 40496325 pair is normative (P1-star).

### 4G. Sway and path-planning models
Per kickoff Step 5: Python/MATLAB pendulum model, horizontal accel
-> sway, feedforward shaping (input shaping / S-curve). Commit
script + plot + assumptions + parameter source.

### 4H. New sub-projects and folders
Mirror structure: README.md, docs/, firmware/, tools/, results/,
datasheets/. Add .gitignore for build artefacts (.pio/, .vscode/,
synthetic/, __pycache__/). Never nest a second git repo unasked.
## 5. Acceptable-output checklist

- Correct location + paired artefacts (.drawio + .png, .docx + .pdf,
  .csv + .png). Traced to P1 clause, P2 slide, P3 evidence, or
  P1-star report section. No invented pins/channels/values.
- No P5 copying. No junk: no ~$*, ~WRL*, .pio/, __pycache__/,
  synthetic outputs. Verified: pio compiles, scripts dry-run,
  .drawio reopens, PNG current, .docx reopens.

## 6. Forbidden list

- 5 V to nRF24 VDD. Nano 3V3 pin for PA+LNA (use external 3V3 LDO).
- WiFi/BT on during tx/rx RF tests (scanner role is the exception).
- Product channels outside ISM 2-81 or on control ch 80.
- Hand-editing results CSVs. Committing secrets, .pio/, lockfiles.
- Copying last year examples. Contradicting SysRS/SoW/ICD.
- Direct commits to main for non-trivial work (see Sec 8).


## 7. IMPORTANT report (P1-star) - read + edit rules

File: REII_nRF24_Link_Characterisation/REII327 nRF24L01 Link
Bring-Up and Characterisation 40496325.docx + synced .pdf.
Verified: 107 paragraphs, 2 tables, 6 figures. Author Riekert Nel
40496325, NWU Computer Electronic Engineering.
Map: Title/Subtitle, Abstract (verify link before STM32 dongle),
List of Figures (6) + Tables (2), Sec 1 Problem Statement,
Sec 2 System Setup (2.1 Method run_test/channel_analysis,
2.2 Implementation ESP32-C3 TX + NodeMCU-32S RX + Fig 1 hardware),
Sec 3 Packet-loss vs distance 1-190 m LOS + Fig 2 (2000 kbps for
5-10 m ops), Sec 4 Channel selection (ch 77 at 2477 MHz, guard 4,
group plan 4/50/73/77/81, Table 1 ch 11 vs ch 4 at 5 m) + Fig 3,
Sec 5 Recommended config (Table 2: RF_CH 77, 2 Mbps, PA_MAX,
ARC 5, ARD 500 us, 5 B addr, CRC-16) + latency 4.1 ms vs 50 ms,
Sec 6 Protocol (no app ARQ, HW Auto-ACK + ACK-payload telemetry),
Sec 7 Appendices (Fig 4 retries, Figs 5-6 wiring schematics).
Table 2 + Sec 6 are the start point for STM32F411 init table,
ESP32-S3 stub, and 32-B protocol design.
Read via python-docx (styles, tables, inline_shapes). Edit rule:
.docx is source, re-export .pdf same name, never rename, keep
Sub.docx as history, justify each change by report section + P1/P2/P3.

## 8. Git + version control (mandatory)

- Flow: status/branch -> new branch (devin/ID-slug or feature/slug,
  matches PRs #2-#7) -> atomic conventional commits
  (REII: ..., nRF24 link test: ...) -> verify (pio run, dry-run,
  valid XML, docx reopens) -> push -u origin BRANCH -> PR to main
  with P1/P2 trace + test evidence.
- Never commit direct to main for non-trivial work. Never
  force-push main. Never commit ~$*, ~WRL*.tmp, .pio/,
  __pycache__/, synthetic/.
- Untracked triage (stage deliberately, not careless -A): .vscode/,
  Increment 2/, REII327 docx/pdf pair, Sub.docx, new
  channel_survey_* + packet_loss_2026-09-08.csv, terminal txt notes,
  Requirements/esp32-s3 doc. Large binaries (ICD 3.8 MB, PPTXs)
  change rarely; confirm before re-committing.
- Messages state WHAT + WHY + evidence (measured file, slide, clause).

## 9. Ambiguity rule + file types

Ask one question (2-5 options) when req, format, env, or destructive
action is ambiguous. Else state assumptions inline.
.drawio = editable XML + PNG. .png = export only. .md = docs.
.py = tools, pinned deps, --help intact. .csv/.raw = append-only via
scripts. .docx = python-docx edits then .pdf export. .pdf = export.
.pptx = P2 authority, do not restyle. .txt = short notes.

## 10. Worked example (draw.io schematic)

1. Open slides/3.x + 0 Intro; note symbols, title block, colours.
2. Open docs/wiring/*.drawio + 02_wiring.md + board_pins.h.
3. Pin change: edit 02_wiring.md + board_pins.h, run
   gen_wiring_drawio.py, export PNGs.
4. Verify: .drawio reopens, PNG matches, branch -> commit ->
   push -> PR.

## 11. Agent sign-off

Before first edit each session confirm: guide read, authority files
for this task read (list them), branch created, toolchain confirmed.
