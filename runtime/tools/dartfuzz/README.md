DartFuzz
========

DartFuzz is a tool for generating random programs with the objective
of fuzz testing the Dart project. Each randomly generated program
can be run under various modes of execution, such as using JIT,
using AOT, using JavaScript after dart2js, and using various target
architectures (x86, arm, etc.). Any difference between the outputs
(**divergence**) may indicate a bug in one of the execution modes.

How to run DartFuzz
===================
To generate a single random Dart program, run

    dart dartfuzz.dart [--help] [--seed SEED] FILENAME

where

    --help : prints help and exits
    --seed : defines random seed (system-set by default)

The tool provides a runnable main isolate. A typical single
test run looks as:

    dart dartfuzz.dart fuzz.dart
    dart fuzz.dart

How to start DartFuzz testing
=============================
To start a fuzz testing session, run

    dart dartfuzz_test.dart

    run_dartfuzz_test.py  [--help]
                          [--isolates ISOLATES ]
                          [--repeat REPEAT]
                          [--true_divergence]
                          [--mode1 MODE]
                          [--mode2 MODE]

where

    --help            : prints help and exits
    --isolates        : number of isolates in the session (1 by default)
    --repeat          : number of tests to run (1000 by default)
    --show-stats      : show statistics during session (true by default)
    --true-divergence : only report true divergences (true by default)
    --dart-top        : sets DART_TOP explicitly through command line
    --mode1           : m1
    --mode2           : m2, and values one of
        jit-[debug-]ia32    = Dart JIT (ia32)
        jit-[debug-]x64     = Dart JIT (x64)
        jit-[debug-]arm32   = Dart JIT (simarm)
        jit-[debug-]arm64   = Dart JIT (simarm64)
        aot-[debug-]x64     = Dart AOT (x64)
        aot-[debug-]arm64   = Dart AOT (simarm64)
        kbc-int-[debug-]x64 = Dart KBC (interpreted bytecode)
        kbc-mix-[debug-]x64 = Dart KBC (mixed-mode bytecode)
        kbc-cmp-[debug-]x64 = Dart KBC (compiled bytecode)
        js                  = dart2js + JS

If no modes are given, a random JIT and/or AOT combination is used.

This fuzz testing tool must have access to the top of a Dart SDK
development tree (DART_TOP) in which all proper binaries have been
built already (for example, testing jit-ia32 will invoke the binary
${DART_TOP}/out/ReleaseIA32/dart to start the Dart VM). The DART_TOP
can be provided through the --dart-top option, as an environment
variable, or, by default, as the current directory by invoking the
fuzz testing tool from the Dart SDK top.

Background
==========

Although test suites are extremely useful to validate the correctness of a
system and to ensure that no regressions occur, any test suite is necessarily
finite in size and scope. Tests typically focus on validating particular
features by means of code sequences most programmers would expect. Regression
tests often use slightly less idiomatic code sequences, since they reflect
problems that were not anticipated originally, but occurred “in the field”.
Still, any test suite leaves the developer wondering whether undetected bugs
and flaws still linger in the system.

Over the years, fuzz testing has gained popularity as a testing technique for
discovering such lingering bugs, including bugs that can bring down a system
in an unexpected way. Fuzzing refers to feeding a large amount of random data
as input to a system in an attempt to find bugs or make it crash. Generation-
based fuzz testing constructs random, but properly formatted input data.
Mutation-based fuzz testing applies small random changes to existing inputs
in order to detect shortcomings in a system. Profile-guided or coverage-guided
fuzz testing adds a direction to the way these random changes are applied.
Multi-layered approaches generate random inputs that are subsequently mutated
at various stages of execution.

The randomness of fuzz testing implies that the size and scope of testing is
no longer bounded. Every new run can potentially discover bugs and crashes
that were hereto undetected.
