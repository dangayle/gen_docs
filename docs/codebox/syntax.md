# Codebox Syntax Reference

Codebox is an imperative, C-like language for Gen. This document covers the complete syntax.

## Basic Syntax

### Statements

Codebox uses **statements** - instructions that perform actions:

```c
x = 5;              // Assignment statement
y = x + 2;          // Expression statement
if (x > 0) { }      // Control statement
```

**All statements end with semicolons** (or closing braces for blocks).

### Variables

Create variables by assignment:

```c
a = 5;              // a is created as int
b = 5.0;            // b is created as float
c = a + b;          // c is float (type promotion)
```

Variables are **case-sensitive**:

```c
x = 5;
X = 10;             // Different variable
```

## Literals

### Numbers

**Integers:**
```c
0
-5
1024
-2147483648        // Min 32-bit int
```

**Floating-point:**
```c
0.0
-3.14159
1e-6               // Scientific notation: 0.000001
1.5e3              // Scientific notation: 1500.0
```

### Boolean-like Values

Codebox uses integers for booleans:

```c
true_value = 1;    // True (non-zero)
false_value = 0;   // False (zero)
```

## Operators

### Arithmetic Operators

```c
a + b               // Addition
a - b               // Subtraction
a * b               // Multiplication
a / b               // Division
a % b               // Modulo (remainder)
```

### Comparison Operators

All return int (0 or 1):

```c
a == b              // Equal
a != b              // Not equal
a > b               // Greater than
a < b               // Less than
a >= b              // Greater than or equal
a <= b              // Less than or equal
```

### Logical Operators

```c
a && b              // Logical AND
a || b              // Logical OR
!a                  // Logical NOT
```

### Bitwise Operators

```c
a & b               // Bitwise AND
a | b               // Bitwise OR
a ^ b               // Bitwise XOR
~a                  // Bitwise NOT
a << b              // Left shift
a >> b              // Right shift (arithmetic)
a >>> b             // Right shift (logical)
```

### Assignment Operators

```c
x = 5;              // Simple assignment
x += 2;             // x = x + 2
x -= 2;             // x = x - 2
x *= 2;             // x = x * 2
x /= 2;             // x = x / 2
x %= 2;             // x = x % 2
x &= 0xFF;          // x = x & 0xFF
x |= 0x01;          // x = x | 0x01
x ^= 0xFF;          // x = x ^ 0xFF
x <<= 2;            // x = x << 2
x >>= 2;            // x = x >> 2
```

### Ternary Operator

```c
condition ? true_value : false_value

y = (x > 0) ? x : -x;  // Absolute value
```

### Increment/Decrement

```c
x++;                // Post-increment
++x;                // Pre-increment
x--;                // Post-decrement
--x;                // Pre-decrement
```

## Control Flow

### If/Else

```c
if (condition) {
  // Execute if true
}

if (condition) {
  // Execute if true
} else {
  // Execute if false
}

if (condition1) {
  // Execute if condition1 is true
} else if (condition2) {
  // Execute if condition2 is true
} else {
  // Execute if all are false
}
```

### For Loops

```c
for (init; condition; increment) {
  // body
}

for (i = 0; i < 10; i += 1) {
  sum += i;
}
```

**Loop initialization:**
```c
for (i = 0; i < 10; i += 1)    // Single init
for (i = 0, j = 10; i < j; i += 1, j -= 1)  // Multiple (rare)
```

**Loop increment:**
```c
for (i = 0; i < 10; i += 1)    // Increment by 1
for (i = 0; i < 10; i += 2)    // Increment by 2
for (i = 0; i < 10; ++i)       // Pre-increment
for (i = 10; i > 0; i -= 1)    // Decrement
```

### While Loops

```c
while (condition) {
  // body
  // Update condition somewhere
}

count = 0;
while (count < 10) {
  value += 1;
  count += 1;
}
```

### Do-While Loops

Execute body at least once:

```c
do {
  // body
  // Update condition somewhere
} while (condition);

i = 0;
do {
  y += i;
  i += 1;
} while (i < 10);
```

### Break and Continue

```c
for (i = 0; i < 10; i += 1) {
  if (i == 5) {
    break;          // Exit loop immediately
  }
  y += i;
}

for (i = 0; i < 10; i += 1) {
  if (i == 3) {
    continue;       // Skip to next iteration
  }
  y += i;
}
```

## Variables and Scope

### Local Variables

Variables are local to their scope by default:

```c
{
  int local_var = 5;    // Local to this block
  y = local_var;
}
// local_var is not accessible here
```

### Block Scope

Blocks create scope boundaries:

```c
if (condition) {
  temp = 5;             // Local to if block
  y = temp;
}
// temp not directly accessible here
```

### Inlets and Outlets

### Reading Inlets

```c
out = in;              // in (or in1) is first inlet
z = in2;               // Second inlet
```

### Writing Outlets

```c
out = value;           // out (or out1) is first outlet
out2 = value2;         // Second outlet
```

### Automatic Creation

Inlets and outlets are created automatically:

```c
// This creates 2 inlets and 1 outlet
out = in1 + in2;
```

## Parameters

### Parameter Declaration

```c
param name(default_value);

param frequency(440.0);
param resonance(1.0);
param mode(0);
```

**Parameter characteristics:**
- Can change without recompilation
- Update at control rate
- Have default values
- Accessible like variables

### Using Parameters

```c
param cutoff(1000.0);

filtered = x * sin(cutoff / 44100.0);
y = filtered;
```

## Functions

### Function Definition

```c
function_name(param1, param2) {
  // body
  return result;
}

add(a, b) {
  return a + b;
}

y = add(5, 3);      // y = 8
```

### Function with Multiple Parameters

```c
multiply_and_add(a, b, c) {
  result = (a * b) + c;
  return result;
}

y = multiply_and_add(2, 3, 4);  // y = 10
```

### Multiple Return Values

```c
swap(a, b) {
  return b, a;
}

y1, y2 = swap(x1, x2);
```

### Function with No Return

```c
process(x) {
  // No explicit return
  // Returns implicitly
}
```

## Comments

### Single-line Comments

```c
// This is a comment
x = 5;  // Inline comment
```

### Multi-line Comments

```c
/*
 * This is a block comment
 * Spanning multiple lines
 */
```

## Whitespace and Formatting

Whitespace is generally ignored:

```c
x=5;y=2;z=x+y;        // Compact
x = 5;
y = 2;
z = x + y;            // Spaced
```

Indentation is for readability only:

```c
if (x > 0) {
  y = x + 1;          // Standard 2 or 4 space indent
  z = y * 2;
}
```

## Operator Precedence

Operations follow standard C precedence:

| Level | Operators | Associativity |
|-------|-----------|---------------|
| 1 (highest) | `()` `[]` `!` `~` `++` `--` (unary) | Right |
| 2 | `*` `/` `%` | Left |
| 3 | `+` `-` (binary) | Left |
| 4 | `<<` `>>` `>>>` | Left |
| 5 | `<` `<=` `>` `>=` | Left |
| 6 | `==` `!=` | Left |
| 7 | `&` (bitwise) | Left |
| 8 | `^` (bitwise) | Left |
| 9 | `\|` (bitwise) | Left |
| 10 | `&&` | Left |
| 11 | `\|\|` | Left |
| 12 (lowest) | `? :` | Right |

Use parentheses for clarity:

```c
// Ambiguous
x = a + b * c;

// Clear
x = a + (b * c);
```

## Block Structure

### Block Syntax

```c
{
  statement1;
  statement2;
  statement3;
}
```

Blocks can be nested:

```c
if (x > 0) {
  if (y > 0) {
    z = x + y;
  }
}
```

## Type Conversion

### Implicit Conversion

```c
a = 5;              // int
b = 2.5;            // float
c = a + b;          // float (a promoted to float)
```

### Explicit Casting

```c
a = 5;
b = (float)a;       // Explicit cast to float
c = (int)3.7;       // Explicit cast to int (result: 3)
```

## Edge Cases

### Division by Zero

```c
// No runtime error, produces inf or nan
y = 1.0 / 0.0;      // Result: inf
z = 0.0 / 0.0;      // Result: nan
```

### Integer Division

```c
x = 7 / 2;          // Result: 3 (integer division, truncates)
y = 7.0 / 2;        // Result: 3.5 (float division)
z = 7 / 2.0;        // Result: 3.5 (float division)
```

### Operator Associativity

```c
// Right associative (ternary)
x = condition1 ? value1 : condition2 ? value2 : value3;

// Left associative (subtraction)
x = 10 - 5 - 2;     // (10 - 5) - 2 = 3, not 10 - (5 - 2) = 7
```

## Related Topics

- [Codebox Overview](./overview.md) - Language introduction
- [Function Definitions](./functions.md) - Detailed function documentation
- [Operators](../operators/index.md) - Operator reference
- [Type System](../concepts/types.md) - Type inference and coercion
- [Execution Model](../concepts/execution-model.md) - How code executes
