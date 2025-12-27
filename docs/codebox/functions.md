# Codebox Function Definitions

Functions in Codebox allow you to encapsulate and reuse computations. This document covers function definition, calling, and patterns.

## Basic Function Definition

```c
add(a, b) {
  return a + b;
}

result = add(5, 3);  // result = 8
```

### Function Parts

```c
add(a, b) {          // Function name and parameters
  return a + b;      // Body and return statement
}
```

**Key parts:**
- **Function name** - How you call the function
- **Parameters** - Inputs to the function
- **Body** - Statements inside the function
- **Return statement** - Value to return

## Parameters

### Single Parameter

```c
square(x) {
  return x * x;
}

y = square(5);       // y = 25
```

### Multiple Parameters

```c
multiply_and_add(a, b, c) {
  return (a * b) + c;
}

result = multiply_and_add(2, 3, 4);  // (2 * 3) + 4 = 10
```

### Parameter Usage

Parameters are local to the function:

```c
add_with_scale(value, scale) {
  return value * scale;
}

x = 5;
y = add_with_scale(x, 2);  // value=5, scale=2
```

## Return Values

### Single Return Value

```c
get_double(x) {
  return x * 2;
}

y = get_double(5);  // y = 10
```

### Multiple Return Values

```c
swap(a, b) {
  return b, a;
}

y1, y2 = swap(10, 20);  // y1 = 20, y2 = 10
```

### Conditional Return

```c
absolute_value(x) {
  if (x >= 0) {
    return x;
  } else {
    return -x;
  }
}

y = absolute_value(-5);  // y = 5
```

### Early Return

```c
find_max(a, b) {
  if (a > b) {
    return a;
  }
  return b;
}

y = find_max(10, 7);  // y = 10
```

### Implicit Return

The last expression is implicitly returned:

```c
add(a, b) {
  a + b  // No explicit return needed
}

y = add(3, 4);  // y = 7
```

## Function Patterns

### Scaling Pattern

```c
scale_signal(input, scale_factor) {
  return input * scale_factor;
}

y = scale_signal(x, 0.5);
```

### Threshold Pattern

```c
apply_threshold(value, threshold) {
  if (abs(value) > threshold) {
    return value;
  } else {
    return 0;
  }
}

y = apply_threshold(x, 0.1);
```

### Accumulation Pattern

```c
// Note: This modifies state outside the function
accumulate(value) {
  total = total + value;
  return total;
}

sum = accumulate(5);  // sum = 5
sum = accumulate(3);  // sum = 8
```

### Conditional Processing

```c
conditional_amplify(input, condition, gain) {
  if (condition > 0) {
    return input * gain;
  } else {
    return input;
  }
}

y = conditional_amplify(x, gate, 2.0);
```

### Clipping Pattern

```c
soft_clip(value, limit) {
  if (value > limit) {
    return limit;
  } else if (value < -limit) {
    return -limit;
  } else {
    return value;
  }
}

y = soft_clip(x, 1.0);
```

## Function Composition

### Chaining Calls

```c
double(x) {
  return x * 2;
}

square(x) {
  return x * x;
}

result = square(double(5));  // square(10) = 100
```

### Nested Calls

```c
max_of_three(a, b, c) {
  return max(max(a, b), c);
}

y = max_of_three(1, 5, 3);  // y = 5
```

### Combining Operations

```c
process_value(x, scale, offset) {
  scaled = x * scale;
  return scaled + offset;
}

y = process_value(10, 0.5, 5);  // (10 * 0.5) + 5 = 10
```

## Scope and Variables

### Local Variables in Functions

Variables created in functions are local:

```c
calculate(x) {
  temp = x * 2;      // Local to function
  result = temp + 1;
  return result;
}

y = calculate(5);
// temp is not accessible here
```

### Global Variables from Functions

Functions can access global variables:

```c
state = 0;

increment_state() {
  state = state + 1;
  return state;
}

y = increment_state();  // y = 1
z = increment_state();  // z = 2
```

### Naming Conventions

Use prefixes to avoid collisions:

```c
// DSP module
dsp_lowpass(input, cutoff) {
  return input;  // Simplified
}

dsp_highpass(input, cutoff) {
  return input;  // Simplified
}

// Utility module
util_clamp(value, min, max) {
  if (value > max) return max;
  if (value < min) return min;
  return value;
}

// Oscillator module
osc_sine(phase) {
  return sin(phase * 2 * 3.14159);
}
```

## Recursion

### Simple Recursion

```c
factorial(n) {
  if (n <= 1) {
    return 1;
  } else {
    return n * factorial(n - 1);
  }
}

y = factorial(5);  // y = 120
```

### Limited Recursion

Keep recursion shallow to avoid stack overflow:

```c
fibonacci(n) {
  if (n <= 1) {
    return n;
  } else {
    return fibonacci(n - 1) + fibonacci(n - 2);
  }
}

// Only use with small n (n < 30)
y = fibonacci(10);
```

## Common Patterns

### Filter State Helper

```c
// Stateful filter function
lp_filter(input, coefficient) {
  lp_state = lp_state + (input - lp_state) * coefficient;
  return lp_state;
}

filtered = lp_filter(noisy_signal, 0.1);
```

### Envelope Helper

```c
exponential_decay(current, decay_rate) {
  return current * decay_rate;
}

envelope = 1.0;
envelope = exponential_decay(envelope, 0.95);
```

### Parameter Scaling

```c
param velocity_scale(1.0);

scale_by_velocity(value, velocity) {
  return value * velocity * velocity_scale;
}

output = scale_by_velocity(signal, midi_velocity);
```

### Multi-Stage Processing

```c
process(input) {
  // Stage 1: Normalize
  normalized = input / 32768.0;
  
  // Stage 2: Filter (simplified)
  filtered = normalized * 0.9;
  
  // Stage 3: Amplify
  amplified = filtered * 2.0;
  
  return amplified;
}

y = process(x);
```

### Lookup and Interpolation

```c
// Simple linear interpolation
lerp(a, b, t) {
  return a * (1.0 - t) + b * t;
}

y = lerp(0.0, 1.0, 0.5);  // y = 0.5
```

## Performance Notes

### Inlining

Function calls are inlined by the compiler—no runtime overhead:

```c
// These are equivalent after compilation
result = expensive_function(x);
result = (expensive_computation);
```

### Avoid Redundant Calls

```c
// GOOD: Cache the value
sin_x = sin(x);
y = sin_x + cos(sin_x);

// LESS EFFICIENT: Compute sin twice
y = sin(x) + cos(sin(x));
```

## Function Signature Reference

| Aspect | Syntax | Example |
|--------|--------|---------|
| **Definition** | `name(params) { }` | `add(a, b) { return a + b; }` |
| **Call** | `name(args)` | `add(5, 3)` |
| **Parameters** | Comma-separated | `func(a, b, c)` |
| **Return** | `return value;` | `return x + 1;` |
| **Multiple return** | `return a, b;` | `return x, y;` |
| **Receive multiple** | `a, b = func();` | `x, y = swap(a, b);` |

## Debugging Functions

### Output Intermediate Values

```c
debug_process(input) {
  step1 = input * 2;
  step2 = step1 + 1;
  step3 = step2 / 2;
  
  y1 = step1;    // Debug output
  y2 = step2;    // Debug output
  y3 = step3;    // Debug output
  
  return step3;
}
```

### Test with Constants

```c
test_function() {
  return add(5, 3);  // Expected: 8
}

result = test_function();
```

### Incremental Testing

```c
// Test simple version first
simple(x) {
  return x * 2;
}

// Then extend
complex(x) {
  result = simple(x);
  result = result + 1;
  result = result / 2;
  return result;
}
```

## Related Topics

- [Codebox Syntax](./syntax.md) - Complete syntax reference
- [Codebox Overview](./overview.md) - Language introduction
- [Operators](../operators/index.md) - Built-in operator reference
- [Memory and State](../concepts/memory-state.md) - State management
- [Performance](../concepts/performance.md) - Performance considerations
