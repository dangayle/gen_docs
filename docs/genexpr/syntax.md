# GenExpr Syntax Reference

GenExpr is a functional, expression-oriented language. This document covers the complete syntax.

## Basic Syntax

### Expressions vs. Statements

GenExpr is fundamentally **expression-based**:

```scheme
; Expression (produces a value)
(+ 2 3)          ; Result: 5

; Statement (performs an action)
(set! x 5)       ; Sets x to 5
```

### S-Expressions (Symbolic Expressions)

GenExpr uses prefix notation with parentheses:

```scheme
; Format: (operator arg1 arg2 ...)
(+ 1 2)          ; 1 + 2
(* 3 4)          ; 3 * 4
(- 5 2)          ; 5 - 2
(/ 10 2)         ; 10 / 2
```

### Infix Alternative Syntax

GenExpr also supports familiar infix notation:

```scheme
; Infix (C-like syntax)
x = 5;
y = x + 2;
z = x * y;

; Prefix (Scheme-like syntax)
(set! x 5)
(set! y (+ x 2))
(set! z (* x y))
```

**Note:** Both syntaxes can be mixed in the same file.

## Literals

### Numbers

**Integers:**
```scheme
0
-5
1024
-2147483648 ; min 32-bit int
```

**Floating-point:**
```scheme
0.0
-3.14159
1e-6        ; Scientific notation
1.5e3       ; = 1500.0
```

### Boolean Values

```scheme
true
false
0           ; False-like
1           ; True-like
```

GenExpr uses integers for boolean logic (0 = false, non-zero = true).

### Symbols

Symbols reference variables, parameters, and functions:

```scheme
x            ; Variable reference
in1          ; Inlet reference
param_name   ; Parameter reference
my_function  ; Function reference
```

## Variables and Assignment

### Variable Declaration and Assignment

Infix style:

```scheme
x = 5;              ; Declare and assign
y = x + 2;          ; Use in expression
z = y * 2;          ; Chain assignments
```

Prefix style:

```scheme
(set! x 5)
(set! y (+ x 2))
(set! z (* y 2))
```

### Variable Scope

Variables are **global to the Gen object**. Once declared, they're accessible everywhere:

```scheme
x = 5;
y = 10;

if (x > 0) {
  z = x + y;       ; Can access x and y here
}

; z is still accessible here
result = z * 2;
```

### Multiple Assignment

Simultaneous assignment to multiple variables:

```scheme
; Prefix syntax
(set! (x y z) (cartopol in1 in2))

; Infix syntax
r, theta = cartopol(in1, in2);
```

## Operators and Functions

### Arithmetic Operators

```scheme
(+ a b)             ; Addition
(- a b)             ; Subtraction
(* a b)             ; Multiplication
(/ a b)             ; Division
(% a b)             ; Modulo (remainder)
```

Infix equivalents:

```scheme
a + b
a - b
a * b
a / b
a % b
```

### Comparison Operators

All return int (0 or 1):

```scheme
(== a b)            ; Equal
(!= a b)            ; Not equal
(> a b)             ; Greater than
(< a b)             ; Less than
(>= a b)            ; Greater than or equal
(<= a b)            ; Less than or equal
```

Infix equivalents:

```scheme
a == b
a != b
a > b
a < b
a >= b
a <= b
```

### Logical Operators

```scheme
(and a b)           ; Logical AND
(or a b)            ; Logical OR
(not a)             ; Logical NOT
```

Infix equivalents:

```scheme
a && b              ; AND
a || b              ; OR
!a                  ; NOT
```

### Bitwise Operators

```scheme
(bit-and a b)       ; Bitwise AND
(bit-or a b)        ; Bitwise OR
(bit-xor a b)       ; Bitwise XOR
(bit-not a)         ; Bitwise NOT
(<< a b)            ; Left shift
(>> a b)            ; Right shift (arithmetic)
(>>> a b)           ; Right shift (logical)
```

Infix equivalents:

```scheme
a & b               ; AND
a | b               ; OR
a ^ b               ; XOR
~a                  ; NOT
a << b              ; Left shift
a >> b              ; Right shift
```

### Trigonometric Functions

```scheme
(sin x)             ; Sine (radians)
(cos x)             ; Cosine (radians)
(tan x)             ; Tangent (radians)
(asin x)            ; Arc sine
(acos x)            ; Arc cosine
(atan x)            ; Arc tangent (single arg)
(atan2 y x)         ; Arc tangent (two args) - angle in radians
```

### Exponential and Logarithmic

```scheme
(exp x)             ; e^x
(log x)             ; Natural logarithm
(log10 x)           ; Base-10 logarithm
(pow x y)           ; x^y (x to the power of y)
(sqrt x)            ; Square root
```

### Rounding Functions

```scheme
(floor x)           ; Round down
(ceil x)            ; Round up
(round x)           ; Round to nearest
(trunc x)           ; Truncate toward zero
```

### Type Conversion

```scheme
(int x)             ; Convert to integer
(float x)           ; Convert to floating-point
```

### Min/Max

```scheme
(min a b)           ; Minimum
(max a b)           ; Maximum
(min a b c d ...)   ; Minimum of many values
(max a b c d ...)   ; Maximum of many values
```

### Absolute Value and Sign

```scheme
(abs x)             ; Absolute value
(sign x)            ; Sign (-1, 0, or 1)
```

### Audio-Specific Functions

```scheme
(history sig n)     ; Access n samples back
(dcblock sig)       ; DC blocking filter
(phasor phase_incr) ; Phase ramp (0 to 1)
```

## Control Flow

### If-Else

Prefix syntax:

```scheme
(if condition
  then_expression
  else_expression)

(if (> x 0)
  (+ x 1)
  (- x 1))
```

Infix syntax:

```scheme
if (condition) {
  statement1;
  statement2;
} else {
  statement3;
  statement4;
}

if (x > 0) {
  y = x + 1;
} else {
  y = x - 1;
}
```

### Ternary Operator

```scheme
condition ? true_value : false_value

(x > 0) ? (x + 1) : (x - 1)
```

### For Loops

Infix syntax:

```scheme
for (init; condition; increment) {
  body;
}

for (i = 0; i < 10; i += 1) {
  accumulator += array[i];
}
```

Prefix syntax:

```scheme
(for ((i 0)) (< i 10) ((set! i (+ i 1)))
  (statement))
```

### While Loops

Infix syntax:

```scheme
while (condition) {
  body;
}

i = 0;
while (i < 10) {
  count += 1;
  i += 1;
}
```

Prefix syntax:

```scheme
(while (< i 10)
  (statement))
```

### Loop Control

```scheme
break;              ; Exit loop
continue;           ; Skip to next iteration
```

## Parameters

### Parameter Declaration

```scheme
param name(default_value);

param frequency(440.0);
param resonance(1.0);
param mode(0);
```

Parameters:
- Can be changed from outside without recompilation
- Update at control rate (1-4ms blocks)
- Have default values
- Behave like global variables within Gen code

### Using Parameters

```scheme
param cutoff(1000.0);

y = input * sin(cutoff / 44100.0);
```

## Functions

### Function Definition

Prefix syntax (Scheme-like):

```scheme
(defun function_name (param1 param2)
  (+ param1 param2))

(defun multiply (a b)
  (* a b))

out = (multiply in1 in2)
```

Infix syntax (C-like):

```scheme
function_name(param1, param2) {
  param1 + param2
}

multiply(a, b) {
  a * b
}

y = multiply(in1, in2);
```

### Functions with Multiple Returns

```scheme
; Return multiple values with comma operator
cartesian_to_polar(x, y) {
  r = sqrt(x * x + y * y);
  theta = atan2(y, x);
  r, theta    ; Return both values
}

; Use tuple assignment
radius, angle = cartesian_to_polar(10, 10);
```

### Local Variables in Functions

Variables in functions are accessible globally (Gen has no true local scope):

```scheme
add_with_offset(a, b) {
  offset = 5;     ; This is global
  a + b + offset
}
```

To avoid collisions, use naming conventions:

```scheme
add_with_temp(a, b) {
  add_temp_offset = 5;    ; Prefixed to avoid collisions
  a + b + add_temp_offset
}
```

## Comments

### Single-line Comments

```scheme
; Scheme-style comment
// C-style comment
x = 5;  ; Inline comment
```

### Multi-line Comments

```scheme
/*
 * Multi-line comment
 * Spans multiple lines
 */

(comment
  This is a block comment
  in Scheme style)
```

## Whitespace and Formatting

Whitespace is generally ignored:

```scheme
x=1; y=2; z=x+y;    ; Compact

x = 1;
y = 2;
z = x + y;          ; Spaced out

; Both are equivalent
```

Indentation is for readability only (no significance):

```scheme
if (x > 0) {
  y = x + 1;        ; 2 spaces
  z = y * 2;
} else {
  y = x - 1;
}
```

## Expression Precedence

When mixing operators, follow standard precedence:

| Level | Operators | Associativity |
|-------|-----------|---------------|
| 1 (highest) | `()` Parentheses | Left |
| 2 | `*`, `/`, `%` | Left |
| 3 | `+`, `-` | Left |
| 4 | `<<`, `>>`, `>>>` | Left |
| 5 | `<`, `<=`, `>`, `>=` | Left |
| 6 | `==`, `!=` | Left |
| 7 | `&` Bitwise AND | Left |
| 8 | `^` Bitwise XOR | Left |
| 9 | `\|` Bitwise OR | Left |
| 10 | `&&` Logical AND | Left |
| 11 | `\|\|` Logical OR | Left |
| 12 (lowest) | `? :` Ternary | Right |

Use parentheses for clarity:

```scheme
; Ambiguous
x = a + b * c;      ; Is it (a + b) * c or a + (b * c)?

; Clear
x = a + (b * c);    ; Multiplication first
```

## Type Inference

GenExpr infers types automatically:

```scheme
a = 5;              ; int
b = 5.0;            ; float
c = a + b;          ; float (promoted from int)
d = (int)c;         ; Explicit cast to int
```

See [Type System](../concepts/types.md) for complete type coercion rules.

## Inlets and Outlets

### Reading Inlets

```scheme
; First inlet
x = in;             ; Alternative: in1

; Multiple inlets
x = in1;
y = in2;
z = in3;
```

### Writing Outlets

```scheme
; First outlet
out = computed_value;       ; Alternative: out1

; Multiple outlets
out1 = value1;
out2 = value2;
out3 = value3;
```

### Automatic Creation

Inlets and outlets are created automatically based on use:

```scheme
; This code creates 2 inlets and 1 outlet
out = in1 + in2;
```

## Special Forms

### Set! (Assignment)

Prefix syntax for assignment:

```scheme
(set! x 5)
(set! y (+ x 1))
(set! (a b) (values 1 2))   ; Multiple assignment
```

### Seq (Sequential Execution)

Execute multiple expressions in sequence:

```scheme
(seq
  (set! x 5)
  (set! y (+ x 1))
  (+ x y))
```

## Edge Cases and Gotchas

### Division by Zero

```scheme
; This returns infinity or NaN (no runtime error)
y = 1.0 / 0.0;      ; Result: inf
z = 0.0 / 0.0;      ; Result: nan
```

### Integer Division

```scheme
; Integer division truncates
x = 7 / 2;          ; Result: 3 (not 3.5)
y = 7.0 / 2;        ; Result: 3.5 (float division)
```

### Type Mismatches

```scheme
; Automatic type promotion
x = 5 + 2.5;        ; Result: 7.5 (float)
y = 5 + 2;          ; Result: 7 (int)
```

## Related Topics

- [GenExpr Overview](./overview.md) - Language introduction
- [Function Definitions](./functions.md) - Detailed function documentation
- [Operators](../operators/index.md) - Operator reference
- [Type System](../concepts/types.md) - Type inference and coercion
- [Execution Model](../concepts/execution-model.md) - How code executes
