# Rounding and Numeric Conversion

Operations for converting and rounding numeric values.

## Floor and Ceiling

### Floor `floor`

**Syntax (GenExpr & Codebox):**
```c
floor(x)
```

**Description:**
Returns the largest integer less than or equal to x (rounds down).

**Examples:**
```c
floor(3.7);        // 3
floor(3.2);        // 3
floor(-2.3);       // -3 (toward negative)
```

**Notes:**
- Always rounds toward negative infinity
- `floor(-2.3) = -3`, not -2

---

### Ceil `ceil`

**Syntax (GenExpr & Codebox):**
```c
ceil(x)
```

**Description:**
Returns the smallest integer greater than or equal to x (rounds up).

**Examples:**
```c
ceil(3.2);         // 4
ceil(3.7);         // 4
ceil(-2.3);        // -2 (toward positive)
```

**Notes:**
- Always rounds toward positive infinity
- `ceil(-2.3) = -2`, not -3

---

## Rounding

### Round to Nearest `round`

**Syntax (GenExpr & Codebox):**
```c
round(x)
```

**Description:**
Rounds to nearest integer. Halfway values (.5) round away from zero.

**Examples:**
```c
round(3.4);        // 3
round(3.5);        // 4 (away from zero)
round(3.6);        // 4
round(-2.5);       // -3 (away from zero)
```

**Notes:**
- 0.5 rounds away from zero (3.5 → 4, -2.5 → -3)
- Standard rounding behavior

---

### Truncate `trunc`

**Syntax (GenExpr & Codebox):**
```c
trunc(x)
```

**Description:**
Truncates to integer by removing fractional part (rounds toward zero).

**Examples:**
```c
trunc(3.7);        // 3
trunc(-2.7);       // -2 (toward zero)
```

**Notes:**
- Different from floor for negative numbers
- `floor(-2.7) = -3`, `trunc(-2.7) = -2`

---

## Fractional Part

### Fraction `fract`

**Syntax (GenExpr & Codebox):**
```c
fract(x)
```

**Description:**
Returns the fractional part of x (`x - floor(x)`). Always returns 0 to 1.

**Examples:**
```c
fract(3.7);        // 0.7
fract(3.2);        // 0.2
fract(-2.3);       // 0.7 (not -0.3)
```

**Notes:**
- Equivalent to `x - floor(x)`
- For negative numbers: `fract(-2.3) = -2.3 - floor(-2.3) = 0.7`

---

## Type Conversion Functions

### Integer Conversion `int`

**Syntax (GenExpr & Codebox):**
```c
int(x)
```

**Description:**
Converts to integer type. Truncates fractional part (toward zero).

**Examples:**
```c
int(3.7);          // 3
int(3.2);          // 3
int(-2.7);         // -2
```

**Notes:**
- Same behavior as `trunc`
- Explicit type conversion (usually automatic)

---

### Float Conversion `float`

**Syntax (GenExpr & Codebox):**
```c
float(x)
```

**Description:**
Converts to float type explicitly. Useful to avoid integer division and enforce precision.

**Examples:**
```c
float(3);          // 3.0
float(1) / 2;      // 0.5 (avoid integer division)
```

**Notes:**
- Forces float division: `float(1) / 2 = 0.5` vs `1 / 2 = 0`

---

## Comparison of Rounding Methods

| Function | 3.7 | -2.3 | Use Case |
|----------|-----|------|----------|
| `floor` | 3 | -3 | Round down (always) |
| `ceil` | 4 | -2 | Round up (always) |
| `round` | 4 | -2 | Nearest integer |
| `trunc` | 3 | -2 | Toward zero |
| `int` | 3 | -2 | Explicit int cast |
| `fract` | 0.7 | 0.7 | Get fractional part |

---

## Common Patterns

### Quantize to Grid
```c
quantized = round(x / step) * step;   // nearest grid step
```

### Sample-Rate-Safe Integer Division
```c
result = float(numerator) / denominator;   // avoid int division truncation
```

### Frequency Quantization
```c
midi_note = round(69 + 12 * log2(freq / 440));
quantized_freq = 440 * exp2((midi_note - 69) / 12.0);
```

### Get Whole and Fractional Parts
```c
whole = trunc(x);
fract_part = fract(x);
```

### Safe Integer Arithmetic
```c
percentage = 100 * float(count) / total;   // force float math
```

### Bit Depth Reduction (Bitcrushing)
```c
bit_depth = 8;
levels = pow(2, bit_depth);
crushed = floor(in * levels) / levels;
```

---

## Performance

| Function | Speed | Notes |
|----------|-------|-------|
| `floor`, `ceil`, `round`, `trunc` | ~5 cycles | Fast integer operations |
| `fract` | ~5 cycles | `x - floor(x)` |
| `int`, `float` | ~1 cycle | Type conversion (often free) |

---

## Related Topics

- [Arithmetic](./arithmetic.md) - Division and modulo
- [Math Functions](./math.md) - clamp, scale, abs
- [Type System](../concepts/types.md) - Type inference and conversion
