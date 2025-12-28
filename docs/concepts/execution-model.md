# Execution Model

Gen code runs per-sample at audio rate. There is no user-exposed “init” phase separate from the main execution; any constants are evaluated by the compiler, and runtime code executes for each sample.

## Audio-Rate Execution

**Characteristics:**
- Runs for every sample (or block-expanded by the host)
- Must be real-time safe: no dynamic allocation or blocking I/O
- Uses fixed, bounded control flow

**Allowed operations:**
- Read inputs (`in`, `in1`, `in2`, ...)
- Perform computations
- Write outputs (`out`, `out1`, `out2`, ...)
- Update state via `history`/delay operators

**Example:**
```c
filtered = in1 * gain + history(0) * feedback;
out = filtered;
```
## Inlet and Outlet Semantics

### Inlets

Inlets (`in`, `in1`, `in2`, ...) provide the current sample value (read-only) aligned to the DSP vector.

```c
value = in1;       // First inlet
x = in2;           // Second inlet
```

### Outlets

Outlets (`out`, `out1`, `out2`, ...) are written per-sample:

```c
out = computed_value;      // First outlet
out2 = another_value;      // Second outlet
```

### Automatic Inlet/Outlet Creation

Inlets and outlets are created automatically based on what you reference:

```c
out = in1 + in2;   // creates two inlets and one outlet
```

## State and History

### Variables and State

Plain variables do not persist sample-to-sample. Use `history` (or related operators) to carry state:

```c
prev = history(0);
out = in1 - prev;
```

### History: Accessing Previous Values

Gen provides `history()` for accessing previous sample values:

```c
previous = history(in1, 1);   // Get previous sample
out = in1 - previous;         // One-sample differentiator
```

## Timing and Synchronization

### Audio Block Processing

Gen processes audio in blocks (not individual samples):

- Block size depends on Max audio settings
- Typical: 64 or 128 samples per block
- Operations inside loops may process one sample at a time

### Sample Rate

Gen inherits Max's sample rate:
- Typically 44.1 kHz or 48 kHz
- Available as global: sample rate context (if needed)

## Parameter Changes

### Dynamic Parameters

Parameters (declared with `param` keyword) can change:

```c
param frequency(440.0);
param resonance(1.0);

out = sin(in * frequency) * resonance;
```

### Parameter Update Rate

- Parameters update at control rate (usually 1-4ms blocks)
- Audio rate processing reads current parameter value
- Changes are sample-accurate (exact sample where change occurs)

## Constraints and Restrictions

### Audio Rate (Per-Sample)

**Cannot do:**
- Allocate memory
- Perform I/O (file operations, network, etc.)
- Call non-real-time functions
- Perform unbounded loops

**Can do:**
- Fixed-size loops (Codebox only)
- Arithmetic operations
- Trigonometric functions
- History/delay lookups

### Memory and Variables

**Automatic variables:**
- Allocated at compile time
- Lifetime: entire Gen object
- No dynamic allocation

**Example:**
```c
scale = 1.0 / 2.0;
out = in1 * scale;   // Uses compile-time constant, no dynamic allocation
```

## Real-Time Safety

Gen objects are **real-time safe** by design:

1. **No dynamic allocation** - Memory allocated at compile time
2. **Deterministic performance** - No garbage collection
3. **Bounded computation** - Can't have unbounded loops
4. **No blocking I/O** - Can't wait for external events

This allows Gen code to process audio reliably without dropouts or glitches.

## Example: Per-Sample Processing

```c
param frequency(440.0);
param amplitude(1.0);

phase = history(0);
out = sin(phase) * amplitude;
phase = phase + frequency / samplerate;
phase = (phase > 1.0) ? (phase - 1.0) : phase;
```

## Related Topics

- [GenExpr Overview](../genexpr/overview.md) - Language basics
- [Codebox Overview](../codebox/overview.md) - Alternative syntax
- [Type System](./types.md) - Type inference and coercion
- [Performance](./performance.md) - Performance considerations
