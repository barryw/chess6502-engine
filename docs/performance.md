# Performance Loop

Use the headless ca65 harness for fast, repeatable performance checks:

```sh
make benchmark
make benchmark-json
make size
```

`make benchmark` runs `tests/engine_benchmark.6502` through the latest Sim6502
Docker image and then reruns a temporary copy with cycle assertions forced to
fail, which exposes exact cycle counts without weakening the checked-in gates.

Current standalone benchmark baseline:

| Benchmark | Cycles | Gate |
| --- | ---: | ---: |
| easy mate in one | 1,191,544 | 2,400,000 |
| medium mate in one | 1,191,561 | 2,400,000 |
| hard mate in one | 1,191,561 | 2,400,000 |
| depth-1 hanging queen search | 857,044 | 950,000 |
| hard hanging queen | 512,552 | 700,000 |
| depth-5 middlegame search | 4,079,788 | 5,000,000 |
| hard white promotion | 457,363 | 650,000 |
| hard black promotion | 460,995 | 650,000 |
| hard rook activation | 573,476 | 750,000 |

`make size` reports ld65 segment sizes from `build/engine_harness.dbg`. `FILE`
is the emitted PRG payload; `RUNTIME` includes `BSS` RAM reserved by the linker.
Current standalone ca65 size:

| Segment | Range | Bytes |
| --- | --- | ---: |
| `LOADADDR` | `$0000-$0001` | 2 |
| `CODE` | `$0801-$555f` | 19,807 |
| `BSS` | `$5560-$68e0` | 4,993 |
| PRG payload | | 19,809 |
| runtime footprint | | 24,802 |

The resident engine budget target is 35K runtime footprint. The current
standalone harness leaves 11,038 bytes for additional resident engine logic
before hitting that ceiling.

Treat benchmark changes as suspicious until they have both a cycle explanation
and a strength/correctness test result. The goal is to make every optimization
visible as either fewer cycles, fewer bytes, or a deliberately accepted tradeoff.
