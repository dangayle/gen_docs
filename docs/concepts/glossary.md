# Glossary

## A

**Audio Rate**
Processing that occurs once per sample during audio playback. In Max at 48kHz, this happens 48,000 times per second. See [Execution Model](./execution-model.md).

**Accumulator**
A variable that accumulates a value over time, typically through repeated addition. Common pattern in Gen for summing inputs or tracking running totals.

## B

**Block Size**
The number of samples processed at once. Typical values are 64 or 128 samples. Affects latency: `latency = block_size / sample_rate`.

**Bounded Loop**
A loop with a known maximum iteration count, safe at audio rate. Example: `for (i = 0; i < 10; i += 1)`. See [Performance](./performance.md).

**Buffer**
A region of memory storing multiple values, typically audio samples or parameter history. Gen doesn't support dynamic buffers; use `history()` instead.

## C

**Codebox**
Imperative, C-like syntax for writing Gen code. Procedural style with explicit control flow (if/else, for, while). See [Codebox Overview](../codebox/overview.md).

**Compilation**
The process of converting Gen code to optimized C code that runs at audio rate. Happens automatically when a Gen patcher loads.

**Control Rate**
Processing at a slower rate than audio rate, typically 1-4ms blocks. Used for parameter updates and less time-critical computations.

**Constant Folding**
Compiler optimization that pre-calculates constant expressions at compile time rather than at runtime. Example: `2 + 3` becomes `5`.

## D

**DSL (Domain-Specific Language)**
A language designed for a specific domain rather than general-purpose programming. Gen is a DSL for real-time audio signal processing.

**Delay (Sample Delay)**
A unit of memory storing one sample of audio. A "1-sample delay" stores the previous sample's value. Access with `history()`.

**Deterministic**
Execution that always produces the same result given the same input. Gen is deterministic; no garbage collection or unpredictable delays.

## E

**Execution Model**
How Gen code runs in phases: initialization once, then repeatedly at audio rate for each sample. See [Execution Model](./execution-model.md).

**Expression**
A computation that produces a value. GenExpr is expression-oriented: `(+ (* 2 x) 3)`. Codebox is statement-oriented: `y = 2 * x + 3;`.

## F

**Feedback**
A value computed at sample N that affects the computation at sample N+1. Creates state and memory. Example: `filter = filter * 0.9 + input * 0.1;`.

**Filter**
A signal processing operation that modifies frequencies or amplitudes. Examples: lowpass, highpass, bandpass filters using feedback patterns.

**Float**
32-bit floating-point number. Range approximately -3.4×10³⁸ to 3.4×10³⁸ with 7-8 significant digits. Default numeric type in Gen.

**Function**
A reusable block of code that takes inputs and returns an output. In GenExpr: `(defun name (args) body)`. In Codebox: `void name(args) { }`.

## G

**GenExpr**
Functional, expression-oriented syntax for writing Gen code. Scheme-like syntax with prefix notation. See [GenExpr Overview](../genexpr/overview.md).

**Gen**
Abbreviated name for the Gen object in Max/MSP. Also refers to the domain-specific language family (GenExpr and Codebox). Generates optimized C code from high-level descriptions.

**Glossary**
(This document) A list of terms and their definitions.

## H

**History**
A function accessing previous sample values: `history(signal, N)` returns the sample N steps back. Example: `history(input, 1)` is the previous sample.

## I

**Initialization**
The phase when Gen code runs once to set up state before audio processing begins. Variables set during initialization are often constants used throughout audio rate.

**Inlet**
An input to a Gen object. In GenExpr: `in`, `in1`, `in2`, etc. In Codebox: `x`, `x1`, `x2`, etc. Automatically created based on references in code.

**Integer (int)**
32-bit signed integer. Range: -2,147,483,648 to 2,147,483,647. Used for counters, indices, and discrete values. Less common than float for audio.

## J

**Jitter**
A Max object family for graphics and video. Gen has operators for Jitter integration (`jit_gl_*`, etc.). Outside typical audio Gen usage.

## L

**Latency**
Processing delay from input to output. Example: 128-sample block at 48kHz = 2.67ms latency. Using `history()` doesn't add latency.

**Lookup**
Accessing a value from history or a computed index. All O(1) operations in Gen—no expensive searches.

## M

**Memory**
Storage for variables and state. Gen allocates all memory at compile time; no dynamic allocation supported. Typical Gen objects use < 1KB of memory.

**Module**
Not commonly used in Gen. Gen code is single-file; Max patches combine multiple Gen objects as modules.

## N

**Nested**
One structure inside another. Example: nested loops or nested if statements. Gen supports arbitrary nesting depth.

## O

**Operator**
A built-in function like `+`, `-`, `*`, `/`, `sin`, `cos`, `max`, `min`, etc. See [Operators Overview](../operators/index.md).

**Outlet**
An output from a Gen object. In GenExpr: `out`, `out1`, `out2`, etc. In Codebox: `y`, `y1`, `y2`, etc. Automatically created based on assignments in code.

## P

**Parameter**
A value that can be changed from outside the Gen object without recompilation. Declared with `param name(default_value)`. Updates at control rate.

**Phase**
In signal processing, the time offset of a periodic signal. Also: execution phase (initialization, audio rate).

**Pragma**
A compiler directive giving hints or instructions. Gen uses pragmas like `#pragma` for special handling (rarely needed for basic Gen code).

## Q

**Quantization**
Reducing precision or rounding values, typically to integers. Example: `floor()`, `round()` functions.

## R

**Real-Time Safe**
Code that executes with bounded, predictable time and memory usage. No garbage collection, no blocking I/O, no unbounded loops. Gen is real-time safe by design.

**Recursion**
A function calling itself. Gen allows recursion but requires explicit depth control to avoid stack overflow. Uncommon in Gen code.

**Resonance**
A peak in frequency response, often from feedback in a filter. Higher resonance = more pronounced peak.

## S

**Sample**
A single value in an audio stream. At 48kHz, there are 48,000 samples per second. One sample = ~20.8 microseconds.

**Sample Rate**
Number of audio samples per second. Standard: 44.1kHz or 48kHz. Gen inherits Max's sample rate setting.

**Scope**
The region of code where a variable is accessible. Gen has limited scope; variables are essentially global to the Gen object.

**Signal**
A stream of audio samples. Represented as a continuous stream of values processed one sample at a time at audio rate.

**State**
Data that persists across multiple samples or processing cycles. Examples: filter state, accumulator value, phase. See [Memory and State](./memory-state.md).

**Statement**
An instruction that performs an action. In Codebox: `x = 5;` is a statement. GenExpr uses expressions instead.

## T

**Type**
The data type of a value: int, float, or signal. Gen performs type inference; explicit type declarations often unnecessary.

**Type Inference**
The compiler determining the type of a value based on context. Example: `5` inferred as int, `5.0` as float.

**Type Coercion**
Automatic conversion from one type to another. Example: mixing int and float promotes result to float.

## U

**Unroll**
Compiler optimization expanding loops into sequential code. Example: a loop of 3 iterations becomes 3 copies of the loop body.

**Unbounded Loop**
A loop with unpredictable iteration count. Example: `while (condition)`. Not allowed at audio rate; causes audio glitches or crashes.

## V

**Variable**
A named storage location holding a value. In Gen, variables persist for the object's lifetime. Example: `state = 0; state += 1;`.

**Vector**
A sequence of values, often used for multi-channel audio. Gen processes vectors at audio rate (one sample per channel per execution).

## W

**Waveform**
A repeating pattern of audio samples. Example: sine wave, square wave, sawtooth. Often generated or processed in Gen code.

**While**
A loop structure repeating while a condition is true. In Codebox: `while (condition) { }`. Must be bounded at audio rate.

## X

(No common Gen terms starting with X)

## Y

(No common Gen terms starting with Y)

## Z

**Zero-Crossing**
A point where a signal crosses zero amplitude. Used in some audio analysis algorithms to detect pitch or transients.

## Related Topics

- [Type System](./types.md) - Type definitions and coercion
- [Execution Model](./execution-model.md) - How code executes
- [Memory and State](./memory-state.md) - Variable storage and lifetime
- [Performance](./performance.md) - Performance characteristics and constraints
- [Operators](../operators/index.md) - Complete operator reference
