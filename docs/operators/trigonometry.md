# Trigonometry Operators

Trigonometric and hyperbolic functions. All angles are in radians.

## Basic Trigonometry

### Sine `sin`

**Syntax:**
```scheme
; GenExpr
(sin x)

; Codebox
sin(x)
```

**Description:**
Returns the sine of an angle in radians.

**Parameters:**
- `x` - Angle in radians

**Returns:**
- Sine value (range -1 to 1)

**Examples:**

GenExpr:
```scheme
(sin 0)           ; 0
(sin (/ pi 2))    ; 1 (sin of 90°)
(sin pi)          ; 0
(sin (* phase twopi)) ; Sine oscillator
```

Codebox:
```c
sin(0)            // 0
sin(pi / 2)       // 1 (90°)
sin(pi)           // 0
```

---

### Cosine `cos`

**Syntax:**
```scheme
; GenExpr
(cos x)

; Codebox
cos(x)
```

**Description:**
Returns the cosine of an angle in radians.

**Parameters:**
- `x` - Angle in radians

**Returns:**
- Cosine value (range -1 to 1)

**Examples:**

GenExpr:
```scheme
(cos 0)           ; 1
(cos (/ pi 2))    ; 0 (cos of 90°)
(cos pi)          ; -1
```

Codebox:
```c
cos(0)            // 1
cos(pi / 2)       // 0 (90°)
cos(pi)           // -1
```

---

### Tangent `tan`

**Syntax:**
```scheme
; GenExpr
(tan x)

; Codebox
tan(x)
```

**Description:**
Returns the tangent of an angle in radians. Equals sin(x) / cos(x).

**Parameters:**
- `x` - Angle in radians

**Returns:**
- Tangent value (unbounded)

**Examples:**

GenExpr:
```scheme
(tan 0)           ; 0
(tan (/ pi 4))    ; 1 (tan of 45°)
```

Codebox:
```c
tan(0)            // 0
tan(pi / 4)       // 1 (45°)
```

---

## Inverse Trigonometry

### Arc Sine `asin`

**Syntax:**
```scheme
; GenExpr
(asin x)

; Codebox
asin(x)
```

**Description:**
Returns the inverse sine (angle whose sine is x). Returns radians.

**Parameters:**
- `x` - Sine value (range -1 to 1)

**Returns:**
- Angle in radians (range -π/2 to π/2)

**Examples:**

GenExpr:
```scheme
(asin 0)          ; 0
(asin 1)          ; π/2 (90°)
(asin 0.5)        ; π/6 (30°)
```

Codebox:
```c
asin(0)           // 0
asin(1)           // π/2 (90°)
asin(0.5)         // π/6 (30°)
```

---

### Arc Cosine `acos`

**Syntax:**
```scheme
; GenExpr
(acos x)

; Codebox
acos(x)
```

**Description:**
Returns the inverse cosine (angle whose cosine is x). Returns radians.

**Parameters:**
- `x` - Cosine value (range -1 to 1)

**Returns:**
- Angle in radians (range 0 to π)

**Examples:**

GenExpr:
```scheme
(acos 1)          ; 0
(acos 0)          ; π/2 (90°)
(acos -1)         ; π (180°)
```

Codebox:
```c
acos(1)           // 0
acos(0)           // π/2 (90°)
acos(-1)          // π (180°)
```

---

### Arc Tangent `atan`

**Syntax:**
```scheme
; GenExpr
(atan x)

; Codebox
atan(x)
```

**Description:**
Returns the inverse tangent (angle whose tangent is x). Returns radians.

**Parameters:**
- `x` - Tangent value

**Returns:**
- Angle in radians (range -π/2 to π/2)

**Examples:**

GenExpr:
```scheme
(atan 0)          ; 0
(atan 1)          ; π/4 (45°)
```

Codebox:
```c
atan(0)           // 0
atan(1)           // π/4 (45°)
```

---

### Arc Tangent (Two Arguments) `atan2`

**Syntax:**
```scheme
; GenExpr
(atan2 y x)

; Codebox
atan2(y, x)
```

**Description:**
Returns the angle to the point (x, y). Handles all quadrants correctly.

**Parameters:**
- `y` - Y coordinate
- `x` - X coordinate

**Returns:**
- Angle in radians (range -π to π)

**Examples:**

GenExpr:
```scheme
(atan2 0 1)       ; 0 (right)
(atan2 1 0)       ; π/2 (up)
(atan2 -1 0)      ; -π/2 (down)
(atan2 1 1)       ; π/4 (45°)
theta = (atan2 y x) ; Angle to point (x, y)
```

Codebox:
```c
atan2(0, 1)       // 0 (right)
atan2(1, 0)       // π/2 (up)
atan2(1, 1)       // π/4 (45°)
```

**Notes:**
- atan2 is better than atan for 2D angles (handles all quadrants)
- Commonly used with cartopol/poltocar conversion

---

## Hyperbolic Functions

### Hyperbolic Sine `sinh`

**Syntax:**
```scheme
; GenExpr
(sinh x)

; Codebox
sinh(x)
```

**Description:**
Returns the hyperbolic sine: (e^x - e^(-x)) / 2

**Parameters:**
- `x` - Input value

**Returns:**
- Hyperbolic sine value

---

### Hyperbolic Cosine `cosh`

**Syntax:**
```scheme
; GenExpr
(cosh x)

; Codebox
cosh(x)
```

**Description:**
Returns the hyperbolic cosine: (e^x + e^(-x)) / 2

**Parameters:**
- `x` - Input value

**Returns:**
- Hyperbolic cosine value

---

### Hyperbolic Tangent `tanh`

**Syntax:**
```scheme
; GenExpr
(tanh x)

; Codebox
tanh(x)
```

**Description:**
Returns the hyperbolic tangent: sinh(x) / cosh(x)

**Parameters:**
- `x` - Input value

**Returns:**
- Hyperbolic tangent value (range -1 to 1)

**Examples:**

GenExpr:
```scheme
(tanh 0)          ; 0
(tanh 1)          ; ~0.76
(tanh in)         ; Soft clipping with tanh
```

Codebox:
```c
tanh(0)           // 0
tanh(1)           // ~0.76
tanh(x)           // Soft clipping
```

---

## Inverse Hyperbolic Functions

### Inverse Hyperbolic Sine `asinh`

**Syntax:**
```scheme
(asinh x)
```

**Description:**
Returns the inverse hyperbolic sine.

---

### Inverse Hyperbolic Cosine `acosh`

**Syntax:**
```scheme
(acosh x)
```

**Description:**
Returns the inverse hyperbolic cosine.

---

### Inverse Hyperbolic Tangent `atanh`

**Syntax:**
```scheme
(atanh x)
```

**Description:**
Returns the inverse hyperbolic tangent.

---

## Angle Conversion

### Degrees to Radians `radians`

**Syntax:**
```scheme
; GenExpr
(radians degrees)
(* degrees degtorad)

; Codebox
radians(degrees)
degrees * DEGTORAD
```

**Description:**
Converts degrees to radians by multiplying by π/180.

**Examples:**

GenExpr:
```scheme
(radians 180)     ; π
(radians 90)      ; π/2
(* 45 degtorad)   ; Using constant
```

---

### Radians to Degrees `degrees`

**Syntax:**
```scheme
; GenExpr
(degrees radians)
(* radians radtodeg)

; Codebox
degrees(radians)
radians * RADTODEG
```

**Description:**
Converts radians to degrees by multiplying by 180/π.

**Examples:**

GenExpr:
```scheme
(degrees pi)      ; 180
(degrees (/ pi 2)) ; 90
(* phase radtodeg) ; Using constant
```

---

## Fast Approximations

For performance-critical code, use faster approximations:

### Fast Sine `fastsin`

**Syntax:**
```scheme
(fastsin x)
```

**Description:**
Approximated sine (faster than sin, less accurate).

---

### Fast Cosine `fastcos`

**Syntax:**
```scheme
(fastcos x)
```

**Description:**
Approximated cosine (faster than cos, less accurate).

---

### Fast Tangent `fasttan`

**Syntax:**
```scheme
(fasttan x)
```

**Description:**
Approximated tangent (faster than tan, less accurate).

---

## Performance

| Function | Speed | Notes |
|----------|-------|-------|
| `sin`, `cos` | ~20 cycles | Accurate |
| `tan` | ~20 cycles | Accurate |
| `asin`, `acos`, `atan` | ~50 cycles | Slow |
| `atan2` | ~50 cycles | Slow but essential for angles |
| `sinh`, `cosh`, `tanh` | ~50 cycles | Hyperbolic functions |
| `fastsin`, `fastcos` | ~5 cycles | ~5x faster, less accurate |
| `fasttan` | ~5 cycles | ~5x faster |

**Recommendation:** Use `fastsin`/`fastcos` for oscillators where speed matters. Use accurate versions for signal analysis.

---

## Common Patterns

### Sine Oscillator
```scheme
; GenExpr
phase = phase + freq / samplerate;
if (phase > 1) { phase -= 1; }
out = sin(phase * twopi) * amplitude;

; Codebox
phase += freq / samplerate;
if (phase > 1) phase -= 1;
y = sin(phase * 2 * pi) * amplitude;
```

### Angle Between Points
```scheme
; GenExpr
theta = (atan2 (- y2 y1) (- x2 x1));

; Codebox
theta = atan2(y2 - y1, x2 - x1);
```

### Soft Clipping with Tanh
```scheme
; GenExpr
(tanh (* in drive))

; Codebox
tanh(x * drive)
```

---

## Related Topics

- [Powers](./powers.md) - exp, log (related to hyperbolic)
- [Constants](./constants.md) - pi, DEGTORAD, RADTODEG
- [Math Functions](./math.md) - cartopol, poltocar, atan2
