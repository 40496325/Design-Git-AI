# I3 Test Log — measurements only (append-only via scripts)

> Schema matches docs/03_test_procedure.md CSV. One row per burst.
> Never hand-edit CSVs; log notes here with CSV path.

| date | position | LOS | channel | rate | PA | mode | N | PER % | acked/failed | retries max | status | csv path | note |
|------|----------|-----|---------|------|----|------|---|-------|--------------|-------------|--------|----------|------|
| | bench 1 m | 1 | 77 | 2000 | LOW | ACK | 200 | | / | | | | smoke |
| | bench 1 m | 1 | 77 | 2000 | LOW | NOACK | 200 | | - | - | | | smoke |
| | rail 5 m | 1 | 77 | 2000 | MAX | ACK | 1000 | | / | | | | I3 rail |
| | rail 5 m | 1 | 77 | 2000 | MAX | NOACK | 1000 | | - | - | | | I3 rail |
| | rail 10 m | 1 | 77 | 2000 | MAX | ACK | 1000 | | / | | | | I3 rail |
| | rail 10 m | 1 | 77 | 2000 | MAX | NOACK | 1000 | | - | - | | | I3 rail |
| | rail NLOS | 0 | 77 | 2000 | MAX | ACK | 1000 | | / | | | | D-03 input |
| | rail survey | - | sweep | - | - | - | - | - | - | - | | | D-07 input |

Fault/E-stop/sway logs (timestamped):

| date | test | input | measured | requirement | pass | file |
|------|------|-------|----------|-------------|------|------|
| | fault inject -> red display | fault packet | ms | <= 50 ms | | capture |
| | E-stop halt magnet-held | E-stop cmd | s | <= 1.5 s | | timing log |
| | sway flat vs shaped | profile id | deg | <= 5 deg | | sway plot |
