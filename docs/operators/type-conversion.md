# Type Conversion Operators

Explicit conversion between numeric types.

## Integer Conversion `int`

**Syntax (GenExpr & Codebox):**
```c
int(x)
```

**Description:**
Converts to integer by truncating the fractional part (toward zero).

**Examples:**
```c
int(3.7);          // 3
int(-2.3);         // -2
int(5 / 2);        // 2 (integer division result)
```

**Notes:**
- Truncates toward zero (different from floor)
- Explicit `int()` forces conversion when precision matters

---

## Float Conversion `float`

**Syntax (GenExpr & Codebox):**
```c
float(x)
```

**Description:**
Converts to float explicitly to preserve precision in arithmetic.

**Examples:**
```c
float(3);          // 3.0
float(1) / 2;      // 0.5 (avoid int division)
float(velocity) / 127.0;   // normalize MIDI velocity
```

**Notes:**
- Critical for division: `float(1) / 2 = 0.5` vs `1 / 2 = 0`

---

## Boolean Conversion `bool`

**Syntax (GenExpr & Codebox):**
```c
bool(x)
```

**Description:**
Converts to boolean (0 or 1). Non-zero becomes 1; zero stays 0.

**Examples:**
```c
bool(0);           // 0
bool(3);           // 1
bool(-1);          // 1 (non-zero)
```

---

## Raw Bit Reinterpretation

**Syntax (GenExpr & Codebox):**
```c
int_bits(x);    // interpret float bits as int
float_bits(x);  // interpret int bits as float
```

**Description:**
Reinterprets underlying bits without numeric conversion. Advanced/rare.

---

## Common Conversion Patterns

### Safe Division (Ensure Float Result)
```c
int bad = 1 / 2;                 // 0 (integer division)
float ok = float(1) / 2;         // 0.5
float ok2 = 1.0 / 2;             // 0.5 (float literal)
```

### Integer Array Index
```c
index = int(scale(param, 0, 1, 0, table_size));
table_value = lookup(table, index);
```

### Boolean Gate
```c
gate = bool(input > threshold);
output = signal * gate;
```

### Type Promotion for Precision
```c
result = float(count) / total;
percentage = 100 * result;
```

### Sample-to-Second Conversion
```c
seconds = float(sample_count) / samplerate;
```

### Time-Based Fade
```c
time_sec = float(sample_index) / samplerate;
fade = min(1, time_sec * fade_rate);
```

### Bitwise Operations (Require Integer)
```c
int value = int(input);
value = value & 0xFF;   // mask to 8 bits
```

---

## Type Inference (Automatic Promotion)

| Operation | Input Types | Output Type |
|-----------|-------------|-------------|
| `int + int` | int, int | int |
| `int + float` | int, float | float |
| `float + float` | float, float | float |
| `int * float` | int, float | float |
| `int / int` | int, int | int (integer division) |
| `any / float` | any, float | float |

**Examples:**
```c
1 + 2;         // int (3)
1.0 + 2;       // float (3.0)
1 / 2;         // int (0)
1.0 / 2;       // float (0.5)
```

---

## Performance

| Function | Speed | Notes |
|----------|-------|-------|
| `int()` | ~1 cycle | Type conversion |
| `float()` | ~1 cycle | Type conversion |
| `bool()` | ~1 cycle | Conversion to 0/1 |
| `int_bits` | ~1 cycle | Raw bit reinterpretation |
| `float_bits` | ~1 cycle | Raw bit reinterpretation |

---

## Related Topics

- [Concepts: Type System](../concepts/types.md) - Type inference details
- [Arithmetic](./arithmetic.md) - Division (int vs float)
