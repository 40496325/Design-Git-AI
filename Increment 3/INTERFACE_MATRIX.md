# I3 Interface Matrix — ICD clause -> test -> pass/fail

> Each row must end with a result file or NCR ID. Blank = not run.
> Config baseline: Table 2 (ch 77 / 2 Mbps / PA_MAX / ARC 5 /
> ARD 500 us / 5 B / CRC-16) unless row states otherwise.

| ICD ref | Check | Method (cmd/script) | Pass band | Result | Evidence file |
|---------|-------|---------------------|-----------|--------|---------------|
| Sec 1 power | Dongle USB-bus-powered only; 3V3 +-0.1 V at module; 10uF+100nF fitted both ends | DMM + photo; PA_MAX burst | No crane power draw; no brownout/reset | | |
| Sec 1 SPI (dongle) | STM32 SPI init = Table 2; regs read back exact | printPrettyDetails | begin OK; RF_CH/RF_SETUP/SETUP_RETR match | | serial capture |
| Sec 1 SPI (crane) | ESP IO-header SPI init = Table 2; RX mode | printPrettyDetails | Same as above | | serial capture |
| Sec 2 RF bench | ch77/2M bench smoke N=200 | run_test.py --n 200 | ACK delivery 100%; PING->PONG | | results/i3_bench/*.csv |
| Sec 2 RF rail 5 m | Rail position 5 m LOS N=1000 | run_test.py --distance 5 --n 1000 | ACK >= 99.9%; worst-case <= 50 ms - USB | | rail CSV + plot |
| Sec 2 RF rail 10 m | Rail position 10 m LOS N=1000 | run_test.py --distance 10 --n 1000 | Same | | rail CSV + plot |
| Sec 2 RF NLOS | >= 1 rail NLOS point | run_test.py --los 0 --n 1000 | Logged; fallback decision D-03 if fail | | rail CSV |
| Sec 2 coexistence | Rail-position survey | channel_analysis.py --out results/i3_rail | ch 77 quiet gap; group plan >= 4 guard | | survey PNG |
| Sec 3 downlink | Idle/Move/E-stop/Magnet cmds execute on rail | Dashboard -> rail | E-stop halt <= 1.5 s; magnet held | | timing log + video |
| Sec 3 uplink | ACK-payload telemetry continuous | REPORT + dashboard log | No seq gaps; retries <= ARC 5 | | telemetry CSV |
| Sec 4 structs | 32-B pack/unpack byte-exact both ends | Loopback + rail | Version byte checked; scaling exact | | unit log |
| SysRS fault | Fault packet -> red display | Inject fault packet | <= 50 ms display | | timestamped capture |
| Sway | Shaped vs baseline profile on MCTR-I drive | sway profile -> rail run | Sway <= 5 deg | | sway plot |
| Safety | Isolation bench-verify before live EE1 drivers | Per slide 7 | Signed off | | isolation log |

NCR template: NCR-ID | ICD ref | observed | expected | owner | due | retest file.
