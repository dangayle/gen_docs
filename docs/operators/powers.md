# Powers and Logarithms

Exponential and logarithmic functions for growth, decay, and frequency operations.

## Exponential Functions

### Exponential `exp`

**Syntax:**
```scheme
; GenExpr
(exp x)

; Codebox
exp(x)
```

**Description:**
Returns e^x (e = 2.71828...).

**Parameters:**
- `x` - Exponent

**Returns:**
- e raised to the power x

**Examples:**

GenExpr:
```scheme
(exp 0)           ; 1
(exp 1)           ; e (~2.718)
(exp 2)           ; e^2 (~7.389)
```

Codebox:
```c
exp(0)            // 1
exp(1)            // e (~2.718)
exp(2)            // e^2 (~7.389)
```

---

### Base-2 Exponential `exp2`

**Syntax:**
```scheme
; GenExpr
(exp2 x)

; Codebox
exp2(x)
```

**Description:**
Returns 2^x (faster than pow(2, x)).

**Parameters:**
- `x` - Exponent

**Returns:**
- 2 raised to the power x

**Examples:**

GenExpr:
```scheme
(exp2 0)          ; 1
(exp2 1)          ; 2
(exp2 10)         ; 1024
frequency = (* 440 (exp2 semitones)) ; Exponential pitch scaling
```

Codebox:
```c
exp2(0)           // 1
exp2(1)           // 2
exp2(10)          // 1024
frequency = 440 * exp2(semitones / 12); // MIDI pitch scaling
```

**Notes:**
- Prefer exp2 over pow(2, x) for performance
- Essential for pitch/frequency calculations (1 octave = 2x frequency)

---

## Logarithmic Functions

### Natural Logarithm `ln`

**Syntax:**
```scheme
; GenExpr
(ln x)

; Codebox
ln(x)
```

**Description:**
Returns the natural logarithm (base e) of x. Inverse of exp(x).

**Parameters:**
- `x` - Input (must be > 0)

**Returns:**
- Natural logarithm

**Examples:**

GenExpr:
```scheme
(ln 1)            ; 0
(ln 2.718)        ; ~1
(ln (exp 5))      ; 5
```

Codebox:
```c
ln(1)             // 0
ln(2.718)         // ~1
```

---

### Base-10 Logarithm `log10`

**Syntax:**
```scheme
; GenExpr
(log10 x)

; Codebox
log10(x)
```

**Description:**
Returns the base-10 logarithm of x. Often used for dB calculations.

**Parameters:**
- `x` - Input (must be > 0)

**Returns:**
- Base-10 logarithm

**Examples:**

GenExpr:
```scheme
(log10 1)         ; 0
(log10 10)        ; 1
(log10 100)       ; 2
db = (* 20 (log10 rms)) ; RMS to dB conversion
```

---

### Base-2 Logarithm `log2`

**Syntax:**
```scheme
; GenExpr
(log2 x)

; Codebox
log2(x)
```

**Description:**
Returns the base-2 logarithm of x. Used for octave calculations.

**Parameters:**
- `x` - Input (must be > 0)

**Returns:**
- Base-2 logarithm

**Examples:**

GenExpr:
```scheme
(log2 1)          ; 0
(log2 2)          ; 1
(log2 8)          ; 3
octaves = (log2 (/ freq basefreq)) ; Octaves between frequencies
```

Codebox:
```c
octaves = log2(freq / basefreq); // Octaves between two frequencies
semitones = 12 * log2(freq / basefreq); // Semitones
```

---

### Generic Logarithm `log`

**Syntax:**
```scheme
; GenExpr
(log x)
(log x base)

; Codebox
log(x)
log(x, base)
```

**Description:**
Returns the logarithm of x in a specified base (default base e).

**Parameters:**
- `x` - Input (must be > 0)
- `base` - Logarithm base (default e)

**Returns:**
- Logarithm in specified base

---

## Power Functions

### Power `pow`

**Syntax:**
```scheme
; GenExpr
(pow base exponent)

; Codebox
pow(base, exponent)
```

**Description:**
Returns base raised to the power of exponent.

**Parameters:**
- `base` - Base value
- `exponent` - Exponent

**Returns:**
- base^exponent

**Examples:**

GenExpr:
```scheme
(pow 2 3)         ; 8
(pow 2 0.5)       ; ~1.414 (sqrt(2))
(pow 2 -1)        ; 0.5
envelope = (pow normalized 2.0) ; Quadratic envelope curve
```

Codebox:
```c
pow(2, 3)         // 8
pow(2, 0.5)       // ~1.414 (sqrt)
pow(x, 0.5)       // Same as sqrt(x)
```

**Notes:**
- More general than exp2 but slower
- Use exp2 for base-2 exponentiation (more efficient)
- Use sqrt for square root (more efficient)

---

### Square Root `sqrt`

**Syntax:**
```scheme
; GenExpr
(sqrt x)

; Codebox
sqrt(x)
```

**Description:**
Returns the square root of x. Equivalent to pow(x, 0.5) but faster.

**Parameters:**
- `x` - Input (must be ≥ 0)

**Returns:**
- Square root of x

**Examples:**

GenExpr:
```scheme
(sqrt 4)          ; 2
(sqrt 2)          ; ~1.414
magnitude = (sqrt (+ (* x x) (* y y))) ; Magnitude of 2D vector
```

Codebox:
```c
sqrt(4)           // 2
sqrt(2)           // ~1.414
magnitude = sqrt(x*x + y*y); // 2D magnitude
```

**Notes:**
- Prefer over pow(x, 0.5)
- Use hypot instead for magnitude calculation

---

## Fast Approximations

### Fast Exponential `fastexp`

**Syntax:**
```scheme
; GenExpr
(fastexp x)

; Codebox
fastexp(x)
```

**Description:**
Approximated exponential (faster than exp, less accurate). Uses polynomial approximation.

**Parameters:**
- `x` - Exponent

**Returns:**
- Approximate e^x

**Performance:**
- ~5 cycles (vs ~50 for accurate exp)
- Accurate to ~0.5% for typical audio range
- Good for envelope generators, frequency modulation

**Examples:**

GenExpr:
```scheme
envelope = (fastexp (* -5 time)) ; Fast exponential decay
```

Codebox:
```c
envelope = fastexp(-5 * time); // Fast decay
```

---

### Fast Power `fastpow`

**Syntax:**
```scheme
; GenExpr
(fastpow base exponent)

; Codebox
fastpow(base, exponent)
```

**Description:**
Approximated power function (faster than pow, less accurate).

**Parameters:**
- `base` - Base value (must be > 0)
- `exponent` - Exponent

**Returns:**
- Approximate base^exponent

**Performance:**
- ~10 cycles (vs ~100 for accurate pow)
- Good for waveshaping curves
- Acceptable for many real-time effects

**Examples:**

GenExpr:
```scheme
distorted = (fastpow (abs x) 2.0) ; Fast quadratic distortion
envelope = (fastpow (- 1 time) 1.5) ; Fast exponential release
```

Codebox:
```c
distorted = fastpow(abs(x), 2.0); // Fast quadratic shaping
```

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
```scheme
; GenExpr - accurate
envelope = (exp (* -decay-time sample-index)) ; Natural decay

; GenExpr - fast
envelope = (fastexp (* -decay-time sample-index))

; Codebox - accurate
envelope = exp(-decay * sample_index);

; Codebox - fast
envelope = fastexp(-decay * sample_index);
```

### MIDI Pitch to Frequency
```scheme
; GenExpr
; MIDI 60 = 261.63 Hz
frequency = (* 440 (exp2 (/ (- midi-note 69) 12)))

; Codebox
frequency = 440 * exp2((midi_note - 69) / 12.0);
```

### Frequency to MIDI Pitch
```scheme
; GenExpr
midi-note = (+ 69 (* 12 (log2 (/ freq 440))))

; Codebox
midi_note = 69 + 12 * log2(freq / 440);
```

### RMS to Decibels
```scheme
; GenExpr
db = (* 20 (log10 rms)) ; RMS > 1 gives positive dB

; Codebox
db = 20 * log10(rms);
```

### Decibels to Linear
```scheme
; GenExpr
linear = (pow 10 (/ db 20))

; Codebox
linear = pow(10, db / 20.0);
```

### Exponential Curve (0 to 1)
```scheme
; GenExpr
; Slow at start, fast at end
curved = (- 1 (fastexp (* -5 normalized)))

; Codebox
curved = 1 - fastexp(-5 * normalized);
```

### Quadratic Waveshaping
```scheme
; GenExpr
; Fast approximation
shaped = (fastpow (abs in) 2.0)

; Codebox
shaped = fastpow(abs(x), 2.0);
```

---

## Related Topics

- [Math Functions](./math.md) - hypot, absdiff
- [Trigonometry](./trigonometry.md) - sinh, cosh, tanh (hyperbolic)
- [Constants](./constants.md) - e, pi
