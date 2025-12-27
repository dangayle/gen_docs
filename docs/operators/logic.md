# Logic Operators

Logical operations for boolean reasoning (AND, OR, NOT, XOR).

## Logical NOT `!` or `not`

**Syntax:**
```scheme
; GenExpr
(! a)
(not a)

; Codebox
!a
not(a)
```

**Description:**
Returns 1 if input is zero (false), 0 if input is non-zero (true).

**Parameters:**
- `a` - Input value

**Returns:**
- 1 if a == 0, 0 otherwise
- Type: int (0 or 1)

**Examples:**

GenExpr:
```scheme
(! 0)             ; 1 (true)
(! 5)             ; 0 (false)
(! (== in 0))     ; NOT equal to zero
```

Codebox:
```c
!0                // 1 (true)
!5                // 0 (false)
!(x == 0)         // NOT equal to zero
```

---

## Logical AND `&&` or `and`

**Syntax:**
```scheme
; GenExpr
(&& a b)
(&& a b c ...)
(and a b)

; Codebox
a && b
a && b && c && ...
and(a, b)
```

**Description:**
Returns 1 if both inputs are non-zero, 0 otherwise.

**Parameters:**
- `a`, `b`, ... - Input values

**Returns:**
- 1 if all inputs are non-zero, 0 otherwise
- Type: int (0 or 1)

**Examples:**

GenExpr:
```scheme
(&& 5 3)          ; 1 (both non-zero)
(&& 5 0)          ; 0 (one is zero)
(&& (> x 0) (< x 1))  ; Both conditions true
```

Codebox:
```c
(5 && 3)          // 1 (both non-zero)
(5 && 0)          // 0 (one is zero)
(x > 0) && (x < 1) // Both conditions
```

**Notes:**
- Treats any non-zero value as true
- Works with any number of inputs

---

## Logical OR `||` or `or`

**Syntax:**
```scheme
; GenExpr
(|| a b)
(|| a b c ...)
(or a b)

; Codebox
a || b
a || b || c || ...
or(a, b)
```

**Description:**
Returns 1 if at least one input is non-zero, 0 if all are zero.

**Parameters:**
- `a`, `b`, ... - Input values

**Returns:**
- 1 if any input is non-zero, 0 if all are zero
- Type: int (0 or 1)

**Examples:**

GenExpr:
```scheme
(|| 0 3)          ; 1 (one is non-zero)
(|| 0 0)          ; 0 (all zero)
(|| (< x 0) (> x 1)) ; At least one true
```

Codebox:
```c
(0 || 3)          // 1 (one is non-zero)
(0 || 0)          // 0 (all zero)
(x < 0) || (x > 1) // At least one true
```

---

## Logical XOR `^^` or `xor`

**Syntax:**
```scheme
; GenExpr
(^^ a b)
(xor a b)

; Codebox
a ^^ b
xor(a, b)
```

**Description:**
Returns 1 if exactly one input is non-zero, 0 otherwise.

**Parameters:**
- `a` - First value
- `b` - Second value

**Returns:**
- 1 if one is non-zero and the other is zero, 0 otherwise
- Type: int (0 or 1)

**Examples:**

GenExpr:
```scheme
(^^ 5 0)          ; 1 (one is non-zero)
(^^ 5 3)          ; 0 (both non-zero)
(^^ 0 0)          ; 0 (both zero)
(^^ (> x 0) (> y 0)) ; Exactly one positive
```

Codebox:
```c
(5 ^^ 0)          // 1 (one is non-zero)
(5 ^^ 3)          // 0 (both non-zero)
(x > 0) ^^ (y > 0) // Exactly one positive
```

---

## Boolean Conversion `bool`

**Syntax:**
```scheme
; GenExpr
(bool a)

; Codebox
bool(a)
```

**Description:**
Converts any non-zero value to 1, keeps 0 as 0.

**Parameters:**
- `a` - Input value

**Returns:**
- 1 if a is non-zero, 0 if a is zero
- Type: int (0 or 1)

**Examples:**

GenExpr:
```scheme
(bool 5)          ; 1
(bool 0)          ; 0
(bool -0.001)     ; 1 (any non-zero)
```

Codebox:
```c
bool(5)           // 1
bool(0)           // 0
bool(-0.001)      // 1 (any non-zero)
```

---

## Truth Tables

### AND (`&&`)
| A | B | A && B |
|---|---|--------|
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

### OR (`||`)
| A | B | A \|\| B |
|---|---|----------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 1 |

### XOR (`^^`)
| A | B | A ^^ B |
|---|---|--------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

### NOT (`!`)
| A | !A |
|---|-----|
| 0 | 1 |
| 1 | 0 |

---

## Complex Logic Expressions

### De Morgan's Laws

**Law 1:** `!(a && b) = !a || !b`
```scheme
; These are equivalent:
(! (&& a b))
(|| (! a) (! b))
```

**Law 2:** `!(a || b) = !a && !b`
```scheme
; These are equivalent:
(! (|| a b))
(&& (! a) (! b))
```

### Short-Circuit Evaluation

Note: Gen evaluates all expressions, no short-circuiting occurs.

```scheme
; Both conditions are always evaluated
(&& (> x 0) (/ 1 x))  ; Does NOT skip division if x <= 0
```

---

## Common Patterns

### Toggle Logic
```scheme
; GenExpr
(^^ state trigger)  ; Toggle if trigger is true

; Codebox
state ^^ trigger
```

### Multi-Condition Gate
```scheme
; GenExpr: Output if multiple conditions true
(* in (&& (> x 0) (< x 1)))

; Codebox
y = x * ((x > 0) && (x < 1));
```

### Priority Logic
```scheme
; GenExpr: First condition takes priority
(|| condition_a condition_b)

; Codebox
y = condition_a || condition_b;
```

---

## Interaction with Comparison Operators

Comparisons return 1 or 0, perfect for logical operators:

```scheme
; GenExpr
(&& (> x 0) (< x 1))           ; x is between 0 and 1
(|| (== x 0) (== x 1))         ; x is 0 or 1
(! (== x 0))                   ; x is not zero
```

Codebox:
```c
(x > 0) && (x < 1)             // x is between 0 and 1
(x == 0) || (x == 1)           // x is 0 or 1
!(x == 0)                      // x is not zero
```

---

## Performance

All logic operators are extremely fast (~1 cycle).

---

## Related Topics

- [Comparison Operators](./comparison.md) - ==, <, >, etc.
- [Bitwise Operators](./bitwise.md) - &, |, ^, ~ (different from logical)
- [Control Flow](./control-flow.md) - switch, gate, mix
