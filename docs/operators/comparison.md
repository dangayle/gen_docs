# Comparison Operators

Compare two values and return a boolean result (0 for false, 1 for true).

## Equal `==` or `eq`

**Syntax:**
```scheme
; GenExpr
(== a b)
(eq a b)

; Codebox
a == b
eq(a, b)
```

**Description:**
Returns 1 if both values are equal, 0 otherwise.

**Parameters:**
- `a` - First value
- `b` - Second value

**Returns:**
- 1 if a equals b, 0 otherwise
- Type: int (0 or 1)

**Examples:**

GenExpr:
```scheme
(== 5 5)          ; 1 (true)
(== 5 3)          ; 0 (false)
(== in1 threshold) ; Check if input equals threshold
result = (== in 0) ; Detect zero
```

Codebox:
```c
(5 == 5)          // 1 (true)
(5 == 3)          // 0 (false)
(x1 == threshold) // Check equality
result = (x == 0) // Detect zero
```

**Notes:**
- Works with int and float
- Floating-point comparison: use with caution due to precision issues
- Result is always 0 or 1

---

## Not Equal `!=` or `neq`

**Syntax:**
```scheme
; GenExpr
(!= a b)
(neq a b)

; Codebox
a != b
neq(a, b)
```

**Description:**
Returns 1 if values are not equal, 0 otherwise.

**Parameters:**
- `a` - First value
- `b` - Second value

**Returns:**
- 1 if a ≠ b, 0 otherwise
- Type: int (0 or 1)

**Examples:**

GenExpr:
```scheme
(!= 5 3)          ; 1 (true)
(!= 5 5)          ; 0 (false)
(if (!= in 0) ...) ; If not zero
```

Codebox:
```c
(5 != 3)          // 1 (true)
(x != 0)          // If not zero
```

---

## Less Than `<` or `lt`

**Syntax:**
```scheme
; GenExpr
(< a b)
(lt a b)

; Codebox
a < b
lt(a, b)
```

**Description:**
Returns 1 if first value is less than second, 0 otherwise.

**Parameters:**
- `a` - First value
- `b` - Second value

**Returns:**
- 1 if a < b, 0 otherwise
- Type: int (0 or 1)

**Examples:**

GenExpr:
```scheme
(< 3 5)           ; 1 (true)
(< in threshold)  ; Check if input below threshold
```

Codebox:
```c
(3 < 5)           // 1 (true)
(x < threshold)   // Check if below threshold
```

---

## Greater Than `>` or `gt`

**Syntax:**
```scheme
; GenExpr
(> a b)
(gt a b)

; Codebox
a > b
gt(a, b)
```

**Description:**
Returns 1 if first value is greater than second, 0 otherwise.

**Parameters:**
- `a` - First value
- `b` - Second value

**Returns:**
- 1 if a > b, 0 otherwise
- Type: int (0 or 1)

**Examples:**

GenExpr:
```scheme
(> 5 3)           ; 1 (true)
(> in 0.5)        ; Check if input above threshold
```

Codebox:
```c
(5 > 3)           // 1 (true)
(x > 0.5)         // Check if above threshold
```

---

## Less Than or Equal `<=` or `lte`

**Syntax:**
```scheme
; GenExpr
(<= a b)
(lte a b)

; Codebox
a <= b
lte(a, b)
```

**Description:**
Returns 1 if first value is less than or equal to second, 0 otherwise.

**Parameters:**
- `a` - First value
- `b` - Second value

**Returns:**
- 1 if a ≤ b, 0 otherwise
- Type: int (0 or 1)

**Examples:**

GenExpr:
```scheme
(<= 3 5)          ; 1 (true)
(<= in max_value) ; Check within limit
```

Codebox:
```c
(3 <= 5)          // 1 (true)
(x <= max_value)  // Within limit
```

---

## Greater Than or Equal `>=` or `gte`

**Syntax:**
```scheme
; GenExpr
(>= a b)
(gte a b)

; Codebox
a >= b
gte(a, b)
```

**Description:**
Returns 1 if first value is greater than or equal to second, 0 otherwise.

**Parameters:**
- `a` - First value
- `b` - Second value

**Returns:**
- 1 if a ≥ b, 0 otherwise
- Type: int (0 or 1)

**Examples:**

GenExpr:
```scheme
(>= 5 3)          ; 1 (true)
(>= in threshold) ; At or above threshold
```

Codebox:
```c
(5 >= 3)          // 1 (true)
(x >= threshold)  // At or above
```

---

## Step (GLSL-Style)

**Syntax:**
```scheme
; GenExpr
(step edge value)

; Codebox
step(edge, value)
```

**Description:**
Returns 0 if value < edge, 1 otherwise. Similar to GLSL's step function.

**Parameters:**
- `edge` - Threshold value
- `value` - Value to test

**Returns:**
- 0 if value < edge, 1 otherwise
- Type: int (0 or 1)

**Examples:**

GenExpr:
```scheme
(step 0.5 0.3)    ; 0 (0.3 < 0.5)
(step 0.5 0.7)    ; 1 (0.7 >= 0.5)
(step threshold in) ; Check if input meets threshold
```

Codebox:
```c
step(0.5, 0.3)    // 0 (0.3 < 0.5)
step(0.5, 0.7)    // 1 (0.7 >= 0.5)
step(threshold, x) // Threshold gate
```

---

## Pass-Through Comparison Operators

Some operators return the original value if the comparison is true, else zero.

### Equal-Pass `==p` or `eqp`

Returns first value if equal to second, else zero.

**Syntax:**
```scheme
(==p a b)
(eqp a b)
```

**Equivalent to:** `a * (a == b)`

**Example:**
```scheme
(==p in 0)        ; Returns in if in equals 0, else 0
```

### Not Equal-Pass `!=p` or `neqp`

Returns first value if not equal to second, else zero.

**Syntax:**
```scheme
(!=p a b)
(neqp a b)
```

**Equivalent to:** `a * (a != b)`

---

### Less Than-Pass `<p` or `ltp`

Returns first value if less than second, else zero.

**Syntax:**
```scheme
(<p a b)
(ltp a b)
```

**Equivalent to:** `a * (a < b)`

---

### Greater Than-Pass `>p` or `gtp`

Returns first value if greater than second, else zero.

**Syntax:**
```scheme
(>p a b)
(gtp a b)
```

**Equivalent to:** `a * (a > b)`

---

### Less Than or Equal-Pass `<=p` or `ltep`

Returns first value if less than or equal to second, else zero.

**Syntax:**
```scheme
(<=p a b)
(ltep a b)
```

**Equivalent to:** `a * (a <= b)`

---

### Greater Than or Equal-Pass `>=p` or `gtep`

Returns first value if greater than or equal to second, else zero.

**Syntax:**
```scheme
(>=p a b)
(gtep a b)
```

**Equivalent to:** `a * (a >= b)`

---

## Chaining Comparisons

**Using Logical AND:**

GenExpr:
```scheme
; Check if x is in range [a, b]
(and (>= x a) (<= x b))
(and (> x min) (< x max))
```

Codebox:
```c
// Check if x is in range [a, b]
(x >= a) && (x <= b)
(x > min) && (x < max)
```

**Using Multiple Conditions:**

GenExpr:
```scheme
; Equivalent conditions
(if (and (> in 0) (< in 1)) ...)
```

Codebox:
```c
// Equivalent conditions
if ((x > 0) && (x < 1)) { }
```

---

## Floating-Point Comparison Caveats

Due to floating-point precision, exact equality comparisons can fail:

```scheme
; May not work as expected due to floating-point rounding
x = 0.1 + 0.2;          ; Often 0.30000000000000004, not 0.3
(== x 0.3)              ; Returns 0 (false)

; Better approach: compare with tolerance
(< (abs (- x 0.3)) 0.0001)  ; Within tolerance
```

---

## Performance

All comparison operators are extremely fast (~1 cycle).

---

## Common Patterns

### Threshold Detection
```scheme
; GenExpr
(> in threshold)

; Codebox
x > threshold
```

### Range Checking
```scheme
; GenExpr
(and (>= in min) (<= in max))

; Codebox
(x >= min) && (x <= max)
```

### Gate Logic
```scheme
; GenExpr: Pass input if above threshold
(* in (> in threshold))

; Codebox
y = x * (x > threshold) ? 1 : 0;
```

---

## Related Topics

- [Logic Operators](./logic.md) - AND, OR, NOT
- [Math Functions](./math.md) - min, max, clamp
- [Control Flow](./control-flow.md) - switch, gate
