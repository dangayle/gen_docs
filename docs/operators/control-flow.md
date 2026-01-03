# Control Flow Operators

Operators for conditional execution, routing, and selection.

## Ternary Conditional

### Conditional Expression `?`

**Syntax (GenExpr & Codebox):**
```c
condition ? true_value : false_value
// or: if(condition, true_value, false_value)
```

**Description:**
Returns one of two values based on a condition. The ternary operator.

**Parameters:**
- `condition` - Boolean value (0 or non-zero)
- `true-value` - Value if condition is true (non-zero)
- `false-value` - Value if condition is false (0)

**Returns:**
- true-value or false-value depending on condition

**Examples:**

Examples (GenExpr & Codebox):
```c
// Simple gate
out = (in > threshold) ? in : 0;

// Conditional amplification
out = gate ? in * gain : 0;

// Clamp between two values
out = (x > max) ? max : (x < min) ? min : x;
```

---

## Switch and Routing

### Switch `switch`

**Syntax:**
```c
switch(selector) {
  case 0: out = value0; break;
  case 1: out = value1; break;
  case 2: out = value2; break;
}
```

**Description:**
Selects one of multiple values based on an integer selector.

**Parameters:**
- `selector` - Integer index (0, 1, 2, ...)
- `case-values` - Values for each case

**Returns:**
- Value corresponding to selector

**Examples:**

Example:
```c
switch(mode) {
  case 0: out = in * 1.0; break;   // unity
  case 1: out = in * 0.5; break;   // -6 dB
  case 2: out = in * 0.25; break;  // -12 dB
  case 3: out = 0; break;          // mute
}
```

---

## Gating and Mixing

### Gate `gate`

**Syntax (GenExpr & Codebox):**
```c
gate(input, gate_signal)
gate(input, gate_signal, fade_time)   // optional fade time in samples
```

**Description:**
Applies an amplitude gate (envelope follower) to a signal.

**Parameters:**
- `input` - Signal to gate
- `gate-signal` - Gate control (0 = off, 1 = on)
- `fade-time` - Optional fade time (prevents clicks)

**Returns:**
- Gated signal

**Examples:**

Examples:
```c
out = gate(in, gate_signal);
out = gate(in, gate_signal, 100);   // fade time in samples
```

---

### Mix `mix`

**Syntax (GenExpr & Codebox):**
```c
mix(signal1, signal2, mix_amount)
```

**Description:**
Linearly interpolates between two signals based on mix amount.

**Parameters:**
- `signal1` - First signal (when mix = 0)
- `signal2` - Second signal (when mix = 1)
- `mix-amount` - Blend factor (0 to 1)

**Returns:**
- Interpolated signal (signal1 * (1 - mix) + signal2 * mix)

**Examples:**

Examples:
```c
out = mix(dry, wet, mix_amount);
out = mix(sig1, sig2, mix_amount);
```

---

### Selector `selector`

**Syntax (GenExpr & Codebox):**
```c
selector(index, signal1, signal2, signal3, ...)
```

**Description:**
Selects one of multiple input signals based on an integer index.

**Parameters:**
- `index` - Signal selector (0, 1, 2, ...)
- `signals` - Input signals

**Returns:**
- Selected signal

**Examples:**

Examples:
```c
out = selector(input_select, in1, in2, in3, in4);

// Select oscillator shape
out = selector(osc_type,
  amp * sin(phase),
  amp * ((2 * phase) - 1),
  amp * ((phase > 0.5) ? 1 : -1)
);
```

---

## Interpolation and Smoothing

### Smooth Step `smoothstep`

**Syntax (GenExpr & Codebox):**
```c
smoothstep(min, max, x)
```

**Description:**
Hermite smooth step interpolation between min and max. Returns 0 below min, 1 above max, smooth curve in between.

**Parameters:**
- `min` - Lower threshold
- `max` - Upper threshold
- `x` - Input value

**Returns:**
- Smooth interpolation from 0 to 1

**Examples:**
```c
envelope = smoothstep(0, samplerate, x);                 // smooth envelope over 1s
soft_gate = smoothstep(threshold - 0.1, threshold + 0.1, in); // soften threshold
```

---

## Communication

### Send `send`

**Syntax (GenExpr & Codebox):**
```c
send(name, value);
```

**Description:**
Sends a value to another Gen object with the matching receive name.

**Parameters:**
- `name` - Symbol name (must match receive)
- `value` - Value to send

**Returns:**
- The value (for chaining)

**Examples:**
```c
send(lfo_freq, phase * freq_control);     // send LFO frequency
send(filter_cutoff, cutoff_param);        // send control value
```

---

### Receive `receive`

**Syntax (GenExpr & Codebox):**
```c
receive(name);
receive(name, default_value);
```

**Description:**
Receives a value from another Gen object with the matching send name.

**Parameters:**
- `name` - Symbol name (must match send)
- `default` - Optional default value if not receiving

**Returns:**
- Received value

**Examples:**
```c
lfo_freq = receive(lfo_freq, 1);       // receive with default
feedback = receive(feedback_amount, 0.5);
```

---

## Common Patterns

### Simple Conditional Gate
```c
out = (in > threshold) ? in : 0;
```

### Soft Gate with Fade
```c
out = gate(in, gate_signal, fade_samples);
```

### Wet/Dry Mix
```c
out = mix(in + feedback * feedback_amt, in, mix_amt);
```

### Oscillator Selection
```c
switch (osc_type) {
  case 0: out = amp * sin(phase * twopi); break;
  case 1: out = amp * (phase * 2 - 1); break;
  case 2: out = amp * (phase > 0.5 ? 1 : -1); break;
}
```

### Smooth Envelope Transition
```c
envelope = smoothstep(0, samplerate, x);   // fade in over 1s
```

### Inter-Object Communication
```c
// Sender object
send(freq_out, current_frequency);
send(gate_out, gate_signal);

// Receiver object
freq = receive(freq_in, 440);
gate = receive(gate_in, 0);
```

### Spectral-Safe Switch
```c
switch (osc_select) {
  case 0: osc = sin(phase); break;
  case 1: osc = phase * 2 - 1; break;
  case 2: osc = phase > 0.5 ? 1 : -1; break;
}
out = mix(osc, previous_osc, 0.01);   // small crossfade to avoid clicks
```

---

## Performance

| Function | Speed | Notes |
|----------|-------|-------|
| `?` (ternary) | ~1 cycle | Extremely fast |
| `switch` | ~2 cycles | Branch selection |
| `gate` | ~5 cycles | Envelope follower |
| `mix` | ~2 cycles | Interpolation |
| `selector` | ~2 cycles | Input selection |
| `smoothstep` | ~10 cycles | Hermite curve |
| `send`/`receive` | ~1 cycle | IPC communication |

---

## Related Topics

- [Comparison](./comparison.md) - Condition expressions (==, <, >)
- [Logic](./logic.md) - Boolean combinations
- [Math Functions](./math.md) - clamp (related to smoothstep)
