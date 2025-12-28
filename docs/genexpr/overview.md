# GenExpr Language Overview

GenExpr is the internal textual language used by Gen patchers. It is a compact, expression-oriented, C-like syntax that describes sample-by-sample computations. GenExpr code is compiled to optimized machine code for execution at audio rate.

## What is GenExpr?

GenExpr provides a **textual alternative** to the visual patching paradigm of Max. You write expressions that mirror the wiring you would build with Gen objects, but in a linear text form.

**Key characteristics:**
- **Expression-oriented** - Statements are primarily assignments and operator calls
- **C-like syntax** - Infix operators, semicolons, and braces for blocks where supported
- **Type inference** - Numeric types are inferred from use
- **Compiled** - Generates optimized code for real-time audio

## When to Use GenExpr

Use GenExpr when:
- You need concise, per-sample math and signal expressions
- You want a text view of what would otherwise be many operator boxes
- You prefer infix operator syntax without full imperative constructs

Use Codebox when you need imperative control flow (`for`, `while`, `break/continue`) or a more general C-like programming style.

## Basic Concepts

### Inlets and Outlets

GenExpr uses explicit inlet/outlet symbols:

```c
// in/out (or in1/out1) are the first inlet/outlet
out = in1 + in2;
```

Keywords:
- `in` / `in1`, `in2`, ... - Inlet references (read-only)
- `out` / `out1`, `out2`, ... - Outlet assignments (write-only per sample)

### Simple Expressions

A single expression that assigns to `out` writes to the first outlet:

```c
out = in * 2;
```

Multiple statements are separated by semicolons:

```c
x = in1 * 2;
y = in2 + 1;
out = x + y;
```

### Parameters

Parameters in GenExpr behave like `param` object boxes:

```c
param amplitude(0.5);      // Default 0.5
out = in * amplitude;
```

## Language Features

### Multiple Return Values

Some operators return multiple values. Use comma assignment to capture them:

```c
r, theta = cartopol(x, y);    // cartopol returns 2 values
```

If you only bind one target, unused values are discarded:

```c
r = cartopol(x, y);           // theta is dropped
```

### Control Flow

GenExpr provides expression-level selection, not full imperative loops. Use ternary or the `if` operator:

```c
out = (x > 0.5) ? cos(x * pi) : sin(x * pi);
// or
out = if(x > 0.5, cos(x * pi), sin(x * pi));
```

### Function Definitions

GenExpr supports simple function definitions with C-like syntax:

```c
sum(a, b) {
  return a + b;
}

out = sum(in1, in2);
```

Functions are inlined by the compiler and share the global scope of the Gen program. Recursion and unbounded control flow are not supported.

### Comments

GenExpr uses C-style comments:

```c
// Single-line comment
x = in1 + in2;  // Inline comment

/* Multi-line comment
  spanning multiple lines */
```

## GenExpr vs. Codebox

| Feature | GenExpr | Codebox |
|---------|---------|---------|
| **Style** | Expression-oriented | Imperative/C-like |
| **Syntax** | C-like expressions | Full C-like statements & loops |
| **Best for** | Formula-driven DSP | Procedural logic & loops |
| **Performance** | Same (compiled) | Same (compiled) |
| **Control flow** | Expression selection (`? :`, `if`) | `if/else`, `for`, `while`, `break/continue` |

## Type System

GenExpr is **type-inferred** at compile time:

```c
x = 5;        // int inferred
y = 5.0;      // float inferred
z = x + y;    // result promoted to float
```

See [Type System](../concepts/types.md) for coercion and promotion rules.

## Compilation and Execution

GenExpr code is parsed, type-inferred, optimized, and compiled to machine code automatically when you edit a Gen patcher. The compiled code runs per-sample at audio rate.

## Common Patterns

### Scaling/Offsetting a Signal

```scheme
param scale(1.0);
param offset(0.0);

out = in * scale + offset;
```

### Conditional Processing

```scheme
param threshold(0.5);

if (in > threshold) {
  out = in * 2;
} else {
  out = in * 0.5;
}
```

### Accumulating Values (State)

Use `history` (or related delay operators) for state across samples:

```c
accum = history(0);
accum = accum + in;
out = accum;
```

## Next Steps

- **[GenExpr Syntax](./syntax.md)** - Complete syntax reference
- **[Function Definitions](./functions.md)** - Detailed function documentation
- **[Operators](../operators/)** - Available operators
- **[Examples](./examples.md)** - Code examples
