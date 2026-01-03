# Powers and Logarithms

Exponential and logarithmic functions for growth, decay, and frequency operations.

## Exponential Functions

### Exponential `exp`

**Syntax (GenExpr & Codebox):**
```c
exp(x)
```

**Description:**
Returns e^x.

**Examples:**
```c
exp(0);                  // 1
exp(1);                  // e (~2.718)
exp(2);                  // e^2 (~7.389)
```

---

### Base-2 Exponential `exp2`

**Syntax (GenExpr & Codebox):**
```c
exp2(x)
```

**Description:**
Returns 2^x (faster than `pow(2, x)`).

**Examples:**
```c
exp2(0);                 // 1
exp2(1);                 // 2
exp2(10);                // 1024
frequency = 440 * exp2(semitones / 12.0);   // MIDI pitch scaling
```

**Notes:**
- Prefer `exp2` over `pow(2, x)` for performance
- Essential for pitch/frequency calculations (1 octave = 2× frequency)

---

## Logarithmic Functions

### Natural Logarithm `ln`

**Syntax (GenExpr & Codebox):**
```c
ln(x)
```

**Description:**
Returns the natural logarithm (base e). Inverse of `exp(x)`.

**Examples:**
```c
ln(1);                   // 0
ln(exp(5));              // 5
```

---

### Base-10 Logarithm `log10`

**Syntax (GenExpr & Codebox):**
```c
log10(x)
```

**Description:**
Returns the base-10 logarithm. Often used for dB calculations.

**Examples:**
```c
log10(1);                // 0
log10(10);               // 1
db = 20 * log10(rms);    // RMS to dB
```

---

### Base-2 Logarithm `log2`

**Syntax (GenExpr & Codebox):**
```c
log2(x)
```

**Description:**
Returns the base-2 logarithm. Useful for octaves and semitone math.

**Examples:**
```c
octaves = log2(freq / basefreq);
semitones = 12 * log2(freq / basefreq);
```

---

### Generic Logarithm `log`

**Syntax (GenExpr & Codebox):**
```c
log(x)
log(x, base)
```

**Description:**
Returns the logarithm of `x` in the given base (default base e).

---

## Power Functions

### Power `pow`

**Syntax (GenExpr & Codebox):**
```c
pow(base, exponent)
```

**Description:**
Returns `base` raised to `exponent`.

**Examples:**
```c
pow(2, 3);               // 8
pow(2, 0.5);             // ~1.414 (sqrt)
pow(x, 0.5);             // same as sqrt(x)
```

**Notes:**
- More general than `exp2` but slower
- Use `sqrt` for square roots when possible

---

### Square Root `sqrt`

**Syntax (GenExpr & Codebox):**
```c
sqrt(x)
```

**Description:**
Returns the square root of `x`. Faster than `pow(x, 0.5)`.

**Examples:**
```c
sqrt(4);                 // 2
sqrt(2);                 // ~1.414
magnitude = sqrt(x*x + y*y);   // 2D magnitude
```

---

## Fast Approximations

### Fast Exponential `fastexp`

**Syntax (GenExpr & Codebox):**
```c
fastexp(x)
```

**Description:**
Approximate `exp(x)`; faster, less accurate.

**Examples:**
```c
envelope = fastexp(-5 * time);   // fast decay
```

**Notes:**
- ~5 cycles vs ~50 for accurate `exp`
- Accurate to ~0.5% for typical audio ranges

---

### Fast Power `fastpow`

**Syntax (GenExpr & Codebox):**
```c
fastpow(base, exponent)
```

**Description:**
Approximate `pow(base, exponent)`; faster, less accurate.

**Examples:**
```c
distorted = fastpow(abs(in), 2.0);   // fast quadratic shaping
```

**Notes:**
- ~10 cycles vs ~100 for accurate `pow`
- Useful for waveshaping and envelope curves

---

## Performance Comparison

| Function | Speed | Use Case |
|----------|-------|----------|
| `sqrt` | ~10 cycles | Magnitude, normalization |
| `exp2` | ~20 cycles | Pitch/frequency scaling |
| `exp` | ~50 cycles | Envelopes, decay |
| `log2` | ~50 cycles | Octave calculations |
| `log10` | ~50 cycles | dB conversions |
| `ln` | ~50 cycles | Mathematical operations |
| `pow` | ~100 cycles | General exponentiation |
| `fastexp` | ~5 cycles | Audio-rate envelopes |
| `fastpow` | ~10 cycles | Curves and waveshaping |

---

## Common Patterns

### Exponential Envelope
```c
// Accurate
envelope = exp(-decay * sample_index);
// Fast
envelope = fastexp(-decay * sample_index);
```

### MIDI Pitch to Frequency
```c
// MIDI 60 = 261.63 Hz
frequency = 440 * exp2((midi_note - 69) / 12.0);
```

### Frequency to MIDI Pitch
```c
midi_note = 69 + 12 * log2(freq / 440);
```

### RMS to Decibels
```c
db = 20 * log10(rms);   // RMS > 1 gives positive dB
```

### Decibels to Linear
```c
linear = pow(10, db / 20.0);
```

### Exponential Curve (0 to 1)
```c
curved = 1 - fastexp(-5 * normalized);   // slow start, fast end
```

### Quadratic Waveshaping
```c
shaped = fastpow(abs(in), 2.0);   // fast approximation
```

---

## Related Topics

- [Math Functions](./math.md) - hypot, absdiff
- [Trigonometry](./trigonometry.md) - sinh, cosh, tanh (hyperbolic)
- [Constants](./constants.md) - e, pi
