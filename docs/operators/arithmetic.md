# Arithmetic Operators

Basic mathematical operations for addition, subtraction, multiplication, division, and modulo.

## Addition `+`

**Syntax:**
```scheme
; GenExpr
(+ a b)
(+ a b c d ...)

; Codebox
a + b
a + b + c + d + ...
```

**Description:**
Adds two or more values together.

**Parameters:**
- `a`, `b`, ... - Values to add (int or float)

**Returns:**
- Sum of inputs
- Type: int if all inputs are int, float otherwise

**Examples:**

GenExpr:
```scheme
out = (+ 5 3);                    ; 8
sum = (+ in1 in2);                ; Add two inlets
total = (+ 1 2 3 4 5);            ; Add multiple values
```

Codebox:
```c
y = 5 + 3;                        // 8
sum = x1 + x2;                    // Add two inlets
total = 1 + 2 + 3 + 4 + 5;       // Add multiple values
```

**Notes:**
- Works with any number of inputs
- Associative: `(a + b) + c = a + (b + c)`
- Type promotion: int + float = float

---

## Subtraction `-`

**Syntax:**
```scheme
; GenExpr
(- a b)
(- a)

; Codebox
a - b
-a
```

**Description:**
Subtracts the second value from the first, or negates a single value.

**Parameters:**
- `a` - First value (minuend)
- `b` - Second value (subtrahend)

**Returns:**
- Difference (a - b)
- Type: int if both inputs are int, float otherwise

**Examples:**

GenExpr:
```scheme
out = (- 5 3);                    ; 2
diff = (- in1 in2);               ; in1 minus in2
negated = (- in);                 ; Negate input
```

Codebox:
```c
y = 5 - 3;                        // 2
diff = x1 - x2;                   // x1 minus x2
negated = -x;                     // Negate input
```

**Notes:**
- NOT associative: `(5 - 3) - 2 = 0`, but `5 - (3 - 2) = 4`
- Single argument negates: `(- x) = 0 - x`

---

## Multiplication `*`

**Syntax:**
```scheme
; GenExpr
(* a b)
(* a b c d ...)

; Codebox
a * b
a * b * c * d * ...
```

**Description:**
Multiplies two or more values together.

**Parameters:**
- `a`, `b`, ... - Values to multiply (int or float)

**Returns:**
- Product of inputs
- Type: int if all inputs are int, float otherwise

**Examples:**

GenExpr:
```scheme
out = (* 5 3);                    ; 15
scaled = (* in scale_factor);      ; Scale input
product = (* 2 3 4);               ; 24
```

Codebox:
```c
y = 5 * 3;                        // 15
scaled = x * scale_factor;        // Scale input
product = 2 * 3 * 4;              // 24
```

**Notes:**
- Works with any number of inputs
- Associative: `(a * b) * c = a * (b * c)`
- Commutative: `a * b = b * a`
- Multiplying by 0 gives 0
- Multiplying by 1 (identity): no change

**Performance:**
- Very fast (~1 cycle)
- Preferred for scaling instead of division

---

## Division `/`

**Syntax:**
```scheme
; GenExpr
(/ a b)

; Codebox
a / b
```

**Description:**
Divides the first value by the second.

**Parameters:**
- `a` - Dividend (numerator)
- `b` - Divisor (denominator)

**Returns:**
- Quotient (a / b)
- Type: Determined by input types

**Examples:**

GenExpr:
```scheme
out = (/ 10 2);                   ; 5
divided = (/ in1 in2);             ; in1 divided by in2
normalized = (/ in 32768.0);       ; Normalize to [-1, 1]
```

Codebox:
```c
y = 10 / 2;                       // 5
divided = x1 / x2;                // x1 divided by x2
normalized = x / 32768.0;         // Normalize
```

**Notes:**
- NOT associative: `(16 / 4) / 2 = 2`, but `16 / (4 / 2) = 8`
- Integer division truncates: `7 / 2 = 3`
- Float division preserves decimals: `7.0 / 2 = 3.5`
- Division by zero results in inf or nan (no error)

**Performance:**
- Slower than multiplication (~10-20 cycles)
- Use multiplication by reciprocal when possible: `a / b ≈ a * (1/b)` if b is constant

**Division by Zero:**
```scheme
(/ 1.0 0.0)        ; Returns inf
(/ 0.0 0.0)        ; Returns nan
(/ 1 0)            ; May return inf or undefined
```

---

## Modulo `%` or `mod`

**Syntax:**
```scheme
; GenExpr
(% a b)
(mod a b)

; Codebox
a % b
mod(a, b)
```

**Description:**
Returns the remainder of a divided by b.

**Parameters:**
- `a` - Dividend
- `b` - Divisor

**Returns:**
- Remainder of a / b
- Type: Same as inputs

**Examples:**

GenExpr:
```scheme
out = (% 7 3);                    ; 1 (7 mod 3)
wrapped = (% in 1.0);              ; Keep in 0-1 range
phase_wrap = (% in twopi);         ; Wrap phase to 0-2π
```

Codebox:
```c
y = 7 % 3;                        // 1
wrapped = x % 1.0;                // Keep in 0-1 range
phase_wrap = x % (2 * pi);        // Wrap phase
```

**Notes:**
- Result has same sign as dividend (a): `-7 % 3 = -1`
- Useful for wrapping values into a range
- Alternative to `wrap` operator for simple cases
- **NOT the same as mathematical modulo** for negative numbers

**Modulo vs. Wrap:**
```scheme
; Modulo (C-style)
(% -0.5 1.0)       ; Returns -0.5

; Wrap (always positive)
(wrap -0.5 0 1)    ; Returns 0.5
```

---

## Reverse Subtraction `!-` or `rsub`

**Syntax:**
```scheme
; GenExpr
(!- a b)
(rsub a b)

; Codebox
!-(a, b)
rsub(a, b)
```

**Description:**
Subtracts the first value from the second (reverse order).

**Parameters:**
- `a` - First value (will be subtracted from)
- `b` - Second value (subtract from this)

**Returns:**
- b - a (reverse order)

**Examples:**

GenExpr:
```scheme
out = (!- in 5);                  ; 5 - in (invert control)
invert = (!- in1 in2);             ; in2 - in1
```

Codebox:
```c
y = rsub(x, 5);                   // 5 - x
invert = rsub(x1, x2);            // x2 - x1
```

**Notes:**
- Equivalent to `(- b a)` in prefix notation
- Useful for reversing subtraction operands
- Less common than standard subtraction

---

## Reverse Division `!/` or `rdiv`

**Syntax:**
```scheme
; GenExpr
(!/ a b)
(rdiv a b)

; Codebox
!/(a, b)
rdiv(a, b)
```

**Description:**
Divides the second value by the first (reverse order).

**Parameters:**
- `a` - Divisor (divides)
- `b` - Dividend (is divided)

**Returns:**
- b / a (reverse order)

**Examples:**

GenExpr:
```scheme
out = (!/ scale in);              ; in / scale
reciprocal = (!/ x 1.0);           ; 1.0 / x
```

Codebox:
```c
y = rdiv(scale, x);               // x / scale
reciprocal = rdiv(x, 1.0);        // 1.0 / x
```

**Notes:**
- Equivalent to `(/ b a)` in prefix notation
- Useful for computing reciprocals or inverting division

---

## Reverse Modulo `!%` or `rmod`

**Syntax:**
```scheme
; GenExpr
(!% a b)
(rmod a b)

; Codebox
!%(a, b)
rmod(a, b)
```

**Description:**
Returns the remainder of b divided by a (reverse order).

**Parameters:**
- `a` - Divisor (divides)
- `b` - Dividend (is divided)

**Returns:**
- b % a (remainder)

**Examples:**

GenExpr:
```scheme
out = (!% 3 7);                   ; 7 % 3 = 1
```

Codebox:
```c
y = rmod(3, 7);                   // 7 % 3 = 1
```

**Notes:**
- Reverse order compared to standard modulo
- Equivalent to `(% b a)` in prefix notation

---

## Negation

Single-argument subtraction negates a value.

**Syntax:**
```scheme
; GenExpr
(- x)

; Codebox
-x
```

**Description:**
Reverses the sign of a value.

**Examples:**

GenExpr:
```scheme
out = (- in);                     ; Negate input
inverted = (- (+ in offset));      ; Negate sum
```

Codebox:
```c
y = -x;                           // Negate input
inverted = -(x + offset);         // Negate sum
```

---

## Combining Arithmetic Operators

**Order of Operations:**
Follow standard mathematical precedence:
1. Parentheses `()`
2. Negation `-` (unary)
3. Multiplication `*`, Division `/`, Modulo `%`
4. Addition `+`, Subtraction `-`

**Examples:**

```scheme
; GenExpr
(+ (* 2 3) 4)                     ; (2 * 3) + 4 = 10
(* (+ 2 3) 4)                     ; (2 + 3) * 4 = 20

; Codebox
(2 * 3) + 4                       // 10
(2 + 3) * 4                       // 20
```

---

## Performance Comparison

| Operator | Speed | Notes |
|----------|-------|-------|
| `+` | ~1 cycle | Addition is fast |
| `-` | ~1 cycle | Subtraction is fast |
| `*` | ~1 cycle | Multiplication is fast |
| `/` | ~10-20 cycles | Slower, use multiplication if possible |
| `%` | ~10-20 cycles | Similar cost to division |

---

## Type Promotion

When mixing int and float:

```scheme
; GenExpr
(+ 5 3.0)          ; Result: float (8.0)
(* 2 4.0)          ; Result: float (8.0)

; Codebox
5 + 3.0            // Result: float
2 * 4.0            // Result: float
```

---

## Related Topics

- [Comparison Operators](./comparison.md) - Compare values
- [Math Functions](./math.md) - abs, min, max, clamp
- [Powers](./powers.md) - sqrt, pow, exp
- [Type System](../concepts/types.md) - Type inference
