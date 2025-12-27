# Type System

Gen uses automatic type inference to determine variable types during compilation. While GenExpr and Codebox are syntactically type-less, the compiler assigns types based on usage patterns and context.

## Supported Types

### Primitive Types

**int**
- 32-bit signed integer
- Range: -2,147,483,648 to 2,147,483,647
- Literals: `5`, `-10`, `0`
- Used for: Counters, indices, discrete values

**float**
- 32-bit floating-point number
- Range: ±3.4×10^-38 to ±3.4×10^38
- Literals: `5.0`, `3.14`, `1e-5`
- Used for: Audio samples, continuous values, trigonometric results

### Signal Types

**signal**
- Vector of audio samples processed at block rate
- Size depends on Gen object's block size
- Used for: Audio processing, multi-sample operations

## Type Inference

Gen infers types automatically based on:

1. **Literal values** - Number format determines initial type
   ```scheme
   x = 5;      ; int (no decimal point)
   y = 5.0;    ; float (has decimal point)
   ```

2. **Operations** - Result type based on operands
   ```scheme
   i = 5;              ; int
   f = 3.0;            ; float
   result = i + f;     ; float (widened from int + float)
   ```

3. **Function return types** - Based on built-in function
   ```scheme
   x = sqrt(4.0);      ; float (sqrt returns float)
   y = sin(x);         ; float (sin returns float)
   ```

## Type Coercion (Promotion)

When combining different types, Gen **promotes** to the wider type:

**Hierarchy (narrow → wide):**
```
int → float → signal
```

### Examples

```scheme
; int + int = int
result = 5 + 3;           ; → 8 (int)

; int + float = float
result = 5 + 3.0;         ; → 8.0 (float)

; float + float = float
result = 5.0 + 3.0;       ; → 8.0 (float)

; Arithmetic with signals
sig = x * 0.5;            ; signal * float = signal
```

### Comparison Operations

All comparison operators return **0 (false) or 1 (true)** as int:

```scheme
result = (x > 0.5);       ; result is int (0 or 1)
```

## Type Conversion

Explicit type conversions can be requested:

### int() - Convert to Integer

```scheme
result = int(3.7);        ; → 3 (truncates)
result = int(x * 100);    ; Convert scaled float to int
```

### float() - Convert to Float

```scheme
result = float(5);        ; → 5.0
result = float(x);        ; Ensure x is float
```

### Automatic Conversions

Some operations implicitly convert:

```scheme
a = 5;                    ; a is int
b = a / 2.0;              ; Division by float forces float result
```

## Variables and Type Determination

Variables gain their type on first assignment:

```scheme
x = 5;            ; x becomes int
x = 5.0;          ; ERROR: Can't reassign different type

y = 5;            ; y becomes int
z = y + 1.0;      ; z is float (promoted from int + float)
```

### Inference from Usage

The compiler can infer type from subsequent operations:

```scheme
result = sqrt(x);         ; result becomes float (sqrt always returns float)
index = 0;
while (index < 10) {      ; index remains int (comparison doesn't change type)
  index += 1;
}
```

## Function Parameters and Return Types

When defining functions, parameter and return types are inferred from usage:

```scheme
; Parameters are inferred as float (math operation implies float)
distance(x, y) {
  sqrt(x * x + y * y)
}

; Return type inferred as float from sqrt()
```

### Multiple Return Values

Different returns can have different inferred types:

```scheme
something() {
  return 5, 3.0;          ; return int, float
}

a, b = something();       ; a is int, b is float
```

## Edge Cases and Considerations

### Floating-Point Precision

Float operations can lose precision:

```scheme
; Floating-point loop may not reach exact target
for (i = 0; i <= 1.0; i += 0.05) {
  ; i might not exactly equal 1.0 at the end
  ; Use: for (i = 0; i <= 1.04; i += 0.05)
}
```

### Division by Zero

Integer division by zero causes problems:

```scheme
result = 5 / 0;    ; Undefined behavior
```

### Vector Operations

When signals are involved:

```scheme
sig = 0;           ; Treated as signal (broadcast value)
result = sig + x;  ; signal + input = signal
```

## Type Conversion Functions

### Numeric Conversions

| Function | Input | Output | Use |
|----------|-------|--------|-----|
| `int()` | float/int | int | Truncate to integer |
| `float()` | int/float | float | Convert to float |
| `round()` | float | float | Round to nearest integer |
| `floor()` | float | float | Round down |
| `ceil()` | float | float | Round up |

### Examples

```scheme
param speed(100.0);
index = int(speed);        ; Convert parameter to int for indexing

scaled = float(sample) / 32768.0;  ; Normalize 16-bit sample

rounded = round(x * 100.0) / 100.0;  ; Round to 2 decimal places
```

## Performance Notes

- **Type inference has no runtime cost** - All determined at compile time
- **Promotion has minimal cost** - Handled during compilation
- **Explicit conversions add instructions** - int(x) and float(x) compile to conversion instructions
- **Prefer matching types** - Avoids unnecessary promotions

## Related Topics

- [GenExpr Overview](../genexpr/overview.md) - Language basics
- [Codebox Overview](../codebox/overview.md) - Alternative syntax
- [Operators](../operators/) - How operators handle types
- [Execution Model](./execution-model.md) - When type inference occurs
