# Type Conversion Operators

Explicit conversion between numeric types.

## Integer Conversion

### Cast to Integer `int`

**Syntax:**
```scheme
; GenExpr
(int x)
(int-bits x)      ; Raw bit interpretation

; Codebox
int(x)
```

**Description:**
Converts a value to integer type by truncating the fractional part (rounding toward zero).

**Parameters:**
- `x` - Input value (float or int)

**Returns:**
- Integer value

**Examples:**

GenExpr:
```scheme
(int 3.7)         ; 3
(int -2.3)        ; -2 (toward zero)
(int (/ 5 2))     ; 2 (integer division result)
```

Codebox:
```c
int(3.7)          // 3
int(-2.3)         // -2
int(5 / 2)        // 2
```

**Notes:**
- Truncates toward zero (different from floor)
- Implicit conversion often happens automatically
- Explicit int() forces type conversion

---

## Float Conversion

### Cast to Float `float`

**Syntax:**
```scheme
; GenExpr
(float x)
(float-bits x)    ; Raw bit interpretation

; Codebox
float(x)
```

**Description:**
Converts a value to float type explicitly. Allows precision in arithmetic operations.

**Parameters:**
- `x` - Input value (int or float)

**Returns:**
- Float value

**Examples:**

GenExpr:
```scheme
(float 3)         ; 3.0
(/ (float 1) 2)   ; 0.5 (not 0 with int division)
(float (+ 1 2))   ; 3.0
```

Codebox:
```c
float(3)          // 3.0
float(1) / 2      // 0.5 (vs 0 with int division)
```

**Notes:**
- Critical for division: (float 1) / 2 = 0.5, but 1 / 2 = 0 (integer division)
- 32-bit float (same as signal type in Gen)
- Implicit conversion often happens, but explicit helps clarity

---

## Boolean Conversion

### Cast to Boolean `bool`

**Syntax:**
```scheme
; GenExpr
(bool x)

; Codebox
bool(x)
```

**Description:**
Converts a value to boolean (0 or 1). Non-zero becomes 1, zero stays 0.

**Parameters:**
- `x` - Input value

**Returns:**
- 0 or 1 (boolean)

**Examples:**

GenExpr:
```scheme
(bool 0)          ; 0
(bool 1)          ; 1
(bool 3)          ; 1 (any non-zero)
(bool -1)         ; 1 (negative is non-zero)
```

Codebox:
```c
bool(0)           // 0
bool(1)           // 1
bool(3)           // 1
```

**Notes:**
- Any non-zero value becomes 1
- Often implicit in conditional contexts
- Equivalent to (? x 1 0)

---

## Common Conversion Patterns

### Safe Division (Ensure Float Result)
```scheme
; GenExpr
; Integer division (unwanted)
(/ 1 2)           ; 0 (integer)

; Float division (correct)
(/ (float 1) 2)   ; 0.5 (float)
(/ 1.0 2)         ; 0.5 (literal float)

; Codebox
int result = 1 / 2;        // 0 (integer division)
float result = float(1) / 2; // 0.5 (float division)
float result = 1.0 / 2;    // 0.5 (float literal)
```

### Integer Array Index
```scheme
; GenExpr
; Must be integer for table access
index = (int (scale param 0 1 0 table-size))
table-value = (lookup table index)

; Codebox
index = int(scale(param, 0, 1, 0, table_size));
table_value = lookup(table, index);
```

### Boolean Gate
```scheme
; GenExpr
; Convert comparison to 0/1 gate
gate = (bool (> input threshold))
output = (* signal gate)

; Codebox
gate = bool(input > threshold);
output = signal * gate;
```

### Type Promotion for Precision
```scheme
; GenExpr
; Ensure float arithmetic
result = (/ (float count) total)
percentage = (* 100 result)

; Codebox
result = float(count) / total;
percentage = 100 * result;
```

### MIDI Velocity Normalization
```scheme
; GenExpr
; Convert MIDI integer to normalized float
normalized = (/ (float velocity) 127)

; Codebox
normalized = float(velocity) / 127.0;
```

### Sample-to-Second Conversion
```scheme
; GenExpr
; Convert sample count to seconds
seconds = (/ (float sample-count) samplerate)

; Codebox
seconds = float(sample_count) / samplerate;
```

### Time-Based Fade
```scheme
; GenExpr
; Integer sample count, float seconds
time-sec = (/ (float sample-index) samplerate)
fade = (min 1 (* time-sec fade-rate))

; Codebox
time_sec = float(sample_index) / samplerate;
fade = min(1, time_sec * fade_rate);
```

### Bitwise Operations (Require Integer)
```scheme
; GenExpr
; Convert to int for bit operations
int-value = (int input)
bit-masked = (& int-value 0xFF)

; Codebox
int value = int(input);
value = value & 0xFF;
```

---

## Type Inference and Automatic Conversion

Gen automatically infers types in most cases:

| Operation | Input Types | Output Type |
|-----------|-------------|-------------|
| int + int | int, int | int |
| int + float | int, float | float |
| float + float | float, float | float |
| int * float | int, float | float |
| / (division) | int, int | int (integer division) |
| / (division) | any float | float |

**Example:**

GenExpr:
```scheme
(+ 1 2)           ; int + int = int (3)
(+ 1.0 2)         ; float + int = float (3.0)
(/ 1 2)           ; int / int = int (0)
(/ 1.0 2)         ; float / int = float (0.5)
```

---

## Integer Bit Interpretation

### Raw Bit Cast (Advanced)

**Syntax:**
```scheme
; GenExpr
(int-bits x)      ; Interpret float bits as integer
(float-bits x)    ; Interpret integer bits as float
```

**Description:**
Raw reinterpretation of bits without numeric conversion. Advanced use only.

**Parameters:**
- `x` - Value to reinterpret

**Returns:**
- Bits reinterpreted as different type

**Examples:**

GenExpr:
```scheme
; Get IEEE 754 representation
bits = (int-bits 1.0)  ; 1065353216 (0x3F800000)

; Recover float from bits
value = (float-bits bits)
```

**Notes:**
- Rarely needed in audio processing
- Used for low-level bit manipulation
- Preserves bit pattern, not numeric value

---

## Performance

| Function | Speed | Notes |
|----------|-------|-------|
| `int()` | ~1 cycle | Type conversion |
| `float()` | ~1 cycle | Type conversion |
| `bool()` | ~1 cycle | Conversion to 0/1 |
| `int-bits` | ~1 cycle | Raw bit reinterpretation |
| `float-bits` | ~1 cycle | Raw bit reinterpretation |

---

## Related Topics

- [Concepts: Type System](../concepts/types.md) - Type inference details
- [Arithmetic](./arithmetic.md) - Division (int vs float)
- [Rounding](./rounding.md) - floor, ceil, trunc, fract
