# Memory and State Management

Gen manages memory automatically; understanding state is critical for correctness and efficiency.

## Variable Storage and Scope

Variables live for the Gen object's lifetime (no local scope inside functions):
```c
my_value = 0;
accumulator = 0;
filter_state = 0;

y = my_value + input;
accumulator += y;
filter_state = (filter_state + y) * 0.9;
```

Initialization happens when the Gen object loads:
```c
initial_sum = 0;
scale = 1.0 / 127.0;
out = input * scale + initial_sum;
```

Reassignment updates state each sample:
```c
position += velocity;
out = sin(position);
```

## State Variables (Feedback)

A simple feedback register creates state:
```c
previous_output = 0;               // init
current_output = input + previous_output * 0.95;
previous_output = current_output;  // update for next sample
out = current_output;
```

### Multi-Sample History

Use `history()` for past samples:
```c
one_back = history(input, 1);
two_back = history(input, 2);
three_back = history(input, 3);
```

Example 3-tap FIR:
```c
y1 = history(x, 1);
y2 = history(x, 2);
y3 = history(x, 3);
y = x * 0.5 + y1 * 0.3 + y2 * 0.15 + y3 * 0.05;
```

## State Patterns

### Accumulator
```c
param reset_on_trigger(0);
if (reset_on_trigger > 0) {
  total = 0;
} else {
  total += input;
}
out = total;
```

### Ring Buffer (Conceptual)
```c
buffer_size = 8;           // compile-time constant
buffer_index = 0;          // pseudo; real buffers use history()
// For actual delays, prefer history():
delayed = history(input, 100);
```

### State Machine
```c
param mode(0);        // 0=idle, 1=record, 2=play
record_position = 0;
play_position = 0;

if (mode == 1) {
  record_position += 1;
} else if (mode == 2) {
  play_position += 1;
}
```

## Memory Constraints

No dynamic allocation; sizes must be compile-time constants:
```c
buffer_size = 1024;   // ok
// dynamic arrays not supported
```

`history()` stores fixed past samples:
```c
recent_1   = history(x, 1);
recent_4   = history(x, 4);
recent_100 = history(x, 100);
```

## State Initialization

Reset on load:
```c
filter_state = 0;
accumulated_sum = 0;
```

Manual reset with parameter:
```c
param reset(0);
if (reset > 0) {
  state_value = 0;
  filter_state = 0;
}
filter_state = filter_state * 0.99 + input * 0.01;
out = filter_state;
```

## Side Effects and Correctness

- Avoid unintended feedback: update state **after** computing current sample.
- Keep `history` counts constant; they must be known at compile time.
- Clamp or dcblock feedback paths to prevent runaway values when appropriate.

## Related Topics
- [Execution Model](./execution-model.md)
- [Operators](../operators/)
- [Type System](./types.md)
