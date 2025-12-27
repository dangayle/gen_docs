# GenExpr Language Overview

GenExpr is the internal language used by Gen patchers. It is a functional, expression-oriented language designed to describe computations in an implementation-agnostic manner. GenExpr code is translated into optimized machine code for execution on the CPU.

## What is GenExpr?

GenExpr provides a **textual alternative** to the visual patching paradigm of Max. You write code in text form that expresses the same computations you would create by patching object boxes together.

**Key characteristics:**
- **Expression-oriented** - Emphasis on composing expressions
- **Type-less** - Variables gain types automatically through inference
- **Compiled** - Code is compiled to optimized C before execution
- **Functional** - Built-in operators work like functions

## When to Use GenExpr

Use GenExpr when:
- You need to express complex logic concisely
- You're combining many operators
- You prefer textual over visual representation
- You want to reuse code through functions

Compare to using the `expr` operator or `codebox` for simpler, more imperative approaches.

## Basic Concepts

### Inlets and Outlets

GenExpr respects Max's concepts of inlets and outlets:

```scheme
; in and in1, in2, ... reference inlets
; out and out1, out2, ... reference outlets

out = in1 + in2
```

Special keywords:
- `in` / `in1`, `in2`, ... - Inlet references (input values)
- `out` / `out1`, `out2`, ... - Outlet references (output assignments)

### Simple Expressions

A single expression implicitly assigns to the first outlet:

```scheme
in * 2                    ; Same as: out1 = in * 2
```

Multiple statements require semicolons:

```scheme
x = in1 * 2;
y = in2 + 1;
out = x + y
```

### Parameters

Parameters in GenExpr behave like `param` object boxes:

```scheme
param amplitude(0.5);      ; Create a parameter with default 0.5
out = in * amplitude;
```

## Language Features

### Multiple Return Values

Some operations return multiple values. Use tuple-style assignment:

```scheme
r, theta = cartopol(x, y)    ; cartopol returns 2 values
```

If you only need one value, the others are optimized away:

```scheme
r = cartopol(x, y)           ; theta calculation is eliminated
```

### Control Flow

GenExpr supports branching and looping:

```scheme
if (x > 0.5) {
  out = cos(x * pi);
} else {
  out = sin(x * pi);
}
```

### Function Definitions

Define reusable functions:

```scheme
f(a, b) {
  a + b
}

out = f(in1, in2)
```

Functions support:
- Multiple parameters
- Multiple return values
- Attributes (like `param` objects)
- Scope isolation

### Comments

GenExpr uses C-style comments:

```scheme
// Single-line comment
x = in1 + in2;  // Inline comment

/* Multi-line comment
   spanning multiple lines */
```

## GenExpr vs. Codebox

| Feature | GenExpr | Codebox |
|---------|---------|---------|
| **Style** | Expression-oriented | Imperative/C-like |
| **Syntax** | Scheme-like, functional | C-like |
| **Best for** | Complex expressions | Complex logic & loops |
| **Performance** | Same (both compiled) | Same (both compiled) |
| **Ease** | Good for formula-based | Good for procedural code |

## Type System

GenExpr is **type-less** at the syntax level. Types are inferred by the compiler:

```scheme
x = 5;        ; int inferred
y = 5.0;      ; float inferred
z = x + y;    ; result is float (type promotion)
```

See [Type System](../concepts/types.md) for complete details on type coercion and conversion.

## Compilation and Execution

GenExpr code goes through these stages:

1. **Parse** - Syntax analysis
2. **Analyze** - Type inference and operator resolution
3. **Optimize** - Dead code elimination, strength reduction
4. **Compile** - Generate C code
5. **Execute** - Run compiled code at audio/sample rate

This happens automatically when you edit GenExpr code in a Gen patcher.

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

```scheme
// Note: for state management, use history() or dedicated constructs
// This is a basic example of flow control
accumulator = 0;
for (i = 0; i < 10; i += 1) {
  accumulator += i;
}
out = accumulator;
```

## Next Steps

- **[GenExpr Syntax](./syntax.md)** - Complete syntax reference
- **[Function Definitions](./functions.md)** - Detailed function documentation
- **[Operators](../operators/)** - Available operators
- **[Examples](./examples.md)** - Code examples
