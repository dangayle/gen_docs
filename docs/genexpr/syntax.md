# GenExpr Syntax Reference

GenExpr is an expression-oriented, C-like language for Gen patchers. Statements end with semicolons and typically assign to outlets.

## Basic Syntax

```c
out = in1 * 0.5;
y = in1 + in2;
```

## Literals

- Integers: `0`, `-5`, `1024`
- Floats: `0.0`, `-3.14159`, `1e-6`, `1.5e3`
- Booleans use integers: `0` (false) or non-zero (true)

## Symbols

Symbols reference variables, parameters, and operators: `in1`, `out`, `param_name`, `my_func`.

## Variables and Assignment

```c
x = 5;
y = x + 2;
z = y * 2;
```

Variables share a single global scope within the Gen patcher. A variable keeps its inferred type for the program.

### Multiple Assignment

Capture multiple returns with comma assignment:

```c
r, theta = cartopol(in1, in2);
```

## Inlets and Outlets

- `in` / `in1`, `in2`, ... read inlet values.
- `out` / `out1`, `out2`, ... write outlet values.

```c
out = in1 + in2;
out2 = in1 - in2;
```

## Operators and Functions

### Arithmetic

```c
a + b
a - b
a * b
a / b
a % b
```

### Comparison (returns int 0 or 1)

```c
a == b
a != b
a > b
a < b
a >= b
a <= b
```

### Logical

```c
a && b
a || b
!a
```

### Bitwise

```c
a & b
a | b
a ^ b
~a
a << b
a >> b
```

### Trigonometric

```c
sin(x)
cos(x)
tan(x)
asin(x)
acos(x)
atan(x)
atan2(y, x)
```

### Exponential / Log

```c
exp(x)
log(x)
log10(x)
pow(x, y)
sqrt(x)
```

### Rounding

```c
floor(x)
ceil(x)
round(x)
trunc(x)
```

### Type Conversion

```c
int(x)
float(x)
```

### Min/Max, Abs, Sign

```c
min(a, b)
max(a, b)
abs(x)
sign(x)
```

### Audio/Signal Helpers

```c
history(sig, n)
dcblock(sig)
phasor(phase_incr)
```

## Control Flow (Expression-Level)

GenExpr supports selection via ternary or the `if` operator. Imperative loops (`for`, `while`, `break`, `continue`) are not available.

```c
out = (x > 0) ? x : -x;
out = if(x > 0.5, sin(x * pi), cos(x * pi));
```

## Parameters

```c
param cutoff(1000.0);
param resonance(1.0);

out = sin(in * cutoff) * resonance;
```

Parameters behave like attributes, can change without recompilation, and are read at control/audio rate as provided by the host.

## Functions

```c
add(a, b) {
  return a + b;
}

cartesian_to_polar(x, y) {
  r = sqrt(x * x + y * y);
  theta = atan2(y, x);
  return r, theta;
}

out1, out2 = cartesian_to_polar(in1, in2);
```

- Functions are inlined by the compiler.
- Recursion and unbounded control flow are not supported.
- Variables inside functions share the global scope; prefix names to avoid collisions.

## Comments

```c
// Single-line comment
x = in1 + in2; // Inline comment

/* Multi-line comment
   spanning multiple lines */
```

## Whitespace and Formatting

Whitespace is ignored except as a separator. Use indentation for readability only.

## Expression Precedence

| Level | Operators | Associativity |
|-------|-----------|---------------|
| 1 (highest) | `()` | Left |
| 2 | `*`, `/`, `%` | Left |
| 3 | `+`, `-` | Left |
| 4 | `<<`, `>>`, `>>>` | Left |
| 5 | `<`, `<=`, `>`, `>=` | Left |
| 6 | `==`, `!=` | Left |
| 7 | `&` | Left |
| 8 | `^` | Left |
| 9 | `|` | Left |
| 10 | `&&` | Left |
| 11 | `||` | Left |
| 12 (lowest) | `? :` | Right |

Use parentheses for clarity:

```c
out = a + (b * c);
```

## Type Inference

Types are inferred from literals and operations:

```c
a = 5;          // int
b = 5.0;        // float
c = a + b;      // promoted to float
d = int(c);     // explicit cast
```

See [Type System](../concepts/types.md) for promotion rules.

## Inlets/Outlets Recap

```c
out = in1 + in2;     // creates two inlets and one outlet
out2 = in1 - in2;    // creates second outlet
```
