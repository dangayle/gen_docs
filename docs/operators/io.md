# Input and Output Operators

Access and manage signal flow through inlets and outlets.

## Inlets

### Inlet `in`

**Syntax:**
```scheme
; GenExpr
in
in1, in2, in3, etc.

; Codebox
in
x, y, z, etc.
```

**Description:**
Access input signals to the Gen object. Default signal names are `in` (GenExpr) or `x` (Codebox). Named inlets created by referencing them in code.

**Parameters:**
- None (use by reference)

**Returns:**
- Current inlet value

**Examples:**

GenExpr:
```scheme
; Default inlet
out = (+ in 1)

; Multiple inlets (auto-created by reference)
out = (+ in1 in2)

; Scale by second inlet
out = (* in scale-input)
```

Codebox:
```c
// Default inlet x (or y for second inlet)
y = x + 1;

// Multiple inlets
z = x + y;
z = x * scale;
```

**Notes:**
- Inlets are created automatically when referenced
- Default names: `in`, `in1`, `in2` (GenExpr) or `x`, `y`, `z` (Codebox)
- Read-only within a sample (value doesn't change during processing)

---

## Outlets

### Outlet `out`

**Syntax:**
```scheme
; GenExpr
(out value)
out = value

; Codebox
out = value;
```

**Description:**
Output a value from the Gen object. Default outlet name is `out` (GenExpr) or `y` (Codebox). Named outlets created by assigning them.

**Parameters:**
- `value` - Value to output

**Returns:**
- The assigned value

**Examples:**

GenExpr:
```scheme
; Default outlet
(out (+ in 1))

; Multiple outlets (auto-created by assignment)
(out1 filtered)
(out2 envelope)

; Or using assignment syntax
out = (+ in 1)
out1 = filtered
out2 = envelope
```

Codebox:
```c
// Default outlet
y = x + 1;

// Multiple outlets
out1 = filtered;
out2 = envelope;
```

**Notes:**
- Outlets are created automatically when assigned
- Default names: `out`, `out1`, `out2` (GenExpr) or `y`, `out1`, `out2` (Codebox)
- Each outlet is sampled once per sample period

---

## Signal Routing

### Send to Another Gen Object `send`

**Syntax:**
```scheme
; GenExpr
(send name value)

; Codebox
send(name, value)
```

**Description:**
Sends a value to another Gen object with the matching receive name. Enables inter-object communication.

**Parameters:**
- `name` - Symbol name (connect to matching receive)
- `value` - Value to send

**Returns:**
- The value (for chaining)

**Examples:**

GenExpr:
```scheme
; Send LFO output to filter
(send lfo-sig (* phase 0.5))

; Send envelope to second object
(send env-out envelope-value)
```

Codebox:
```c
// Send signals between objects
send(lfo_sig, phase * 0.5);
send(env_out, envelope_value);
```

---

### Receive from Another Gen Object `receive`

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
- `name` - Symbol name (connect to matching send)
- `default` - Optional default value if not receiving yet

**Returns:**
- Received value

**Examples:**

GenExpr:
```scheme
; Receive LFO from another object
lfo = (receive lfo-sig 0)

; Receive with default envelope
env = (receive env-in 1)
```

Codebox:
```c
// Receive from another object
lfo = receive(lfo_sig, 0);
env = receive(env_in, 1);
```

---

## Common Patterns

### Basic Signal Pass-Through
```scheme
; GenExpr
(out in)

; Codebox
y = x;
```

### Multi-Input Processing
```scheme
; GenExpr
; Two inputs, mix them
(out (+ (* in 0.5) (* in2 0.5)))

; Codebox
y = x * 0.5 + y_param * 0.5; // Note: y_param different name
```

### Multi-Output Processing
```scheme
; GenExpr
; Output both wet and dry
(out dry-signal)
(out1 wet-signal)

; Codebox
y = dry_signal;
out1 = wet_signal;
```

### Audio Analysis Output
```scheme
; GenExpr
; Output audio and analysis data
(out filtered-audio)  ; Audio output
(out1 peak-level)     ; Level meter output
(out2 frequency)      ; Frequency estimate output

; Codebox
y = filtered_audio;
out1 = peak_level;
out2 = frequency;
```

### Inter-Object LFO Sharing
```scheme
; LFO Generator Object
(send lfo-tri (- 1 (abs (- (* phase 2) 1))))
(send lfo-sin (sin (* phase twopi)))
(send lfo-sq (? (> phase 0.5) 1 -1))

; Consumer Object 1
lfo = (receive lfo-tri 0)
(out (* in (+ 0.5 lfo)))

; Consumer Object 2
lfo = (receive lfo-sin 0)
(out (* in (+ 1 lfo)))
```

### Parameter Distribution
```scheme
; Master Parameter Object
(send cutoff param1)
(send resonance param2)
(send drive param3)

; Filter Object
cutoff-freq = (receive cutoff 500)
resonance = (receive resonance 0)
(out filtered)

; Distortion Object
drive = (receive drive 0)
(out (fastpow in (+ 1 drive)))
```

### Stereo Processing
```scheme
; GenExpr
; Process left and right channels
(out left-processed)
(out1 right-processed)

; Codebox
y = left_processed;
out1 = right_processed;
```

### Parallel Processing with Mix
```scheme
; GenExpr
; Send signal to two parallel processors
(send parallel-in in)

; This object processes and mixes results
dry = in
wet = (receive parallel-out)
(out (mix dry wet mix-amount))

; Codebox
send(parallel_in, x);
wet = receive(parallel_out, 0);
y = mix(x, wet, mix_amount);
```

---

## Message Handling

Messages (control-rate changes) are automatically handled through inlets and parameters. To receive messages at control rate:

```scheme
; GenExpr
; Parameter value updates automatically
cutoff = param-inlet  ; Updates when message received

; Codebox
cutoff = param_inlet; // Updates when message received
```

---

## Performance

| Function | Speed | Notes |
|----------|-------|-------|
| `in` (inlet) | ~0 cycles | Direct reference |
| `out` (outlet) | ~0 cycles | Assignment |
| `send` | ~1 cycle | IPC communication |
| `receive` | ~1 cycle | IPC communication |

---

## Best Practices

### Naming Conventions
- Use descriptive names for `send`/`receive` pairs: `lfo-out`/`lfo-in`
- Keep inlet/outlet names concise: `cutoff`, `drive`, `mix`
- Avoid generic names that might conflict: use object type prefix (`filter-cutoff` instead of `cutoff`)

### Initialization
- Provide defaults in `receive()` calls for robustness
- Initialize state variables on first sample if needed
- Use `receive` defaults to prevent clicks/pops from undefined values

### Signal Routing
- Keep `send`/`receive` pairs in separate objects
- Name sender/receiver pairs consistently (e.g., `freq-out`/`freq-in`)
- Avoid circular dependencies (A sends to B sends to A)

---

## Related Topics

- [Concepts: Execution Model](../concepts/execution-model.md) - Inlet/outlet semantics
- [Control Flow](./control-flow.md) - send/receive in detail
- [GenExpr Overview](../genexpr/overview.md) - Parameter syntax
- [Codebox Overview](../codebox/overview.md) - Variable syntax
