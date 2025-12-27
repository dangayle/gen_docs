# Control Flow Operators

Operators for conditional execution, routing, and selection.

## Ternary Conditional

### Conditional Expression `?`

**Syntax:**
```scheme
; GenExpr - functional style
(? condition true-value false-value)
(if condition true-value false-value)

; Codebox - C-style
condition ? true_value : false_value
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

GenExpr:
```scheme
; Simple gate
(? (> in threshold) in 0)

; Select between two signals
(? (< in 0) (- in) in)

; Conditional amplification
(? gate (* in gain) 0)
```

Codebox:
```c
// Simple gate
y = x > threshold ? x : 0;

// Conditional amplification
y = gate ? x * gain : 0;

// Clamp between two values
y = (x > max) ? max : (x < min) ? min : x;
```

---

## Switch and Routing

### Switch `switch`

**Syntax:**
```scheme
; GenExpr
(switch selector case1 case2 case3 ...)

; Codebox
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

GenExpr:
```scheme
; 4-way selector
(switch mode
  (* in 1.0)        ; mode 0: unity
  (* in 0.5)        ; mode 1: -6dB
  (* in 0.25)       ; mode 2: -12dB
  0)                ; mode 3: mute
```

Codebox:
```c
switch(mode) {
  case 0: y = x * 1.0; break;
  case 1: y = x * 0.5; break;
  case 2: y = x * 0.25; break;
  case 3: y = 0; break;
}
```

---

## Gating and Mixing

### Gate `gate`

**Syntax:**
```scheme
; GenExpr
(gate input gate-signal [fade-time])

; Codebox
gate(input, gate_signal)
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

GenExpr:
```scheme
; Simple on/off gate
(gate in gate-signal)

; Soft gate (fade time in samples)
(gate in gate-signal 100)
```

Codebox:
```c
y = gate(x, gate_signal);
y = gate(x, gate_signal, 100);
```

---

### Mix `mix`

**Syntax:**
```scheme
; GenExpr
(mix signal1 signal2 mix-amount)

; Codebox
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

GenExpr:
```scheme
; Mix between dry and wet
(mix dry wet mix-amount)

; Equal-power crossfade
(mix sig1 sig2 mix-amount)
```

Codebox:
```c
y = mix(dry, wet, mix_amount);
y = mix(sig1, sig2, mix_amount);
```

---

### Selector `selector`

**Syntax:**
```scheme
; GenExpr
(selector index signal1 signal2 signal3 ...)

; Codebox
selector(index, signal1, signal2, ...)
```

**Description:**
Selects one of multiple input signals based on an integer index.

**Parameters:**
- `index` - Signal selector (0, 1, 2, ...)
- `signals` - Input signals

**Returns:**
- Selected signal

**Examples:**

GenExpr:
```scheme
; 4-input selector
(selector input-select in1 in2 in3 in4)

; Select oscillator
(selector osc-type
  (* amp (sin phase))
  (* amp (- (* 2 phase) 1))
  (* amp (? (> phase 0.5) 1 -1)))
```

Codebox:
```c
// Select from multiple sources
y = selector(input_select, in1, in2, in3, in4);
```

---

## Interpolation and Smoothing

### Smooth Step `smoothstep`

**Syntax:**
```scheme
; GenExpr
(smoothstep min max x)

; Codebox
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

GenExpr:
```scheme
; Smooth envelope from 0 to 1 ms
(smoothstep 0 samplerate x)

; Smooth threshold detection
(smoothstep (- threshold 0.1) (+ threshold 0.1) in)
```

Codebox:
```c
// Smooth transition
y = smoothstep(min, max, x);
```

---

## Communication

### Send `send`

**Syntax:**
```scheme
; GenExpr
(send name value)

; Codebox
send(name, value)
```

**Description:**
Sends a value to another Gen object with the matching receive name.

**Parameters:**
- `name` - Symbol name (must match receive)
- `value` - Value to send

**Returns:**
- The value (for chaining)

**Examples:**

GenExpr:
```scheme
; Send LFO frequency
(send lfo-freq (* phase freq-control))

; Send control value
(send filter-cutoff cutoff-param)
```

Codebox:
```c
send(lfo_freq, phase * freq_control);
send(filter_cutoff, cutoff_param);
```

---

### Receive `receive`

**Syntax:**
```scheme
; GenExpr
(receive name [default])

; Codebox
receive(name)
receive(name, default)
```

**Description:**
Receives a value from another Gen object with the matching send name.

**Parameters:**
- `name` - Symbol name (must match send)
- `default` - Optional default value if not receiving

**Returns:**
- Received value

**Examples:**

GenExpr:
```scheme
; Receive LFO frequency
lfo-freq = (receive lfo-freq 1)

; Receive with default
feedback = (receive feedback-amount 0.5)
```

Codebox:
```c
// Receive from another object
lfo_freq = receive(lfo_freq);
lfo_freq = receive(lfo_freq, 1); // Default: 1
```

---

## Common Patterns

### Simple Conditional Gate
```scheme
; GenExpr
(? (> in threshold) in 0)

; Codebox
y = x > threshold ? x : 0;
```

### Soft Gate with Fade
```scheme
; GenExpr
(gate in gate-signal fade-samples)

; Codebox
y = gate(x, gate_signal, fade_samples);
```

### Wet/Dry Mix
```scheme
; GenExpr
(mix (+ in (* feedback feedback-amt)) in mix-amt)

; Codebox
y = mix(x + feedback * feedback_amt, x, mix_amt);
```

### Oscillator Selection
```scheme
; GenExpr
(selector osc-type
  (* amp (sin (* phase twopi)))
  (* amp (- (* phase 2) 1))
  (* amp (? (> phase 0.5) 1 -1)))

; Codebox
switch(osc_type) {
  case 0: y = amp * sin(phase * twopi); break;
  case 1: y = amp * (phase * 2 - 1); break;
  case 2: y = amp * (phase > 0.5 ? 1 : -1); break;
}
```

### Smooth Envelope Transition
```scheme
; GenExpr
; Fade in over 100ms
envelope = (smoothstep 0 samplerate x)

; Codebox
envelope = smoothstep(0, samplerate, x);
```

### Inter-Object Communication
```scheme
; Sender object
(send freq-out current-frequency)
(send gate-out gate-signal)

; Receiver object
freq = (receive freq-in 440)
gate = (receive gate-in 0)
```

### Spectral-Safe Switch
```scheme
; GenExpr
; Switch oscillators without clicks
(mix
  (selector osc-select
    (sin phase)
    (- (* 2 phase) 1)
    (? (> phase 0.5) 1 -1))
  previous-osc
  0.01)

; Codebox
switch(osc_select) {
  case 0: osc = sin(phase); break;
  case 1: osc = phase * 2 - 1; break;
  case 2: osc = phase > 0.5 ? 1 : -1; break;
}
y = mix(osc, previous_osc, 0.01);
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
