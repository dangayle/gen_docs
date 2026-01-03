# Trigonometry Operators

Trigonometric and hyperbolic functions. All angles are in radians.

## Basic Trigonometry

### Sine `sin`

**Syntax (GenExpr & Codebox):**
```c
sin(x)
```

**Description:**
Returns the sine of an angle in radians.

**Examples:**
```c
sin(0);                 // 0
sin(pi / 2);            // 1 (90°)
sin(pi);                // 0
sin(phase * twopi);     // sine oscillator
```

---

### Cosine `cos`

**Syntax (GenExpr & Codebox):**
```c
cos(x)
```

**Description:**
Returns the cosine of an angle in radians.

**Examples:**
```c
cos(0);                 // 1
cos(pi / 2);            // 0 (90°)
cos(pi);                // -1
```

---

### Tangent `tan`

**Syntax (GenExpr & Codebox):**
```c
tan(x)
```

**Description:**
Returns the tangent of an angle in radians (`sin(x) / cos(x)`).

**Examples:**
```c
tan(0);                 // 0
tan(pi / 4);            // 1 (45°)
```

---

## Inverse Trigonometry

### Arc Sine `asin`

**Syntax (GenExpr & Codebox):**
```c
asin(x)
```

**Description:**
Returns the angle whose sine is `x` (radians, range -π/2 to π/2).

**Examples:**
```c
asin(0);                // 0
asin(1);                // π/2 (90°)
asin(0.5);              // π/6 (30°)
```

---

### Arc Cosine `acos`

**Syntax (GenExpr & Codebox):**
```c
acos(x)
```

**Description:**
Returns the angle whose cosine is `x` (radians, range 0 to π).

**Examples:**
```c
acos(1);                // 0
acos(0);                // π/2 (90°)
acos(-1);               // π (180°)
```

---

### Arc Tangent `atan`

**Syntax (GenExpr & Codebox):**
```c
atan(x)
```

**Description:**
Returns the angle whose tangent is `x` (radians, range -π/2 to π/2).

**Examples:**
```c
atan(0);                // 0
atan(1);                // π/4 (45°)
```

---

### Arc Tangent (Two Arguments) `atan2`

**Syntax (GenExpr & Codebox):**
```c
atan2(y, x)
```

**Description:**
Returns the angle to the point `(x, y)`; handles all quadrants correctly (range -π to π).

**Examples:**
```c
atan2(0, 1);            // 0 (right)
atan2(1, 0);            // π/2 (up)
atan2(-1, 0);           // -π/2 (down)
atan2(1, 1);            // π/4 (45°)
theta = atan2(y, x);    // angle to point (x, y)
```

**Notes:**
- Prefer `atan2` over `atan` for 2D angles; it handles all quadrants.

---

## Hyperbolic Functions

### Hyperbolic Sine `sinh`

**Syntax (GenExpr & Codebox):**
```c
sinh(x)
```

**Description:**
Returns the hyperbolic sine `(e^x - e^{-x}) / 2`.

---

### Hyperbolic Cosine `cosh`

**Syntax (GenExpr & Codebox):**
```c
cosh(x)
```

**Description:**
Returns the hyperbolic cosine `(e^x + e^{-x}) / 2`.

---

### Hyperbolic Tangent `tanh`

**Syntax (GenExpr & Codebox):**
```c
tanh(x)
```

**Description:**
Returns the hyperbolic tangent `sinh(x) / cosh(x)` (range -1 to 1).

**Examples:**
```c
tanh(0);                // 0
tanh(1);                // ~0.76
tanh(in * drive);       // soft clipping
```

---

## Inverse Hyperbolic Functions

### Inverse Hyperbolic Sine `asinh`

**Syntax (GenExpr & Codebox):**
```c
asinh(x)
```

### Inverse Hyperbolic Cosine `acosh`

**Syntax (GenExpr & Codebox):**
```c
acosh(x)
```

### Inverse Hyperbolic Tangent `atanh`

**Syntax (GenExpr & Codebox):**
```c
atanh(x)
```

---

## Angle Conversion

### Degrees to Radians `radians`

**Syntax (GenExpr & Codebox):**
```c
radians(degrees)
degrees * DEGTORAD
```

**Description:**
Converts degrees to radians (`degrees * π / 180`).

**Examples:**
```c
radians(180);           // π
radians(90);            // π/2
45 * DEGTORAD;          // using constant
```

---

### Radians to Degrees `degrees`

**Syntax (GenExpr & Codebox):**
```c
degrees(radians)
radians * RADTODEG
```

**Description:**
Converts radians to degrees (`radians * 180 / π`).

**Examples:**
```c
degrees(pi);            // 180
degrees(pi / 2);        // 90
phase * RADTODEG;       // using constant
```

---

## Fast Approximations

### Fast Sine `fastsin`

**Syntax (GenExpr & Codebox):**
```c
fastsin(x)
```

**Description:**
Approximate sine; faster than `sin`, less accurate.

---

### Fast Cosine `fastcos`

**Syntax (GenExpr & Codebox):**
```c
fastcos(x)
```

**Description:**
Approximate cosine; faster than `cos`, less accurate.

---

### Fast Tangent `fasttan`

**Syntax (GenExpr & Codebox):**
```c
fasttan(x)
```

**Description:**
Approximate tangent; faster than `tan`, less accurate.

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

**Recommendation:** Use `fastsin`/`fastcos` for oscillators where speed matters. Use accurate versions for analysis.

---

## Common Patterns

### Sine Oscillator
```c
phase += freq / samplerate;
if (phase > 1) phase -= 1;
out = sin(phase * twopi) * amplitude;
```

### Angle Between Points
```c
theta = atan2(y2 - y1, x2 - x1);
```

### Soft Clipping with Tanh
```c
out = tanh(in * drive);
```

---

## Related Topics

- [Powers](./powers.md) - exp, log (hyperbolic definitions)
- [Constants](./constants.md) - pi, DEGTORAD, RADTODEG
- [Math Functions](./math.md) - cartopol, poltocar, atan2
