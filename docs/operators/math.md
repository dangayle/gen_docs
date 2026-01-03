# Math Functions

Common mathematical operations and transformations.

## Absolute Value `abs`

**Syntax (GenExpr & Codebox):**
```c
abs(x)
```

**Description:**
Returns the absolute value (distance from zero).

**Examples:**
```c
abs(5);           // 5
abs(-5);          // 5
abs(in);          // absolute value of input
```

---

## Minimum `min`

**Syntax (GenExpr & Codebox):**
```c
min(a, b)
min(a, b, c, d, ...)
```

**Description:**
Returns the smallest input value.

**Examples:**
```c
min(5, 3);                // 3
min(10, 5, 20, 1);        // 1
min(in, threshold);       // lower of input or threshold
```

---

## Maximum `max`

**Syntax (GenExpr & Codebox):**
```c
max(a, b)
max(a, b, c, d, ...)
```

**Description:**
Returns the largest input value.

**Examples:**
```c
max(5, 3);                // 5
max(10, 5, 20, 1);        // 20
max(in, threshold);       // higher of input or threshold
```

---

## Clamp (Clip) `clamp` / `clip`

**Syntax (GenExpr & Codebox):**
```c
clamp(x, min, max)
clip(x, min, max)
```

**Description:**
Constrains a value to `[min, max]`.

**Examples:**
```c
clamp(5, 0, 10);    // 5
clamp(-5, 0, 10);   // 0
clamp(15, 0, 10);   // 10
clamp(in, 0, 1);    // constrain to [0, 1]
```

---

## Wrap

**Syntax (GenExpr & Codebox):**
```c
wrap(x, min, max)
```

**Description:**
Wraps a value within a range (always positive wrap).

**Examples:**
```c
wrap(-0.5, 0, 1);          // 0.5
wrap(1.5, 0, 1);           // 0.5
wrap(phase, 0, twopi);     // wrap phase to [0, 2π)
```

---

## Fold

**Syntax (GenExpr & Codebox):**
```c
fold(x, min, max)
```

**Description:**
Folds a value by reflecting at boundaries.

**Examples:**
```c
fold(1.5, 0, 1);    // 0.5 (reflects at 1)
fold(2.5, 0, 1);    // 0.5 (double reflection)
```

---

## Negate `neg`

**Syntax (GenExpr & Codebox):**
```c
neg(x)
-x
```

**Description:**
Negates a value (multiplies by -1).

**Examples:**
```c
neg(5);      // -5
- in;        // negate input
```

---

## Sign `sign`

**Syntax (GenExpr & Codebox):**
```c
sign(x)
```

**Description:**
Returns -1 for negative, 0 for zero, 1 for positive.

**Examples:**
```c
sign(5);           // 1
sign(-5);          // -1
sign(0);           // 0
```

---

## Scale

**Syntax (GenExpr & Codebox):**
```c
scale(x, in_min, in_max, out_min, out_max)
scale(x, in_min, in_max, out_min, out_max, curve)
```

**Description:**
Maps a value from one range to another with optional exponential curve (`curve` default 1.0 linear).

**Examples:**
```c
scale(in, 0, 127, 0, 1);        // MIDI to 0-1
scale(in, 0, 1, 20, 20000);     // normalized to frequency
scale(in, 0, 1, 0, 1, 2);       // exponential curve
```

**Notes:**
- No automatic clamping; values outside input range extrapolate
- Curve > 1 expands; curve < 1 compresses

---

## Absolute Difference `absdiff`

**Syntax (GenExpr & Codebox):**
```c
absdiff(a, b)
```

**Description:**
Absolute difference `|a - b|`.

**Examples:**
```c
absdiff(5, 3);      // 2
absdiff(-5, 3);     // 8
absdiff(in1, in2);  // distance between inputs
```

---

## Cartesian/Polar Helpers

### Cartesian to Polar `cartopol`
```c
cartopol(x, y, &r, &theta);
```

### Polar to Cartesian `poltocar`
```c
poltocar(r, theta, &x, &y);
```

### Hypotenuse `hypot`
```c
hypot(x, y);    // sqrt(x^2 + y^2)
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
| `cartopol`, `hypot` | ~20 cycles | Uses sqrt/atan2 |
| `poltocar` | ~20 cycles | Uses sin/cos |

---

## Related Topics

- [Trigonometry](./trigonometry.md) - sin, cos, atan2
- [Powers](./powers.md) - sqrt, pow
- [Comparison Operators](./comparison.md) - For min/max logic
