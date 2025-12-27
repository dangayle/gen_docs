# Rounding and Numeric Conversion

Operations for converting and rounding numeric values.

## Floor and Ceiling

### Floor `floor`

**Syntax:**
```scheme
; GenExpr
(floor x)

; Codebox
floor(x)
```

**Description:**
Returns the largest integer less than or equal to x (rounds down).

**Parameters:**
- `x` - Input value

**Returns:**
- Floor of x (integer type)

**Examples:**

GenExpr:
```scheme
(floor 3.7)       ; 3
(floor 3.2)       ; 3
(floor -2.3)      ; -3 (rounds down, toward negative)
```

Codebox:
```c
floor(3.7)        // 3
floor(3.2)        // 3
floor(-2.3)       // -3
```

**Notes:**
- Always rounds toward negative infinity
- floor(-2.3) = -3, not -2

---

### Ceil `ceil`

**Syntax:**
```scheme
; GenExpr
(ceil x)

; Codebox
ceil(x)
```

**Description:**
Returns the smallest integer greater than or equal to x (rounds up).

**Parameters:**
- `x` - Input value

**Returns:**
- Ceiling of x (integer type)

**Examples:**

GenExpr:
```scheme
(ceil 3.2)        ; 4
(ceil 3.7)        ; 4
(ceil -2.3)       ; -2 (rounds up, toward positive)
```

Codebox:
```c
ceil(3.2)         // 4
ceil(3.7)         // 4
ceil(-2.3)        // -2
```

**Notes:**
- Always rounds toward positive infinity
- ceil(-2.3) = -2, not -3

---

## Rounding

### Round to Nearest `round`

**Syntax:**
```scheme
; GenExpr
(round x)

; Codebox
round(x)
```

**Description:**
Rounds to nearest integer. Halfway values (.5) round away from zero.

**Parameters:**
- `x` - Input value

**Returns:**
- Nearest integer

**Examples:**

GenExpr:
```scheme
(round 3.4)       ; 3
(round 3.5)       ; 4 (away from zero)
(round 3.6)       ; 4
(round -2.5)      ; -3 (away from zero)
```

Codebox:
```c
round(3.4)        // 3
round(3.5)        // 4
round(-2.5)       // -3
```

**Notes:**
- 0.5 rounds away from zero (3.5 → 4, -2.5 → -3)
- Standard rounding behavior

---

### Truncate `trunc`

**Syntax:**
```scheme
; GenExpr
(trunc x)

; Codebox
trunc(x)
```

**Description:**
Truncates to integer by removing fractional part (rounds toward zero).

**Parameters:**
- `x` - Input value

**Returns:**
- Truncated integer

**Examples:**

GenExpr:
```scheme
(trunc 3.7)       ; 3 (toward zero)
(trunc -2.7)      ; -2 (toward zero, not -3)
```

Codebox:
```c
trunc(3.7)        // 3
trunc(-2.7)       // -2
```

**Notes:**
- Different from floor for negative numbers
- floor(-2.7) = -3, trunc(-2.7) = -2

---

## Fractional Part

### Fraction `fract`

**Syntax:**
```scheme
; GenExpr
(fract x)

; Codebox
fract(x)
```

**Description:**
Returns the fractional part of x (x - floor(x)). Always returns 0 to 1.

**Parameters:**
- `x` - Input value

**Returns:**
- Fractional part (range [0, 1))

**Examples:**

GenExpr:
```scheme
(fract 3.7)       ; 0.7
(fract 3.2)       ; 0.2
(fract -2.3)      ; 0.7 (not -0.3)
```

Codebox:
```c
fract(3.7)        // 0.7
fract(3.2)        // 0.2
fract(-2.3)       // 0.7
```

**Notes:**
- Equivalent to x - floor(x)
- For negative numbers: fract(-2.3) = -2.3 - floor(-2.3) = -2.3 - (-3) = 0.7

---

## Type Conversion Functions

### Integer Conversion `int`

**Syntax:**
```scheme
; GenExpr
(int x)
(int-bits x)      ; Alternative syntax

; Codebox
int(x)
```

**Description:**
Converts to integer type. Truncates fractional part (toward zero).

**Parameters:**
- `x` - Input value

**Returns:**
- Integer value

**Examples:**

GenExpr:
```scheme
(int 3.7)         ; 3
(int 3.2)         ; 3
(int -2.7)        ; -2
```

Codebox:
```c
int(3.7)          // 3
int(-2.7)         // -2
```

**Notes:**
- Same behavior as trunc
- Explicit type conversion (usually automatic)

---

### Float Conversion `float`

**Syntax:**
```scheme
; GenExpr
(float x)
(float-bits x)    ; Alternative syntax

; Codebox
float(x)
```

**Description:**
Converts to float type explicitly. Allows mathematical operations on integers.

**Parameters:**
- `x` - Input value (int or float)

**Returns:**
- Float value

**Examples:**

GenExpr:
```scheme
(float 3)         ; 3.0
(/ (float 1) 2)   ; 0.5 (not 0 with int division)
```

Codebox:
```c
float(3)          // 3.0
float(1) / 2      // 0.5
```

**Notes:**
- Forces float division: (/ 1 2) = 0, (/ (float 1) 2) = 0.5
- Useful for ensuring precision

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
```scheme
; GenExpr
; Quantize to nearest grid step
quantized = (* (round (/ x step)) step)

; Codebox
quantized = round(x / step) * step;
```

### Sample-Rate-Safe Integer Division
```scheme
; GenExpr
; Avoid integer division pitfall
result = (/ (float numerator) denominator)

; Codebox
result = float(numerator) / denominator;
```

### Frequency Quantization
```scheme
; GenExpr
; Snap frequency to nearest semitone
midi-note = (round (+ 69 (* 12 (log2 (/ freq 440)))))
quantized-freq = (* 440 (exp2 (/ (- midi-note 69) 12)))

; Codebox
midi_note = round(69 + 12 * log2(freq / 440));
quantized_freq = 440 * exp2((midi_note - 69) / 12.0);
```

### Get Whole and Fractional Parts
```scheme
; GenExpr
whole = (trunc x)
fract-part = (fract x)

; Codebox
whole = trunc(x);
fract_part = fract(x);
```

### Safe Integer Arithmetic
```scheme
; GenExpr
; Ensure float result
percentage = (* 100 (/ (float count) total))

; Codebox
percentage = 100 * float(count) / total;
```

### Bit Depth Reduction (Bitcrushing)
```scheme
; GenExpr
bit-depth = 8
levels = (pow 2 bit-depth)
crushed = (/ (float (floor (* in levels))) levels)

; Codebox
bit_depth = 8;
levels = pow(2, bit_depth);
crushed = floor(in * levels) / levels;
```

---

## Performance

| Function | Speed | Notes |
|----------|-------|-------|
| `floor`, `ceil`, `round`, `trunc` | ~5 cycles | Fast integer operations |
| `fract` | ~5 cycles | x - floor(x) |
| `int`, `float` | ~1 cycle | Type conversion (often free) |

---

## Related Topics

- [Arithmetic](./arithmetic.md) - Division and modulo
- [Math Functions](./math.md) - clamp, scale, abs
- [Type System](../concepts/types.md) - Type inference and conversion
