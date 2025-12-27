# Operators Reference

Gen provides a comprehensive set of operators for signal processing, mathematical computation, and control flow. Operators are built-in functions you can use in both GenExpr and Codebox.

## Operator Categories

### [Arithmetic](./arithmetic.md)
Basic mathematical operations: addition, subtraction, multiplication, division, modulo.

**Common operators:** `+`, `-`, `*`, `/`, `%`, `mod`, `rsub`, `rdiv`, `rmod`

### [Comparison](./comparison.md)
Compare values and return boolean results (0 or 1).

**Common operators:** `==`, `!=`, `<`, `>`, `<=`, `>=`, `step`

### [Logic](./logic.md)
Logical operations on boolean values.

**Common operators:** `!`, `not`, `&&`, `and`, `||`, `or`, `^^`, `xor`, `bool`

### [Bitwise](./bitwise.md)
Bit-level operations on integers.

**Common operators:** `&`, `|`, `^`, `~`, `<<`, `>>`, `>>>`

### [Math Functions](./math.md)
Mathematical operations and transformations.

**Common operators:** `abs`, `neg`, `min`, `max`, `sign`, `clamp`, `fold`, `wrap`, `scale`

### [Rounding](./rounding.md)
Rounding and numeric conversion.

**Common operators:** `floor`, `ceil`, `round`, `trunc`, `fract`, `int`, `float`

### [Trigonometry](./trigonometry.md)
Trigonometric and hyperbolic functions.

**Common operators:** `sin`, `cos`, `tan`, `asin`, `acos`, `atan`, `atan2`, `sinh`, `cosh`, `tanh`

### [Powers and Logarithms](./powers.md)
Exponential and logarithmic operations.

**Common operators:** `sqrt`, `pow`, `exp`, `exp2`, `log`, `log10`, `log2`, `ln`, `fastexp`, `fastpow`

### [Constants](./constants.md)
Mathematical and conversion constants.

**Common operators:** `pi`, `e`, `phi`, `twopi`, `DEGTORAD`, `RADTODEG`

### [Signal Processing](./signal-processing.md)
Audio/signal-specific operations.

**Common operators:** `history`, `dcblock`, `phasor`, `noise`, `cartopol`, `poltocar`, `hypot`, `absdiff`

### [Type Conversion](./type-conversion.md)
Convert between different numeric types.

**Common operators:** `int`, `float`, `bool`

### [Control Flow](./control-flow.md)
Routing and selection operators.

**Common operators:** `?`, `switch`, `gate`, `mix`, `selector`, `smoothstep`

### [Range](./range.md)
Operations for constraining values within ranges.

**Common operators:** `clamp`, `fold`, `wrap`, `scale`, `step`

### [Input/Output](./io.md)
Access inlets and outlets.

**Common operators:** `in`, `out`, `send`, `receive`, `gate`

## Quick Lookup

### By Syntax

| Category | Operators |
|----------|-----------|
| **Arithmetic** | `+`, `-`, `*`, `/`, `%` |
| **Comparison** | `==`, `!=`, `<`, `>`, `<=`, `>=` |
| **Logic** | `!`, `&&`, `\|\|`, `^^` |
| **Bitwise** | `&`, `\|`, `^`, `~`, `<<`, `>>`, `>>>` |
| **Assignment** | `=`, `+=`, `-=`, `*=`, `/=`, etc. |
| **Ternary** | `? :` |

### By Function

| Use Case | Operators |
|----------|-----------|
| **Scaling** | `scale`, `*`, `/` |
| **Clamping** | `clamp`, `min`, `max` |
| **Filtering** | `history`, `dcblock` |
| **Oscillation** | `sin`, `cos`, `phasor` |
| **Frequency** | `pow`, `exp`, `cartopol`, `poltocar` |
| **Selection** | `?`, `switch`, `mix`, `selector` |
| **Type Checking** | `int`, `float` |
| **Normalization** | `abs`, `sign`, `scale` |

## Operator Characteristics

### Real-Time Safe
All Gen operators are designed to be real-time safe:
- No memory allocation
- Deterministic execution time
- No blocking operations

### Type Inference
Types are inferred automatically:
```scheme
; int + int = int
x = 5 + 3;          ; Result: int

; int + float = float
y = 5 + 3.0;        ; Result: float

; float * float = float
z = 5.0 * 3.0;      ; Result: float
```

### Operator Overloading
Many operators work with multiple input types:

```scheme
min(5, 3);          ; Returns 3 (int)
min(5.0, 3.0);      ; Returns 3.0 (float)
max(10, 20, 30);    ; Returns 30 (variadic)
```

## Common Patterns

### Scaling a Signal
```scheme
// GenExpr
out = in * scale_factor;

// Codebox
y = x * scale_factor;
```

### Clamping to Range
```scheme
// GenExpr
out = (min (max in min_val) max_val);

// Codebox
y = clamp(x, min_val, max_val);
```

### Conditional Selection
```scheme
// GenExpr
out = (if (> in threshold) in 0);

// Codebox
y = (x > threshold) ? x : 0;
```

### Mixing Two Signals
```scheme
// GenExpr
out = (mix in1 in2 mix_amount);

// Codebox
y = mix(x1, x2, mix_amount);
```

## Performance Notes

### Fast Operations (< 1 cycle)
- Basic arithmetic: `+`, `-`, `*`, `/`
- Comparison: `==`, `<`, `>`, etc.
- Logical: `!`, `&&`, `||`
- Bitwise: `&`, `|`, `^`, `<<`, `>>`

### Medium Operations (5-20 cycles)
- Trigonometry: `sin`, `cos`, `tan`, `asin`, `acos`, `atan`
- Exponential/Logarithmic: `sqrt`, `pow`, `exp`, `log`
- Conversions: `int()`, `float()`

### Slower Operations (20+ cycles)
- `atan2` (50+ cycles)
- `log10`, `log2` (50+ cycles)
- Hyperbolic: `sinh`, `cosh`, `tanh` (50+ cycles)
- Approximations may be faster: `fastsin`, `fastcos`, `fastexp`, `fastpow`

### Optimized Versions
Use faster approximations when precision isn't critical:
- `fastsin` / `fastcos` instead of `sin` / `cos`
- `fastexp` instead of `exp`
- `fastpow` instead of `pow`

## Related Topics

- [Type System](../concepts/types.md) - Type inference and coercion
- [Execution Model](../concepts/execution-model.md) - How operators execute
- [Performance](../concepts/performance.md) - Performance characteristics
- [GenExpr Overview](../genexpr/overview.md) - GenExpr syntax
- [Codebox Overview](../codebox/overview.md) - Codebox syntax
