# Performance and Constraints

Gen is designed for real-time audio processing. Understanding performance characteristics is critical for writing code that doesn't drop audio or cause glitches.

## Execution Speed

### Audio Rate Performance

Gen code compiled to optimized C is very fast:

- **Typical operation:** < 1 microsecond
- **Complex filter:** 5-50 microseconds
- **Dense modulation:** 10-100 microseconds
- **Budget per sample (48kHz, 128-block):** ~333 microseconds

**Rule of thumb:** You can do substantial computation (dozens of operations) per sample.

### Optimization

The Gen compiler automatically:
- Inlines function calls
- Constant-folds expressions
- Eliminates dead code
- Vectorizes operations where possible

No manual optimization typically needed.

## Common Performance Patterns

### Fast Operations

These are essentially "free" in Gen:

```scheme
; All of these are very fast
x = input;                    ; Variable read
y = x + 1;                    ; Addition
z = x * 0.5;                  ; Multiplication
w = if (x > 0, x, -x);       ; Conditional (branch predicted)
```

### Moderate Operations

These cost a few cycles each:

```scheme
; All fast enough for audio rate
y = sin(x);                   ; Trigonometric (~10-20 cycles)
y = sqrt(x);                  ; Square root (~5-10 cycles)
y = pow(x, 2);               ; Power function (~20-30 cycles)
```

### Expensive Operations

Avoid these at audio rate:

```scheme
; SLOW - Don't use in tight loops at audio rate
y = log(x + 1);              ; Logarithm (~50+ cycles)
y = atan2(y, x);            ; Arc tangent (~50+ cycles)
```

### I/O Operations

**Not allowed at audio rate:**
- File operations
- Network I/O
- Memory allocation
- Anything that might block

These cause audio glitches or crashes.

## Loop Performance

### Fixed Loops

Safe at audio rate if bounded:

```scheme
; This is fine - loop count known at compile time
accumulator = 0;
for (i = 0; i < 10; i += 1) {
  accumulator += input * weights[i];
}
y = accumulator;
```

**Typical cost:** O(N) where N is the loop count

### Unbounded Loops

**Not allowed:**
```scheme
; INVALID - Loop count unknown, might block forever
while (some_condition) {
  y = y + 1;
}
```

## Memory and Allocation

### Variable Storage

Variables stored efficiently:

```scheme
; Each of these is a 32-bit value
a = 0.0;           ; float (4 bytes)
b = 1;             ; int (4 bytes)
c = history(x, 1); ; buffer pointer (0 overhead)
```

**Total memory:** Typically < 1 KB for reasonable Gen code

### History Buffer Memory

Each `history()` call uses memory:

```scheme
; Each requires storing N samples
y1 = history(x, 1);        ; 4 bytes (1 float)
y2 = history(x, 64);       ; 256 bytes (64 floats)
y3 = history(x, 44100);    ; ~176 KB (1 second at 44.1kHz)
```

**Rule:** Keep history depth reasonable (< 1 second typically)

### No Garbage Collection

Gen never garbage collects:

- Deterministic memory usage
- No hidden pauses
- Real-time safe

## Real-Time Safety Checklist

### Code that's SAFE at audio rate:

- ✅ Arithmetic operations
- ✅ Trigonometric functions (sin, cos, tan, etc.)
- ✅ Logarithms, exponentials, powers (fast enough)
- ✅ Fixed-size loops
- ✅ Variable read/write
- ✅ History lookups
- ✅ If/else conditions
- ✅ Function calls (inlined)

### Code that's UNSAFE:

- ❌ `while` loops (unbounded)
- ❌ Memory allocation (`new`, `malloc`, etc.) - not supported
- ❌ File I/O
- ❌ Network operations
- ❌ Blocking system calls
- ❌ Recursive functions without depth limit

## Latency

### Processing Latency

Gen typically adds minimal latency:

```
Processing latency ≈ Block size / Sample rate
Example: 128 samples at 48kHz = 2.67ms latency
```

### Avoiding Latency

Use history() for short delays:

```scheme
; This adds NO latency (just lookback)
previous = history(input, 1);
```

Minimize feedback loops:

```scheme
; Keep feedback loops simple to avoid latency buildup
feedback = 0;
y = input + feedback * 0.5;
feedback = y;
```

## Vector Operations

### Per-Sample vs. Per-Block

Gen processes **sample-by-sample**:

```scheme
; This runs once per sample
y = x * 0.5;
```

Not per-block or per-vector:

```scheme
; This is NOT faster than the scalar version
; (Gen does not have SIMD vector syntax)
y = x * 0.5;
```

### SIMD (Vector Instructions)

The Gen compiler may automatically use SIMD instructions on modern CPUs, but you don't control this directly.

## Compilation Performance

### Compile Time

First time a Gen patcher loads:
- Typically < 1 second
- Depends on code complexity
- Creates optimized binary

### Edit and Reload

Changing code triggers recompilation:
- Usually fast
- May have brief audio glitch during reload

**Best practice:** Test performance before deploying to live performance

## Profiling Your Code

### Count Operations

Simple way to estimate performance:

```scheme
; Estimate: ~5 operations per sample
y = input * scale;          ; 1 operation
y = y + offset;             ; 1 operation
y = sin(y);                 ; 1 operation
output = y * envelope;      ; 1 operation
state = state * feedback;   ; 1 operation
```

**At 48kHz:** 5 ops × 48,000 = 240,000 ops/second = cheap

### Use Max's CPU Meter

Check actual CPU load:
1. Open Max's CPU meter
2. Toggle your Gen patcher on/off
3. Note the difference

If < 2% per Gen object, you're fine.

## Performance Guidelines

### Strategies for Performance

1. **Keep it simple** - Simplest code is usually fastest
2. **Avoid expensive functions** - log(), atan2() only if necessary
3. **Use appropriate precision** - float is sufficient for audio
4. **Cache constants** - Calculate once, use many times
5. **Minimize state** - Only store what you need
6. **Use history()** - Cheaper than maintaining manual buffers

### Example: Efficient Filter

```scheme
; Good: Efficient, clear
param cutoff(1000);
param resonance(1.0);

; Pre-calculate coefficients
a = 0.1;  ; Simplified for example
b = 0.9;

; Simple filter
state = state * b + input * a;
y = state * resonance;
```

## Constraints by Type

### Type-Based Limits

Gen types have different performance characteristics:

**int (32-bit):**
- Fastest for arithmetic
- Suitable for counters, indices

**float (32-bit):**
- Suitable for audio signals
- Trigonometric and math functions
- Default choice

**signal:**
- Same as float internally
- Preferred for audio I/O

## Common Bottlenecks

### What Makes Code Slow

1. **Complex math** - log(), atan2(), pow()
2. **High history depth** - history(x, 10000)
3. **Large loops** - for (i = 0; i < 1000; i += 1)
4. **Inefficient algorithms** - O(N²) or worse

### Optimization Techniques

**Pre-calculate when possible:**
```scheme
; BAD: Recalculate every sample
param frequency(440);
two_pi_f = 2.0 * 3.14159 * frequency;
y = sin(phase * two_pi_f);

; GOOD: Cache the constant
param frequency(440);
two_pi_f = 2.0 * 3.14159 * frequency;  ; Calculated once
phase = 0;
y = sin(phase * two_pi_f);  ; Use cached value
```

**Use simpler approximations:**
```scheme
; Instead of log(x), consider:
log_approx = (x - 1.0) / (x + 1.0);  ; Faster approximation
```

## Related Topics

- [Execution Model](./execution-model.md) - How Gen executes
- [Memory and State](./memory-state.md) - Memory management
- [Type System](./types.md) - Type performance implications
- [GenExpr Overview](../genexpr/overview.md) - Language features
- [Codebox Overview](../codebox/overview.md) - Alternative syntax
