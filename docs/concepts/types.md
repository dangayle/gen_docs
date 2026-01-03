# Type System

Gen uses automatic type inference. GenExpr and Codebox are syntactically typeless, but the compiler assigns types based on usage.

## Supported Types

**int**
- 32-bit signed integer
- Range: -2,147,483,648 to 2,147,483,647
- Literals: `5`, `-10`, `0`
- Use for counters, indices, discrete values

**float**
- 32-bit floating-point
- Range: ±3.4×10^-38 to ±3.4×10^38
- Literals: `5.0`, `3.14`, `1e-5`
- Use for audio samples, continuous values, trig results

Gen processes single-sample values at audio rate; all math is per-sample float or int.

## Type Inference

The compiler infers types from literals, operations, and function returns:
```c
x = 5;       // int (no decimal)
y = 5.0;     // float (has decimal)

i = 5;       // int
f = 3.0;     // float
result = i + f; // float (int + float widens)

x = sqrt(4.0);   // float
y = sin(x);      // float
```

Promotion hierarchy: `int → float`.

### Comparison Operations
Comparison operators return `0` or `1` (int):
```c
result = (x > 0.5);   // int 0 or 1
```

## Type Conversion

```c
int(3.7);         // 3 (truncate toward 0)
int(x * 100);     // convert to int

float(5);         // 5.0
float(x);         // ensure float
```

Division with a float operand yields float:
```c
a = 5;            // int
b = a / 2.0;      // float result
```

## Variables

Variables are inferred from first assignment; reassignment widens as needed when expressions require it.

## Functions

Parameter and return types are inferred from usage:
```c
distance(x, y) {
  return sqrt(x * x + y * y);  // returns float
}

something() {
  return 5, 3.0;   // int, float
}

a, b = something(); // a int, b float
```

## Edge Cases

**Floating-point precision**
```c
for (i = 0; i <= 1.0; i += 0.05) {
  // i might not hit 1.0 exactly
}
```

**Division by zero**
```c
result = 5 / 0;    // undefined
```

**Signals**
```c
sig = 0;           // treated as signal
result = sig + x;  // signal + input = signal
```

## Type Conversion Functions

| Function | Input | Output | Use |
|----------|-------|--------|-----|
| `int()` | float/int | int | Truncate to int |
| `float()` | int/float | float | Convert to float |
| `round()` | float | float | Round to nearest int |
| `floor()` | float | float | Round down |
| `ceil()` | float | float | Round up |

**Examples**
```c
param speed(100.0);
index = int(speed);                  // to int for indexing
scaled = float(sample) / 32768.0;    // normalize 16-bit sample
rounded = round(x * 100.0) / 100.0;  // round to 2 decimals
```

## Performance Notes
- Type inference: no runtime cost
- Promotion: compile-time
- Conversions emit instructions (int/float casts)
- Prefer matching types to avoid extra promotions

## Related Topics
- [GenExpr Overview](../genexpr/overview.md)
- [Codebox Overview](../codebox/overview.md)
- [Operators](../operators/)
- [Execution Model](./execution-model.md)
