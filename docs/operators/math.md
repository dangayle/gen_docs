# Math Functions

Common mathematical operations and transformations.

## Absolute Value `abs`

**Syntax:**
```scheme
; GenExpr
(abs x)

; Codebox
abs(x)
```

**Description:**
Returns the absolute value (distance from zero) of a number.

**Parameters:**
- `x` - Input value (int or float)

**Returns:**
- Absolute value (always positive)

**Examples:**

GenExpr:
```scheme
(abs 5)           ; 5
(abs -5)          ; 5
(abs in)          ; Absolute value of input
```

Codebox:
```c
abs(5)            // 5
abs(-5)           // 5
abs(x)            // Absolute value of input
```

---

## Minimum `min`

**Syntax:**
```scheme
; GenExpr
(min a b)
(min a b c d ...)

; Codebox
min(a, b)
min(a, b, c, d, ...)
```

**Description:**
Returns the smallest of the input values.

**Parameters:**
- `a`, `b`, ... - Values to compare

**Returns:**
- Minimum value

**Examples:**

GenExpr:
```scheme
(min 5 3)         ; 3
(min 10 5 20 1)   ; 1
(min in threshold) ; Lower of input or threshold
```

Codebox:
```c
min(5, 3)         // 3
min(10, 5, 20, 1) // 1
min(x, threshold) // Lower value
```

---

## Maximum `max`

**Syntax:**
```scheme
; GenExpr
(max a b)
(max a b c d ...)

; Codebox
max(a, b)
max(a, b, c, d, ...)
```

**Description:**
Returns the largest of the input values.

**Parameters:**
- `a`, `b`, ... - Values to compare

**Returns:**
- Maximum value

**Examples:**

GenExpr:
```scheme
(max 5 3)         ; 5
(max 10 5 20 1)   ; 20
(max in threshold) ; Higher of input or threshold
```

Codebox:
```c
max(5, 3)         // 5
max(10, 5, 20, 1) // 20
max(x, threshold) // Higher value
```

---

## Clamp (Clip) `clamp` or `clip`

**Syntax:**
```scheme
; GenExpr
(clamp x min max)
(clip x min max)

; Codebox
clamp(x, min, max)
clip(x, min, max)
```

**Description:**
Constrains a value to be within a specified range.

**Parameters:**
- `x` - Value to clamp
- `min` - Minimum allowed value
- `max` - Maximum allowed value

**Returns:**
- Clamped value (within [min, max])

**Examples:**

GenExpr:
```scheme
(clamp 5 0 10)    ; 5 (within range)
(clamp -5 0 10)   ; 0 (clipped to min)
(clamp 15 0 10)   ; 10 (clipped to max)
(clamp in 0 1)    ; Constrain input to [0, 1]
```

Codebox:
```c
clamp(5, 0, 10)   // 5
clamp(-5, 0, 10)  // 0
clamp(15, 0, 10)  // 10
clamp(x, 0, 1)    // Constrain to [0, 1]
```

---

## Wrap

**Syntax:**
```scheme
; GenExpr
(wrap x min max)

; Codebox
wrap(x, min, max)
```

**Description:**
Wraps a value within a range (like modulo but always positive).

**Parameters:**
- `x` - Value to wrap
- `min` - Minimum value (default 0)
- `max` - Maximum value (default 1)

**Returns:**
- Wrapped value within [min, max)

**Examples:**

GenExpr:
```scheme
(wrap -0.5 0 1)   ; 0.5 (wraps around)
(wrap 1.5 0 1)    ; 0.5 (wraps around)
(wrap phase 0 twopi) ; Wrap phase to [0, 2π)
```

Codebox:
```c
wrap(-0.5, 0, 1)  // 0.5 (wraps around)
wrap(1.5, 0, 1)   // 0.5 (wraps around)
```

**Notes:**
- Different from modulo for negative numbers
- Useful for keeping values in a cyclic range
- Default range: 0 to 1

---

## Fold

**Syntax:**
```scheme
; GenExpr
(fold x min max)

; Codebox
fold(x, min, max)
```

**Description:**
Folds a value within a range by reflecting at boundaries (like a bouncing ball).

**Parameters:**
- `x` - Value to fold
- `min` - Minimum value (default 0)
- `max` - Maximum value (default 1)

**Returns:**
- Folded value within [min, max]

**Examples:**

GenExpr:
```scheme
(fold 1.5 0 1)    ; 0.5 (reflects at 1)
(fold 2.5 0 1)    ; 0.5 (reflects twice)
```

Codebox:
```c
fold(1.5, 0, 1)   // 0.5 (reflects)
fold(2.5, 0, 1)   // 0.5 (reflects)
```

---

## Negate `neg`

**Syntax:**
```scheme
; GenExpr
(neg x)

; Codebox
neg(x)
-x
```

**Description:**
Returns the negation of a value (multiplies by -1).

**Parameters:**
- `x` - Input value

**Returns:**
- Negated value

**Examples:**

GenExpr:
```scheme
(neg 5)           ; -5
(neg in)          ; Negate input
```

Codebox:
```c
neg(5)            // -5
-x                // Negate input
```

---

## Sign `sign`

**Syntax:**
```scheme
; GenExpr
(sign x)

; Codebox
sign(x)
```

**Description:**
Returns the sign of a value: -1 for negative, 0 for zero, 1 for positive.

**Parameters:**
- `x` - Input value

**Returns:**
- -1, 0, or 1

**Examples:**

GenExpr:
```scheme
(sign 5)          ; 1
(sign -5)         ; -1
(sign 0)          ; 0
```

Codebox:
```c
sign(5)           // 1
sign(-5)          // -1
sign(0)           // 0
```

---

## Scale

**Syntax:**
```scheme
; GenExpr
(scale x in_min in_max out_min out_max)
(scale x in_min in_max out_min out_max curve)

; Codebox
scale(x, in_min, in_max, out_min, out_max)
scale(x, in_min, in_max, out_min, out_max, curve)
```

**Description:**
Maps a value from one range to another, with optional exponential curve.

**Parameters:**
- `x` - Value to scale
- `in_min` - Input range minimum
- `in_max` - Input range maximum
- `out_min` - Output range minimum
- `out_max` - Output range maximum
- `curve` - Exponential curve (optional, default 1.0 = linear)

**Returns:**
- Scaled value

**Examples:**

GenExpr:
```scheme
; Map 0-127 (MIDI) to 0-1
(scale in 0 127 0 1)

; Map normalized input to frequency range
(scale in 0 1 20 20000)

; Exponential curve (curve > 1 = exponential)
(scale in 0 1 0 1 2)
```

Codebox:
```c
scale(in, 0, 127, 0, 1)        // MIDI to 0-1
scale(in, 0, 1, 20, 20000)     // To frequency range
scale(in, 0, 1, 0, 1, 2)       // Exponential curve
```

**Notes:**
- No automatic clamping (values outside input range extrapolate)
- Default curve: 1.0 (linear)
- Curve > 1: exponential expansion
- Curve < 1: exponential compression

---

## Absolute Difference `absdiff`

**Syntax:**
```scheme
; GenExpr
(absdiff a b)

; Codebox
absdiff(a, b)
```

**Description:**
Returns the absolute difference between two values: |a - b|

**Parameters:**
- `a` - First value
- `b` - Second value

**Returns:**
- Absolute difference

**Examples:**

GenExpr:
```scheme
(absdiff 5 3)     ; 2
(absdiff -5 3)    ; 8
(absdiff in1 in2) ; Distance between inputs
```

Codebox:
```c
absdiff(5, 3)     // 2
absdiff(-5, 3)    // 8
absdiff(x1, x2)   // Distance between inputs
```

---

## Cartesian to Polar `cartopol`

**Syntax:**
```scheme
; GenExpr
(cartopol x y)

; Codebox
cartopol(x, y)
```

**Description:**
Converts Cartesian coordinates (x, y) to polar coordinates (radius, angle).

**Parameters:**
- `x` - Cartesian X coordinate
- `y` - Cartesian Y coordinate

**Returns:**
- Two values: radius (magnitude), angle (in radians)

**Examples:**

GenExpr:
```scheme
r, theta = (cartopol 3 4)  ; r=5, theta=0.927... (atan2(4,3))
```

Codebox:
```c
r, theta = cartopol(3, 4); // r=5, theta=0.927...
```

---

## Polar to Cartesian `poltocar`

**Syntax:**
```scheme
; GenExpr
(poltocar r theta)

; Codebox
poltocar(r, theta)
```

**Description:**
Converts polar coordinates (radius, angle) to Cartesian coordinates (x, y).

**Parameters:**
- `r` - Radius (magnitude)
- `theta` - Angle (in radians)

**Returns:**
- Two values: x, y

**Examples:**

GenExpr:
```scheme
x, y = (poltocar 5 0.927)  ; Converts back to ~(3, 4)
```

Codebox:
```c
x, y = poltocar(5, 0.927); // Converts back to ~(3, 4)
```

---

## Hypotenuse `hypot`

**Syntax:**
```scheme
; GenExpr
(hypot x y)

; Codebox
hypot(x, y)
```

**Description:**
Returns the length of the hypotenuse: √(x² + y²). Equivalent to radius in polar coordinates.

**Parameters:**
- `x` - X component
- `y` - Y component

**Returns:**
- Hypotenuse length

**Examples:**

GenExpr:
```scheme
(hypot 3 4)       ; 5 (√(9+16))
(hypot in1 in2)   ; Vector magnitude
```

Codebox:
```c
hypot(3, 4)       // 5
hypot(x1, x2)     // Vector magnitude
```

---

## Performance

| Function | Speed | Notes |
|----------|-------|-------|
| `abs` | ~1 cycle | Very fast |
| `min`, `max` | ~1 cycle | Very fast |
| `clamp` | ~2 cycles | min/max operations |
| `wrap`, `fold` | ~5 cycles | Modulo-like |
| `scale` | ~10 cycles | Multiple operations |
| `cartopol`, `hypot` | ~20 cycles | Uses sqrt |
| `poltocar` | ~20 cycles | Uses sin/cos |

---

## Related Topics

- [Trigonometry](./trigonometry.md) - sin, cos, atan2
- [Powers](./powers.md) - sqrt, pow
- [Comparison Operators](./comparison.md) - For min/max logic
