# Bitwise Operators

Bit-level operations on integer values. These operators work at the binary representation level.

## Bitwise AND `&`

**Syntax:**
```scheme
; GenExpr
(& a b)

; Codebox
a & b
```

**Description:**
Returns the bitwise AND of two values. Each bit is 1 only if both input bits are 1.

**Parameters:**
- `a` - First value (int)
- `b` - Second value (int)

**Returns:**
- Bitwise AND result
- Type: int

**Examples:**

GenExpr:
```scheme
(& 12 10)         ; Binary: 1100 & 1010 = 1000 = 8
(& 0xFF 0x0F)     ; Hexadecimal: 255 & 15 = 15
(& value mask)    ; Mask specific bits
```

Codebox:
```c
12 & 10           // Binary: 1100 & 1010 = 1000 = 8
0xFF & 0x0F       // Hexadecimal: 255 & 15 = 15
value & mask      // Mask specific bits
```

**Truth Table (per bit):**
| A | B | A & B |
|---|---|-------|
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

**Common Use Cases:**
- Extracting specific bits: `value & 0x0F` (get lowest 4 bits)
- Testing if bits are set: `(& value mask) != 0`
- Clearing bits: `& value ~mask`

---

## Bitwise OR `|`

**Syntax:**
```scheme
; GenExpr
(| a b)

; Codebox
a | b
```

**Description:**
Returns the bitwise OR of two values. Each bit is 1 if either input bit is 1.

**Parameters:**
- `a` - First value (int)
- `b` - Second value (int)

**Returns:**
- Bitwise OR result
- Type: int

**Examples:**

GenExpr:
```scheme
(| 12 10)         ; Binary: 1100 | 1010 = 1110 = 14
(| 0x0F 0xF0)     ; Combine bit ranges
(| flags bit)     ; Set a bit
```

Codebox:
```c
12 | 10           // Binary: 1100 | 1010 = 1110 = 14
0x0F | 0xF0       // Combine: 255
flags | bit       // Set a bit
```

**Truth Table (per bit):**
| A | B | A \| B |
|---|---|--------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 1 |

**Common Use Cases:**
- Setting bits: `value | mask`
- Combining flags: `flag1 | flag2 | flag3`

---

## Bitwise XOR `^`

**Syntax:**
```scheme
; GenExpr
(^ a b)

; Codebox
a ^ b
```

**Description:**
Returns the bitwise XOR (exclusive OR) of two values. Each bit is 1 if input bits differ.

**Parameters:**
- `a` - First value (int)
- `b` - Second value (int)

**Returns:**
- Bitwise XOR result
- Type: int

**Examples:**

GenExpr:
```scheme
(^ 12 10)         ; Binary: 1100 ^ 1010 = 0110 = 6
(^ value 0xFF)    ; Flip all bits (for 8-bit values)
(^ state toggle)  ; Toggle bits
```

Codebox:
```c
12 ^ 10           // Binary: 1100 ^ 1010 = 0110 = 6
value ^ 0xFF      // Flip all bits (for 8-bit)
state ^ toggle    // Toggle bits
```

**Truth Table (per bit):**
| A | B | A ^ B |
|---|---|-------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

**Common Use Cases:**
- Toggling bits: `value ^ mask`
- Checking if bits differ: `(^ a b) != 0`

---

## Bitwise NOT `~`

**Syntax:**
```scheme
; GenExpr
(~ a)

; Codebox
~a
```

**Description:**
Returns the bitwise NOT (complement) of a value. Flips all bits: 0 becomes 1, 1 becomes 0.

**Parameters:**
- `a` - Input value (int)

**Returns:**
- Bitwise NOT result
- Type: int

**Examples:**

GenExpr:
```scheme
(~ 0)             ; -1 (all bits set)
(~ 0xFF)          ; -256 (flip 8-bit pattern)
(~ mask)          ; Invert mask
```

Codebox:
```c
~0                // -1 (all bits set)
~0xFF             // -256
~mask             // Invert mask
```

**Notes:**
- In two's complement representation, `~x = -x - 1`
- `(~ 0) = -1` (all bits set)
- `(~ -1) = 0`

**Common Use Cases:**
- Inverting masks: `value & ~mask` (clear specific bits)
- Creating all-ones: `~ 0`

---

## Left Shift `<<`

**Syntax:**
```scheme
; GenExpr
(<< a b)

; Codebox
a << b
```

**Description:**
Left shifts the bits of the first value by the number of positions in the second value. Equivalent to multiplying by 2^b.

**Parameters:**
- `a` - Value to shift (int)
- `b` - Number of positions to shift (int)

**Returns:**
- Shifted value
- Type: int

**Examples:**

GenExpr:
```scheme
(<< 5 1)          ; Binary: 0101 << 1 = 1010 = 10 (5 * 2)
(<< 1 4)          ; Binary: 0001 << 4 = 10000 = 16 (1 * 2^4)
(<< value 8)      ; Multiply by 256
```

Codebox:
```c
5 << 1            // Binary: 0101 << 1 = 1010 = 10
1 << 4            // Binary: 0001 << 4 = 10000 = 16
value << 8        // Multiply by 256
```

**Performance:**
- Much faster than multiplication: `a << b` vs `a * pow(2, b)`
- Preferred for powers of 2: `a << 3` instead of `a * 8`

---

## Right Shift `>>` (Arithmetic)

**Syntax:**
```scheme
; GenExpr
(>> a b)

; Codebox
a >> b
```

**Description:**
Right shifts the bits of the first value by the number of positions in the second value. Sign-extends (arithmetic shift): copies the sign bit. Equivalent to integer division by 2^b.

**Parameters:**
- `a` - Value to shift (int)
- `b` - Number of positions to shift (int)

**Returns:**
- Shifted value
- Type: int

**Examples:**

GenExpr:
```scheme
(>> 10 1)         ; Binary: 1010 >> 1 = 0101 = 5 (10 / 2)
(>> 16 4)         ; Binary: 10000 >> 4 = 0001 = 1 (16 / 16)
(>> value 8)      ; Divide by 256 (with sign extension)
(>> -10 1)        ; -10 / 2 = -5 (sign-extends)
```

Codebox:
```c
10 >> 1           // Binary: 1010 >> 1 = 0101 = 5
16 >> 4           // Binary: 10000 >> 4 = 0001 = 1
value >> 8        // Divide by 256
-10 >> 1          // -5 (sign-extends)
```

**Notes:**
- Sign-extends: negative numbers stay negative
- Preferred for dividing by powers of 2: `a >> 3` instead of `a / 8`

---

## Right Shift `>>>` (Logical)

**Syntax:**
```scheme
; GenExpr
(>>> a b)

; Codebox
a >>> b
```

**Description:**
Logical right shift: shifts bits right and fills with zeros (no sign extension). Always treats input as unsigned.

**Parameters:**
- `a` - Value to shift (int)
- `b` - Number of positions to shift (int)

**Returns:**
- Shifted value (zero-filled)
- Type: int

**Examples:**

GenExpr:
```scheme
(>>> 10 1)        ; Binary: 1010 >>> 1 = 0101 = 5
(>>> -10 1)       ; Treats -10 as unsigned, fills with 0
```

Codebox:
```c
10 >>> 1          // Binary: 1010 >>> 1 = 0101 = 5
-10 >>> 1         // Logical shift (zero-fill)
```

**Notes:**
- Zero-fills (no sign extension)
- Different from `>>` for negative numbers
- Less commonly used than arithmetic shift

---

## Common Bit Manipulation Patterns

### Check if Bit is Set
```scheme
; GenExpr
(if (& value mask) ...)

; Codebox
if (value & mask) { }
```

### Set a Bit
```scheme
; GenExpr
(| value bit_mask)

; Codebox
value | bit_mask
```

### Clear a Bit
```scheme
; GenExpr
(& value (~ bit_mask))

; Codebox
value & ~bit_mask
```

### Toggle a Bit
```scheme
; GenExpr
(^ value bit_mask)

; Codebox
value ^ bit_mask
```

### Extract Bits (Mask)
```scheme
; GenExpr
(& value 0x0F)    ; Get lowest 4 bits

; Codebox
value & 0x0F      // Get lowest 4 bits
```

### Multiply by Power of 2
```scheme
; GenExpr
(<< value 3)      ; Multiply by 8 (2^3)

; Codebox
value << 3        // Multiply by 8
```

### Divide by Power of 2
```scheme
; GenExpr
(>> value 3)      ; Divide by 8

; Codebox
value >> 3        // Divide by 8
```

---

## Performance

All bitwise operators are extremely fast (~1 cycle):
- `&`, `|`, `^`, `~`: Fast
- `<<`, `>>`, `>>>`: Fast (especially preferred over multiplication/division for powers of 2)

---

## Hexadecimal Notation

Hex notation useful for bitwise operations:

```scheme
0xFF              ; 255 (8 bits all set)
0x0F              ; 15 (lower 4 bits)
0xF0              ; 240 (upper 4 bits)
0x1000            ; 4096 (single high bit)
```

---

## Difference: Logical vs Bitwise

| Operation | Logical | Bitwise | Example |
|-----------|---------|---------|---------|
| AND | `&&` | `&` | `(1 && 2)` = 1, `(1 & 2)` = 0 |
| OR | `\|\|` | `\|` | `(1 \|\| 0)` = 1, `(1 \| 0)` = 1 |
| XOR | `^^` | `^` | `(1 ^^ 2)` = 1, `(1 ^ 2)` = 3 |
| NOT | `!` | `~` | `(!1)` = 0, `(~1)` = -2 |

---

## Related Topics

- [Logic Operators](./logic.md) - Logical &&, \|\|, !
- [Arithmetic Operators](./arithmetic.md) - +, -, *, /
- [Comparison Operators](./comparison.md) - ==, <, >
