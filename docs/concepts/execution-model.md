# Execution Model

Gen code executes in distinct phases. Understanding these phases is critical for writing correct Gen code and knowing when certain operations are allowed.

## Execution Phases

### Phase 1: Initialization (Init)

**When:** Once, when the Gen patcher compiles or when explicitly triggered

**Characteristics:**
- Runs once before audio processing begins
- Can perform setup computations
- Can allocate memory and initialize state
- Runs at control rate (not audio rate)

**Allowed operations:**
- Variable initialization
- Parameter declarations
- State setup

**Example:**
```scheme
; Initialization happens here
scale = 1.0 / max(input_range, 0.001);

; Main computation
out = in * scale;
```

### Phase 2: Audio Rate (Per-Sample or Per-Block)

**When:** For each audio sample (or block of samples) being processed

**Characteristics:**
- Runs repeatedly during audio playback
- Must be fast - it's called at audio rate
- No expensive operations
- Cannot allocate memory
- Cannot perform I/O

**Allowed operations:**
- Read inputs (`in`, `in1`, `in2`, ...)
- Perform computations
- Write outputs (`out`, `out1`, `out2`, ...)
- Update state

**Example:**
```scheme
; This runs for EVERY sample
filtered = input * gain + (previous_output * feedback);
y = filtered;
```

## Inlet and Outlet Semantics

### Inlets

Inlets (`in`, `in1`, `in2`, ...) provide:
- Current input value at the current sample
- Read-only access
- Synchronized with Max's message and audio timing

**GenExpr syntax:**
```scheme
; Read from inlets (multiple syntaxes)
value = in;        ; First inlet
value = in1;       ; First inlet (explicit)
x = in2;           ; Second inlet
```

**Codebox syntax:**
```c
// Alternative inlet naming
y = x;             ; x is first inlet
y = x1;            ; First inlet (explicit)
z = x2;            ; Second inlet
```

### Outlets

Outlets (`out`, `out1`, `out2`, ...) are where you write output values:

**GenExpr:**
```scheme
out = computed_value;      ; Write to first outlet
out1 = value1;
out2 = value2;
```

**Codebox:**
```c
y = computed_value;        ; y is first outlet
y1 = value1;
y2 = value2;
```

### Automatic Inlet/Outlet Creation

The `expr` and `codebox` operators automatically create inlets and outlets based on what you reference:

```scheme
; This code automatically creates:
; - 2 inlets (because in1 and in2 are used)
; - 1 outlet (because out is assigned)

out = in1 + in2;
```

## State and History

### Variables and State

Variables persist across samples within the same block:

```scheme
; This variable persists across iterations
accumulator = 0;

; This will accumulate over samples
for (i = 0; i < 10; i += 1) {
  accumulator += input[i];  ; Pseudo-code
}
```

### History: Accessing Previous Values

Gen provides `history()` for accessing previous sample values:

```scheme
; Simple one-sample delay
previous = history(input, 1);    ; Get previous sample
y = previous;
```

**History syntax:**
```scheme
past_value = history(signal, N);     ; Get N samples back
```

Where:
- `signal` - The signal to access
- `N` - Number of samples back (1 = last sample, 2 = two samples ago)

**Example - Simple highpass filter:**
```scheme
; Difference equation: y[n] = x[n] - x[n-1]
previous = history(x, 1);
y = x - previous;
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

```scheme
param frequency(440.0);
param resonance(1.0);

; frequency and resonance can be changed from outside
; without recompiling
y = sin(x * frequency) * resonance;
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
- Fixed-size loops
- Arithmetic operations
- Trigonometric functions
- History/delay lookups

### Memory and Variables

**Automatic variables:**
- Allocated at compile time
- Lifetime: entire Gen object
- No dynamic allocation

**Example:**
```scheme
; These are allocated once at compile time
buffer_size = 1024;
scaling_factor = 1.0 / buffer_size;

; Safe to use at audio rate
out = in * scaling_factor;
```

## Real-Time Safety

Gen objects are **real-time safe** by design:

1. **No dynamic allocation** - Memory allocated at compile time
2. **Deterministic performance** - No garbage collection
3. **Bounded computation** - Can't have unbounded loops
4. **No blocking I/O** - Can't wait for external events

This allows Gen code to process audio reliably without dropouts or glitches.

## Example: Complete Lifecycle

```scheme
; INITIALIZATION PHASE
; Happens once when Gen patcher loads
param frequency(440.0);
param amplitude(1.0);
phase = 0;

; AUDIO RATE PHASE
; Happens for each sample
out = sin(phase) * amplitude;
phase += frequency / samplerate;

; Handle phase wrapping
if (phase > 1.0) {
  phase -= 1.0;
}
```

## Related Topics

- [GenExpr Overview](../genexpr/overview.md) - Language basics
- [Codebox Overview](../codebox/overview.md) - Alternative syntax
- [Type System](./types.md) - Type inference and coercion
- [Performance](./performance.md) - Performance considerations
