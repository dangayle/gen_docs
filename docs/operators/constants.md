# Mathematical Constants

Pre-defined constants for common mathematical values and conversions.

## Mathematical Constants

### Pi `pi`

**Syntax:**
```scheme
; GenExpr
pi

; Codebox
pi
```

**Description:**
The mathematical constant π ≈ 3.14159265359. The ratio of a circle's circumference to its diameter.

**Returns:**
- 3.14159265359

**Examples:**

GenExpr:
```scheme
(sin pi)          ; 0
(* 2 pi)          ; ~6.283 (full circle in radians)
circumference = (* 2 pi radius)
```

Codebox:
```c
sin(pi)           // 0
y = sin(x * pi);
circumference = 2 * pi * radius;
```

---

### Tau (Two Pi) `twopi`

**Syntax:**
```scheme
; GenExpr
twopi

; Codebox
twopi
```

**Description:**
The constant 2π ≈ 6.28318530718. Often used for circular operations (represents one full rotation).

**Returns:**
- 6.28318530718

**Examples:**

GenExpr:
```scheme
; Sine oscillator from phasor (0 to 1)
(sin (* phase twopi))

; Convert angle from normalized (0-1) to radians
(sin (* normalized twopi))
```

Codebox:
```c
// Sine from normalized phase
y = sin(phase * twopi);

// Full rotation
angle_radians = normalized * twopi;
```

---

### Euler's Number `e`

**Syntax:**
```scheme
; GenExpr
e

; Codebox
e
```

**Description:**
The mathematical constant e ≈ 2.71828182846. Base of natural logarithm.

**Returns:**
- 2.71828182846

**Examples:**

GenExpr:
```scheme
(exp 0)           ; 1 (e^0)
(ln e)            ; 1
(pow e 2)         ; e^2 (~7.389)
```

Codebox:
```c
exp(1)            // e
ln(e)             // 1
```

---

### Golden Ratio `phi`

**Syntax:**
```scheme
; GenExpr
phi

; Codebox
phi
```

**Description:**
The golden ratio φ ≈ 1.61803398875. Appears in nature and aesthetics.

**Returns:**
- 1.61803398875

**Examples:**

GenExpr:
```scheme
; Golden ratio for proportions
width = 100;
height = (* width phi)

; Fibonacci relationship
phi = (* 2 (sin (/ pi 5)))
```

Codebox:
```c
width = 100;
height = width * phi;
```

---

## Square Root Constants

### Square Root of 2 `sqrt2`

**Syntax:**
```scheme
sqrt2
```

**Description:**
√2 ≈ 1.41421356237

**Returns:**
- 1.41421356237

**Examples:**

GenExpr:
```scheme
; Equal temperament semitone ratio
semitone = (pow sqrt2 (/ 1 12)) ; 2^(1/12)

; Normalize 2-channel signal
rms = (* magnitude (/ 1 sqrt2))
```

---

### Reciprocal of Square Root of 2 `sqrt1_2`

**Syntax:**
```scheme
sqrt1_2
```

**Description:**
1/√2 ≈ 0.70710678118 (or √2/2). Commonly used for normalization.

**Returns:**
- 0.70710678118

**Examples:**

GenExpr:
```scheme
; Normalize stereo RMS
rms = (* magnitude sqrt1_2)

; Equal-power pan
left = (* audio (sin (* pan (/ pi 2))))
right = (* audio (cos (* pan (/ pi 2))))
```

---

## Angle Conversion Constants

### Degrees to Radians `degtorad`

**Syntax:**
```scheme
; GenExpr
degtorad
(* degrees degtorad)

; Codebox
DEGTORAD
degrees * DEGTORAD
```

**Description:**
Conversion factor π/180 ≈ 0.0174532925199. Multiply degrees by this to get radians.

**Returns:**
- 0.0174532925199

**Examples:**

GenExpr:
```scheme
; Convert 180 degrees to radians
radians = (* 180 degtorad)  ; π

; Convert angle to radians
angle-rad = (* angle degtorad)
```

Codebox:
```c
radians = 180 * DEGTORAD;  // π
angle_rad = angle * DEGTORAD;
```

---

### Radians to Degrees `radtodeg`

**Syntax:**
```scheme
; GenExpr
radtodeg
(* radians radtodeg)

; Codebox
RADTODEG
radians * RADTODEG
```

**Description:**
Conversion factor 180/π ≈ 57.2957795131. Multiply radians by this to get degrees.

**Returns:**
- 57.2957795131

**Examples:**

GenExpr:
```scheme
; Convert pi radians to degrees
degrees = (* pi radtodeg)    ; 180

; Convert angle to degrees
angle-deg = (* angle radtodeg)
```

Codebox:
```c
degrees = pi * RADTODEG;  // 180
angle_deg = angle * RADTODEG;
```

---

## Logarithm Constants

### Natural Log of 2 `ln2`

**Syntax:**
```scheme
ln2
```

**Description:**
ln(2) ≈ 0.693147180560. Used in exponential relationships.

**Returns:**
- 0.693147180560

---

### Natural Log of 10 `ln10`

**Syntax:**
```scheme
ln10
```

**Description:**
ln(10) ≈ 2.302585092994. Used in dB/frequency relationships.

**Returns:**
- 2.302585092994

---

## Common Usage Patterns

### Frequency to MIDI Note
```scheme
; GenExpr
; A4 = 440 Hz = MIDI note 69
midi-note = (+ 69 (* 12 (log2 (/ freq 440))))

; Codebox
midi_note = 69 + 12 * log2(freq / 440.0);
```

### MIDI Note to Frequency
```scheme
; GenExpr
freq = (* 440 (exp2 (/ (- midi-note 69) 12)))

; Codebox
freq = 440 * exp2((midi_note - 69) / 12.0);
```

### Normalize Stereo RMS
```scheme
; GenExpr
rms = (* (hypot left right) sqrt1_2)

; Codebox
rms = hypot(left, right) * sqrt1_2;
```

### RMS to Decibels
```scheme
; GenExpr
db = (* 20 (log10 rms))

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

### Angle Conversion
```scheme
; GenExpr
radians = (* degrees degtorad)
degrees = (* radians radtodeg)

; Codebox
radians = degrees * DEGTORAD;
degrees = radians * RADTODEG;
```

### Sine Oscillator
```scheme
; GenExpr
; Using phasor output (0 to 1) and twopi
(sin (* phase twopi))

; Using manual phase (0 to 2π)
(sin (* phase twopi))
```

---

## Related Topics

- [Trigonometry](./trigonometry.md) - sin, cos, atan2
- [Powers](./powers.md) - exp, exp2, log, log2, log10
- [Math Functions](./math.md) - scale, clamp
