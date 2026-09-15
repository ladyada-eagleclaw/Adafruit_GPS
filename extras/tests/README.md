# Regression tests

Run every test on Linux with Python 3 and g++:

```sh
python3 extras/tests/run_tests.py
```

The runner discovers every `.cpp` and `.ino` in the test subdirectories.
Each C++ test supplies `main()`. Each Arduino sketch supplies `setup()`,
`loop()`, and a `testsPassed` flag that remains false until all checks pass.
The runner calls `setup()` and `loop()` once and returns failure when that
flag is false. New tests are picked up automatically without editing CI.

All tests use AddressSanitizer and UndefinedBehaviorSanitizer. Compiler
warnings, failed checks, sanitizer findings, and a 30-second execution timeout
fail the job. Other tests still run after an individual failure.

The Arduino sketches use the real library sources twice: once with
`NMEA_EXTRAS=0` for the basic GPS API and once with `NMEA_EXTRAS=1` for the marine
extensions. The RMB test checks that basic builds reject that unsupported
sentence and that extended builds decode it without partial updates on error.
Standalone C++ core tests run once. `extras/test_support` provides serial
output and a real monotonic clock; the wakeup timeout test takes ten seconds.
I2C, SPI, GPIO, and hardware serial input abort if used. This runs the parser
and mock-stream regressions, not physical GPS/SD hardware or MCU emulation.
The sketches remain usable on Arduino boards with the normal Arduino core.
