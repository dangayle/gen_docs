# GenExpr Function Definitions

Functions in GenExpr allow you to encapsulate and reuse computations. This document covers function definition, calling, and patterns.

## Basic Function Definition

### Simple Function (Infix)

```scheme
add(a, b) {
  a + b
}

result = add(5, 3);   ; Result: 8
```

### Simple Function (Prefix)

```scheme
(defun add (a b)
  (+ a b))

(set! result (add 5 3))  ; Result: 8
```

### Function with No Parameters

```scheme
get_constant() {
  3.14159
}

pi = get_constant();  ; pi = 3.14159
```

### Function with No Return

Functions without an explicit return implicitly return the last expression:

```scheme
process_value(x) {
  y = x * 2;
  y = y + 1;
  y                  ; This is returned
}

result = process_value(5);  ; Result: 11
```

## Parameters

### Single Parameter

```scheme
square(x) {
  x * x
}

y = square(5);       ; y = 25
```

### Multiple Parameters

```scheme
multiply_and_add(a, b, c) {
  (a * b) + c
}

result = multiply_and_add(2, 3, 4);  ; (2 * 3) + 4 = 10
```

### Parameter Scope

Parameters are local to the function in terms of naming, but Gen has limited scope protection:

```scheme
scale_input(scale_factor) {
  input * scale_factor
}

; scale_factor is a parameter here, shadowing outer scope if it exists
y = scale_input(0.5);
```

### Default Parameters

GenExpr doesn't support default parameters in functions. Use outer scope or parameters:

```scheme
param global_scale(1.0);

apply_scale(value) {
  value * global_scale    ; Uses outer parameter
}

y = apply_scale(5);       ; Uses global_scale value
```

## Return Values

### Single Return Value

The last expression in a function is the return value:

```scheme
add_one(x) {
  x + 1                ; This is returned
}

y = add_one(5);        ; y = 6
```

### Multiple Return Values

Return multiple values using tuple notation:

```scheme
cartesian_to_polar(x, y) {
  r = sqrt(x * x + y * y);
  theta = atan2(y, x);
  r, theta            ; Return both
}

magnitude, angle = cartesian_to_polar(3, 4);
```

Receiving multiple returns:

```scheme
; Tuple assignment (infix)
a, b, c = multi_return_function();

; Prefix syntax
(set! (a b c) (multi-return-function))
```

### Returning Conditionally

```scheme
absolute_value(x) {
  if (x >= 0) {
    x
  } else {
    -x
  }
}

y = absolute_value(-5);  ; y = 5
```

## Function Composition

### Chaining Calls

```scheme
double(x) { x * 2 }
square(x) { x * x }

result = square(double(5));  ; square(10) = 100
```

### Higher-Order Patterns

GenExpr doesn't support true higher-order functions, but you can structure code similarly:

```scheme
apply_operation(a, b, operation) {
  if (operation == 0) {
    a + b
  } else if (operation == 1) {
    a * b
  } else {
    a - b
  }
}

result = apply_operation(5, 3, 1);  ; Multiply: 5 * 3 = 15
```

## Recursive Functions

Recursion is supported but requires explicit depth control:

```scheme
; Factorial (recursive)
factorial(n) {
  if (n <= 1) {
    1
  } else {
    n * factorial(n - 1)
  }
}

result = factorial(5);  ; Result: 120
```

**Warning:** Deep recursion can cause stack overflow. Limit recursion depth:

```scheme
fibonacci(n) {
  if (n <= 1) {
    n
  } else {
    fibonacci(n - 1) + fibonacci(n - 2)
  }
}

; Use only with small n (n < 30 recommended)
```

## Function Patterns

### Filter Pattern

```scheme
lowpass_filter(input, cutoff) {
  coeff = 0.1;  ; Simplified coefficient
  state = 0;
  state = state + (input - state) * coeff;
  state
}

filtered = lowpass_filter(noisy_signal, 1000);
```

### Accumulator Pattern

```scheme
accumulate(input, scale) {
  accumulator = 0;
  accumulator += input * scale;
  accumulator
}

sum = accumulate(samples, 1.0);
```

### Conditional Processing

```scheme
gate(input, threshold) {
  if (abs(input) > threshold) {
    input
  } else {
    0
  }
}

gated = gate(signal, 0.1);
```

### Waveshaping

```scheme
soft_clip(input) {
  ; Soft clipping using tanh-like approximation
  x = input;
  if (x > 1.0) {
    1.0
  } else if (x < -1.0) {
    -1.0
  } else {
    x
  }
}

clipped = soft_clip(loud_signal);
```

### Scaling and Normalization

```scheme
normalize(input, min_val, max_val) {
  (input - min_val) / (max_val - min_val)
}

normalized = normalize(value, 0, 255);  ; 0-255 to 0-1
```

## Function Organization

### Helper Functions

Create small, focused functions:

```scheme
get_frequency_bin(freq, samplerate) {
  (freq / samplerate) * 2 * pi
}

get_decay_coefficient(time_ms, samplerate) {
  pow(0.001, 1.0 / (time_ms * samplerate / 1000.0))
}

; Use helpers in main computation
bin = get_frequency_bin(440, 44100);
decay = get_decay_coefficient(1000, 44100);
```

### Namespace Conventions

Since Gen has limited scope, use prefixes to avoid collisions:

```scheme
; DSP module functions
dsp_lowpass(input, cutoff) { ... }
dsp_highpass(input, cutoff) { ... }

; Utility functions
util_normalize(value, scale) { ... }
util_clamp(value, min, max) { ... }

; Oscillator functions
osc_sine(phase, frequency) { ... }
osc_square(phase, frequency) { ... }
```

## Calling Conventions

### Positional Arguments

```scheme
multiply(a, b, c) {
  a * b * c
}

result = multiply(2, 3, 4);  ; a=2, b=3, c=4
```

### Call from Inlets

```scheme
add_inputs(in1, in2) {
  in1 + in2
}

out = add_inputs(input1, input2);
```

### Call with Literals

```scheme
power_of_two(x) {
  pow(x, 2)
}

y = power_of_two(5);         ; Works: y = 25
z = power_of_two(in);        ; Works: uses current inlet value
```

## Function Scope and Variables

### Global Variables in Functions

All variables in Gen are effectively global:

```scheme
global_var = 0;

increment_global() {
  global_var = global_var + 1;
  global_var
}

y = increment_global();  ; Modifies and returns global_var
z = increment_global();  ; z = 2
```

### Avoiding Name Collisions

Use function prefixes:

```scheme
filter_state = 0;      ; Module-level state

filter_process(input) {
  filter_state = filter_state * 0.9 + input * 0.1;
  filter_state
}

osc_phase = 0;         ; Different module

osc_process(freq) {
  osc_phase = osc_phase + freq / 44100;
  if (osc_phase > 1) {
    osc_phase = osc_phase - 1;
  }
  sin(osc_phase * 2 * pi)
}
```

## Common Patterns

### Parameter Wrapping

```scheme
param velocity_scale(1.0);

; Use parameter in function
scale_velocity(velocity) {
  velocity * velocity_scale
}

scaled = scale_velocity(100);
```

### Stateful Processing

```scheme
; Filter with state
lp_state = 0;

lowpass(input, amount) {
  lp_state = lp_state + (input - lp_state) * amount;
  lp_state
}

filtered = lowpass(raw_signal, 0.05);
```

### Multi-Stage Processing

```scheme
; Compose multiple operations
process_signal(sig) {
  ; Stage 1: Normalize
  norm = sig / 32768;
  
  ; Stage 2: Filter
  filt = lowpass(norm, 0.1);
  
  ; Stage 3: Amplify
  amp = filt * gain;
  
  amp
}

out = process_signal(in);
```

## Debugging Functions

### Output Intermediate Values

```scheme
debug_add(a, b) {
  sum = a + b;
  out1 = a;      ; Debug: output a
  out2 = b;      ; Debug: output b
  out3 = sum;    ; Debug: output result
  sum
}
```

### Test with Constants

```scheme
test_function() {
  add(5, 3)      ; Test with known values
}

result = test_function();  ; Expected: 8
```

### Simplify and Test

```scheme
; Start simple
simple_version(x) {
  x * 2
}

; Gradually add complexity
complex_version(x) {
  y = x * 2;
  y = y + 1;
  y = y / 3;
  y
}
```

## Function Signature Summary

| Aspect | Syntax |
|--------|--------|
| **Definition** | `name(params) { body }` |
| **Call** | `name(args)` |
| **Multiple params** | `name(a, b, c)` |
| **Multiple return** | `a, b = multi_return()` |
| **Recursion** | Supported (with depth limit) |
| **Scope** | Global (limited local scope) |

## Performance Considerations

### Function Call Overhead

Function calls are inlined by the compiler—no runtime overhead.

```scheme
; These are equivalent after compilation
result = expensive_function(x);
result = (expensive_computation);
```

### Avoid Redundant Calls

```scheme
; BAD: Computes sin twice
y = sin(x) + cos(sin(x));

; GOOD: Cache the value
sin_x = sin(x);
y = sin_x + cos(sin_x);
```

## Related Topics

- [GenExpr Syntax](./syntax.md) - Complete syntax reference
- [GenExpr Overview](./overview.md) - Language introduction
- [Operators](../operators/index.md) - Built-in operator reference
- [Memory and State](../concepts/memory-state.md) - State management
- [Performance](../concepts/performance.md) - Performance considerations
