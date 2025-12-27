# Memory and State Management

Gen manages memory automatically, but understanding how state works is critical for avoiding bugs and writing efficient code.

## Variable Storage and Scope

### Local Variables

Variables created within expressions or statements exist for the lifetime of the Gen object:

```scheme
; All variables are global to the Gen object's execution
my_value = 0;
accumulator = 0;
filter_state = 0;

; These persist across all sample processing
y = my_value + input;
accumulator += y;
filter_state = (filter_state + y) * 0.9;
```

**Key point:** Gen has no local scope within functions—all variables are accessible from anywhere.

### Initialization Timing

Variables are initialized when the Gen object loads:

```scheme
; These are set once
initial_sum = 0;
scale = 1.0 / 127.0;

; Then used repeatedly during audio rate
out = input * scale + initial_sum;
```

Reassignment at audio rate updates the variable:

```scheme
; This updates the variable for the next sample
position += velocity;
out = sin(position);
```

## State Variables (Feedback)

### Creating State with Feedback

Simple feedback creates state:

```scheme
; previous_output persists and affects future computation
previous_output = 0;  ; Initialization

; This creates a 1-sample delay
current_output = input + previous_output * 0.95;
previous_output = current_output;  ; Update for next sample
y = current_output;
```

### Multi-Sample History

Use `history()` to access previous samples:

```scheme
; Access previous samples without explicit variables
one_back = history(input, 1);    ; Last sample
two_back = history(input, 2);    ; Two samples ago
three_back = history(input, 3);  ; Three samples ago
```

**Example - 3-tap FIR filter:**
```scheme
y1 = history(x, 1);
y2 = history(x, 2);
y3 = history(x, 3);

; Weighted average of current and past samples
y = x * 0.5 + y1 * 0.3 + y2 * 0.15 + y3 * 0.05;
```

## State Patterns

### Accumulator Pattern

Track cumulative values:

```scheme
param reset_on_trigger(0);

; Accumulate input
total = 0;

if (reset_on_trigger > 0) {
  total = 0;
} else {
  total += input;
}

y = total;
```

### Ring Buffer (Circular Delay)

For longer delays, use manual indexing:

```scheme
; Create a fixed-size buffer
buffer_size = 8;
buffer_index = 0;
buffer = [0, 0, 0, 0, 0, 0, 0, 0];  ; Pseudo-code: actual implementation uses history()

; For actual delays, prefer history() instead:
delayed = history(input, 100);  ; 100-sample delay
```

### State Machine

Track different modes:

```scheme
param mode(0);        ; 0=idle, 1=recording, 2=playback
record_position = 0;
play_position = 0;

if (mode == 1) {
  ; Recording mode
  record_position += 1;
} else if (mode == 2) {
  ; Playback mode
  play_position += 1;
}
```

## Memory Constraints

### No Dynamic Allocation

Gen does not support dynamic allocation. All memory is fixed at compile time:

```scheme
; This works - fixed size known at compile time
buffer_size = 1024;

; This does NOT work - can't allocate arrays at runtime
; (Gen doesn't have array syntax for dynamic buffers)
```

**Implications:**
- Buffer sizes must be compile-time constants
- No linked lists or dynamic data structures
- Use fixed-size buffers with manual indexing

### History Buffer Size

The `history()` function stores a fixed number of past samples:

```scheme
; Each of these requires storing N samples in memory
recent_1 = history(x, 1);     ; 1 sample buffer
recent_4 = history(x, 4);     ; 4 sample buffer
recent_100 = history(x, 100); ; 100 sample buffer
```

**Memory usage:** Each history() call uses `N * sizeof(type)` bytes

## State Initialization

### Reset Behavior

Variables reset when the Gen patcher loads:

```scheme
; Fresh state on patch load
filter_state = 0;
accumulated_sum = 0;
```

### Manual Reset

Create reset logic with parameters:

```scheme
param reset(0);

// Reset on parameter change
if (reset > 0) {
  state_value = 0;
  filter_state = 0;
}

// Normal operation
filter_state = filter_state * 0.99 + input * 0.01;
y = filter_state;
```

## Side Effects and Correctness

### Preserving Correctness

Every variable assignment has side effects on future computation:

```scheme
; This is correct:
accumulator = 0;
accumulator += input;
y = accumulator;

; This is WRONG (outputs zero):
y = accumulator;
accumulator = 0;  ; Resets too late

; This is WRONG (accumulates forever):
accumulator = 0;  ; Resets every sample
accumulator += input;  ; Never accumulates
y = accumulator;
```

### Order of Operations

Variable assignments happen sequentially:

```scheme
a = 0;
b = 0;

; This stores a, then updates b
y = a;
b += 1;  ; Executed after y assignment

; Next sample: a=0 (unchanged), b=1
```

## State in Functions

### Function Parameters

Parameters are passed by value in GenExpr:

```scheme
(defun add-and-return (x y)
  ; x and y are local to this function
  ; Changes to x and y don't affect caller
  (+ x y))
```

### Function Return Values

Functions return values:

```scheme
(defun get-state ()
  state_value)  ; Returns current state

out = (get-state);  ; Read current state
```

### Accessing Global State from Functions

Functions can access global variables:

```scheme
global_state = 0;

(defun increment-state ()
  (set! global_state (+ global_state 1))
  global_state)  ; Return updated value

y = (increment-state);
```

## Performance Notes

### State Access Cost

Accessing state variables is cheap:

```scheme
; These are all O(1) operations
x = state_var;              ; Direct access
y = history(signal, 50);    ; Fixed-time lookup
z = buffer[index];          ; Direct array access
```

### Avoid Redundant State

Minimize unnecessary state:

```scheme
; GOOD: Only store what you need
feedback_state = 0;
y = input * 0.5 + feedback_state * 0.5;
feedback_state = y;

; WASTEFUL: Storing unused values
debug_value_1 = 0;  ; Never used
debug_value_2 = 0;  ; Never used
debug_value_3 = 0;  ; Never used
y = input;
```

### State Update Patterns

**Efficient pattern:**
```scheme
; Single assignment per sample
state = state * decay + input * (1 - decay);
y = state;
```

**Inefficient pattern:**
```scheme
; Multiple updates per sample
temp = state * decay;
temp = temp + input * (1 - decay);
state = temp;
y = state;
```

## Debugging State Issues

### Visualize State

Output intermediate values:

```scheme
; Outputs at different outlets to inspect state
out1 = state_a;
out2 = state_b;
out3 = final_result;
```

### Test State Reset

Verify initialization:

```scheme
param test_reset(0);

if (test_reset > 0.5) {
  state_var = 0;
  y = 0;
} else {
  state_var += input;
  y = state_var;
}
```

## Related Topics

- [Execution Model](./execution-model.md) - How Gen code runs
- [Type System](./types.md) - Variable types
- [Performance](./performance.md) - State performance implications
- [GenExpr Overview](../genexpr/overview.md) - GenExpr syntax
- [Codebox Overview](../codebox/overview.md) - Codebox syntax
