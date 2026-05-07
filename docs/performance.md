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
| easy mate in one | 1,864,029 | 2,400,000 |
| medium mate in one | 1,864,063 | 2,400,000 |
| hard mate in one | 1,864,063 | 2,400,000 |
| depth-1 hanging queen search | 741,701 | 950,000 |
| hard hanging queen | 482,042 | 700,000 |
| hard white promotion | 447,574 | 650,000 |
| hard black promotion | 451,212 | 650,000 |
| hard rook activation | 513,910 | 750,000 |

`make size` reports ld65 segment sizes from `build/engine_harness.dbg`. Current
standalone ca65 size:

| Segment | Range | Bytes |
| --- | --- | ---: |
| `LOADADDR` | `$0000-$0001` | 2 |
| `CODE` | `$0801-$61f6` | 23,030 |
| total PRG payload | | 23,032 |

Treat benchmark changes as suspicious until they have both a cycle explanation
and a strength/correctness test result. The goal is to make every optimization
visible as either fewer cycles, fewer bytes, or a deliberately accepted tradeoff.
