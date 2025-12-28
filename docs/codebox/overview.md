# Codebox Language Overview

Codebox is an imperative, C-like language for writing Gen code. It provides a more traditional programming approach compared to GenExpr's functional style, with explicit control flow and procedural code organization.

## What is Codebox?

Codebox is a **textual language** embedded in Gen patchers that uses syntax similar to C or JavaScript. It emphasizes **imperative programming** - expressing computation as a sequence of statements that modify state.

**Key characteristics:**
- **Imperative/Procedural** - Step-by-step command execution
- **C-like syntax** - Familiar to C/C++/JavaScript programmers
- **Type-less** - Types inferred automatically
- **Compiled** - Compiles to optimized code before execution

## When to Use Codebox

Use Codebox when:
- You have complex logic with many conditionals
- You prefer traditional programming style
- You're more comfortable with C/JavaScript than Lisp-like syntax
- You need explicit loops and state manipulation

Codebox and GenExpr are **functionally equivalent** - both compile to the same optimized code. Choose based on what feels natural to you.

## Basic Concepts

### Inlets and Outlets

Codebox uses the same inlet/outlet names as GenExpr:

```c
// Read from inlets, write to outlets
out = in + 5;         // in (or in1) is the first inlet, out/out1 is the first outlet
```

Keywords:
- `in` / `in1`, `in2`, ... - Inlet variables (read-only)
- `out` / `out1`, `out2`, ... - Outlet variables (write)

### Simple Assignments

Basic computation:

```c
y = x * 2;
```

### Statements

Codebox requires **semicolons** for each statement:

```c
a = x + 1;
b = a * 2;
y = b - 3;
```

## Language Features

### Variables and Scope

Variables are created on first use:

```c
temp = x * x;      // temp is created (inferred as float)
result = temp + 1;
y = result;
```

Variables are **local to their scope** by default.

### Control Flow: If/Else

```c
if (x > 0.5) {
  y = 1.0;
} else if (x > 0.25) {
  y = 0.5;
} else {
  y = 0.0;
}
```

### Control Flow: Loops

**For loops:**
```c
sum = 0;
for (i = 0; i < 10; i += 1) {
  sum += i;
}
y = sum;
```

**While loops:**
```c
count = 0;
value = x;
while (value < 1.0) {
  value *= 2;
  count += 1;
}
y = count;
```

**Break and Continue:**
```c
for (i = 0; i < 10; i += 1) {
  if (x > 5.0) {
    break;        // Exit loop early
  }
  if (i == 3) {
    continue;     // Skip to next iteration
  }
  y += i;
}
```

### Multiple Assignment/Return

Assign multiple values in one statement:

```c
a, b = x + 1, x - 1;
y1, y2 = sin(x), cos(x);
```

### Comments

C-style comments:

```c
// Single-line comment
x = 5;  // Inline comment

/* Multi-line comment
   spanning multiple lines */
```

## Type System

Codebox is **type-less** syntactically. Types are inferred during compilation:

```c
a = 5;          // int inferred
b = 5.0;        // float inferred
c = a + b;      // float (result of int + float)
```

See [Type System](../concepts/types.md) for details on type inference and coercion.

## Parameters

Create parameters with the `param` keyword:

```c
param frequency(440.0);    // Parameter with default value
param resonance(1.0);

y = sin(x * frequency) * resonance;
```

Parameters:
- Appear as Max attributes on the Gen object
- Can be set from outside the Gen patcher
- Support ranges and other constraints (advanced)

## Functions in Codebox

Define reusable functions:

```c
double(v) {
  return v * 2;
}

y = double(x) + double(x + 1);
```

**Function features:**
- Multiple parameters
- Multiple return values
- Local variables within function scope
- Can define parameters and attributes

### Multiple Return Values

```c
// Function with multiple outputs
swap(a, b) {
  return b, a;
}

// Use it
y1, y2 = swap(x1, x2);
```

## Codebox vs. GenExpr

| Feature | Codebox | GenExpr |
|---------|---------|---------|
| **Style** | Imperative/procedural | Expression-oriented/functional |
| **Syntax** | C-like | Scheme-like |
| **Best for** | Complex logic & loops | Complex expressions |
| **Performance** | Same (both compiled) | Same (both compiled) |
| **Ease** | Good for procedural | Good for formula-based |

## Common Patterns

### Threshold with Hysteresis

```c
param threshold(0.5);
param hysteresis(0.1);

if (x > threshold + hysteresis) {
  y = 1.0;
} else if (x < threshold - hysteresis) {
  y = 0.0;
} else {
  y = 0.0;  // or keep previous state with history
}
```

### Multi-Stage Processing

```c
// Stage 1: Normalize
normalized = x / 2.0;

// Stage 2: Apply function
processed = sin(normalized * pi);

// Stage 3: Scale
y = processed * 0.5;
```

### Conditional Accumulation

```c
param maxValue(100.0);

accumulated = 0;
for (i = 0; i < 10; i += 1) {
  if (accumulated < maxValue) {
    accumulated += i;
  }
}
y = accumulated;
```

## Next Steps

- **[Codebox Syntax](./syntax.md)** - Complete syntax reference
- **[Function Definitions](./functions.md)** - Detailed function documentation
- **[Operators](../operators/)** - Available operators
- **[Examples](./examples.md)** - Code examples
