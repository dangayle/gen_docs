# Bitwise Operators

Bit-level operations on integer values.

## Bitwise AND `&`

**Syntax (GenExpr & Codebox):**
```c
a & b
```

**Description:**
Bitwise AND of two ints; each bit is 1 only if both inputs are 1.

**Examples:**
```c
12 & 10;           // 1100 & 1010 = 1000 = 8
0xFF & 0x0F;       // 255 & 15 = 15
value & mask;      // mask specific bits
```

**Truth Table (per bit):**
| A | B | A & B |
|---|---|-------|
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

**Common Use Cases:**
- Extract bits: `value & 0x0F`
- Test bits: `(value & mask) != 0`
- Clear bits: `value & ~mask`

---

## Bitwise OR `|`

**Syntax (GenExpr & Codebox):**
```c
a | b
```

**Description:**
Bitwise OR; each bit is 1 if either input bit is 1.

**Examples:**
```c
12 | 10;           // 1100 | 1010 = 1110 = 14
0x0F | 0xF0;       // combine bit ranges
flags | bit;       // set a bit
```

**Common Use Cases:**
- Set bits: `value | mask`
- Combine flags: `flag1 | flag2 | flag3`

---

## Bitwise XOR `^`

**Syntax (GenExpr & Codebox):**
```c
a ^ b
```

**Description:**
Bitwise exclusive OR; bit is 1 if input bits differ.

**Examples:**
```c
12 ^ 10;           // 1100 ^ 1010 = 0110 = 6
value ^ 0xFF;      // flip all bits (8-bit)
state ^ toggle;    // toggle bits
```

**Common Use Cases:**
- Toggle bits: `value ^ mask`
- Check difference: `(a ^ b) != 0`

---

## Bitwise NOT `~`

**Syntax (GenExpr & Codebox):**
```c
~a
```

**Description:**
Bitwise complement; flips all bits.

**Examples:**
```c
~0;                // -1 (all bits set)
~0xFF;             // -256 (flip 8-bit pattern)
~mask;             // invert mask
```

**Notes:**
- Two's complement: `~x = -x - 1`
- `~0` produces all 1 bits

---

## Left Shift `<<`

**Syntax (GenExpr & Codebox):**
```c
a << b
```

**Description:**
Shifts bits left by `b`; equivalent to multiplying by `2^b`.

**Examples:**
```c
5 << 1;            // 0101 << 1 = 1010 = 10
1 << 4;            // 0001 << 4 = 10000 = 16
value << 8;        // multiply by 256
```

**Notes:**
- Faster than `a * pow(2, b)` for powers of two

---

## Right Shift `>>` (Arithmetic)

**Syntax (GenExpr & Codebox):**
```c
a >> b
```

**Description:**
Arithmetic right shift with sign extension; like integer divide by `2^b`.

**Examples:**
```c
10 >> 1;           // 1010 >> 1 = 0101 = 5
16 >> 4;           // 10000 >> 4 = 0001 = 1
value >> 8;        // divide by 256
-10 >> 1;          // -5 (sign-extends)
```

**Notes:**
- Sign bit is copied; negatives stay negative

---

## Right Shift `>>>` (Logical)

**Syntax (GenExpr & Codebox):**
```c
a >>> b
```

**Description:**
Logical right shift; fills with zeros (no sign extension). Treats input as unsigned.

**Examples:**
```c
10 >>> 1;          // 0101 = 5
-10 >>> 1;         // zero-filled logical shift
```

**Notes:**
- Differs from `>>` for negative numbers

---

## Common Bit Manipulation Patterns

```c
// Check if bit is set
if (value & mask) { /* ... */ }

// Set a bit
value = value | bit_mask;

// Clear a bit
value = value & ~bit_mask;

// Toggle a bit
value = value ^ bit_mask;

// Extract bits
low_nibble = value & 0x0F;

// Multiply or divide by power of 2
value <<= 3;   // multiply by 8
value >>= 3;   // divide by 8 (sign-extend)
```

---

## Performance

All bitwise operators are extremely fast (~1 cycle):
- `&`, `|`, `^`, `~`
- `<<`, `>>`, `>>>`

---

## Hexadecimal Notation

Hex is convenient for masks and flags:
```c
0xFF;     // 255 (8 bits all set)
0x0F;     // 15 (lower 4 bits)
0xF0;     // 240 (upper 4 bits)
0x1000;   // 4096 (single high bit)
```

---

## Logical vs Bitwise

| Operation | Logical | Bitwise | Example |
|-----------|---------|---------|---------|
| AND | `&&` | `&` | `(1 && 2) = 1`, `(1 & 2) = 0` |
| OR | `||` | `|` | `(1 || 0) = 1`, `(1 | 0) = 1` |
| XOR | `^^` | `^` | `(1 ^^ 2) = 1`, `(1 ^ 2) = 3` |
| NOT | `!` | `~` | `(!1) = 0`, `(~1) = -2` |

---

## Related Topics

- [Logic Operators](./logic.md) - Logical &&, ||, !
- [Arithmetic Operators](./arithmetic.md) - +, -, *, /
- [Comparison Operators](./comparison.md) - ==, <, >
