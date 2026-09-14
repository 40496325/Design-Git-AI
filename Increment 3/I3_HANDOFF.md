# I3 Handoff — exit gate for I4 Full Integration

> I3 exit = rail-mounted assemblies talking through ICD interfaces
> with evidence. Everything below must point to a committed file.

## 1. Entry/exit

- Entry (I2 done): bench functionality + ICD draft. Evidence:
  REII327...40496325.docx (P1-star), results/summary.md,
  Increment 2/ICD_REII.docx.
- Exit (I3 done): INTERFACE_MATRIX.md all-pass or NCRs with owners;
  DECISION_LOG.md D-01..D-07 accepted; TEST_LOG.md rail rows filled;
  this handoff signed.

## 2. Handoff artefacts (paths)

- Frozen protocol headers (STM32 + ESP32 shared, version byte):
  path: TBD fill Week 8.
- STM32 dongle init table (= Table 2) + CDC bridge build:
  path: TBD fill Week 8.
- ESP32 RX stub + ACK-payload build: path TBD Week 8.
- Dashboard build with 32-B pack/unpack + fault timer: path TBD.
- Sway model + profile generator + assumptions: path TBD Week 9.
- Rail CSVs + plots + survey PNG: results/i3_rail/ TBD Week 9.
- Signed ICD v1.0 (v1.1 if changed): Increment 3/TBD Week 7/10.

## 3. Trace to main goal (SoW items)

1. Dongle + ESP32 stub -> matrix SPI/RF rows -> files above.
2. Protocol -> D-01/D-02 + Sec 4 loopback log.
3. Dashboard -> fault <= 50 ms capture.
4. Anti-sway -> D-04 + sway plot <= 5 deg.
5. Obstacle-avoidance plan -> profile generator (open-loop only).
6. Validation -> TEST_LOG rail rows + NCR closures.

## 4. Known provisionals into I4

- List here (e.g. load-cell deferred per slide 6; payload map if
  unsigned Week 7; IO-board schematic if open). Each: owner + date.

## 5. Sign-off

REII lead: ______ date: ______. MCTR/EEII counterparts: ______.
