# Input and Output Operators

Access and manage signal flow through inlets and outlets.

## Inlets

### Inlet `in`

**Syntax (GenExpr & Codebox):**
```c
in;            // default inlet
in1, in2, in3; // additional inlets created by reference
```

**Description:**
Access input signals to the Gen object. Default signal names are `in` (GenExpr) or `x` (Codebox). Named inlets created by referencing them in code.

**Parameters:**
- None (use by reference)

**Returns:**
- Current inlet value

**Examples:**
```c
out = in + 1;                // default inlet
out = in1 + in2;             // auto-creates in2
scaled = in * scale_amount;  // scale by another inlet/param
```

**Notes:**
- Inlets are created automatically when referenced
- Default names: `in`, `in1`, `in2` (GenExpr) or `x`, `y`, `z` (Codebox)
- Read-only within a sample (value doesn't change during processing)

---

## Outlets

### Outlet `out`

**Syntax (GenExpr & Codebox):**
```c
out = value;
out1 = value1; // additional outlets created by assignment
```

**Description:**
Output a value from the Gen object. Default outlet name is `out` (GenExpr) or `y` (Codebox). Named outlets created by assigning them.

**Parameters:**
- `value` - Value to output

**Returns:**
- The assigned value

**Examples:**
```c
out = in + 1;        // default outlet
out1 = filtered;     // additional outlet auto-creates out1
out2 = envelope;     // more outlets as needed
```

**Notes:**
- Outlets are created automatically when assigned
- Default names: `out`, `out1`, `out2` (GenExpr) or `y`, `out1`, `out2` (Codebox)
- Each outlet is sampled once per sample period

---

## Signal Routing

### Send to Another Gen Object `send`

**Syntax (GenExpr & Codebox):**
```c
send(name, value);
```

**Description:**
Sends a value to another Gen object with the matching receive name. Enables inter-object communication.

**Parameters:**
- `name` - Symbol name (connect to matching receive)
- `value` - Value to send

**Returns:**
- The value (for chaining)

**Examples:**
```c
send(lfo_sig, phase * 0.5);          // LFO output to filter
send(env_out, envelope_value);       // Envelope to another gen object
```

---

### Receive from Another Gen Object `receive`

**Syntax (GenExpr & Codebox):**
```c
receive(name);
receive(name, default_value);
```

**Description:**
Receives a value from another Gen object with the matching send name.

**Parameters:**
- `name` - Symbol name (connect to matching send)
- `default` - Optional default value if not receiving yet

**Returns:**
- Received value

**Examples:**
```c
lfo = receive(lfo_sig, 0);   // Receive LFO with default
env = receive(env_in, 1);    // Receive envelope with default
```

---

## Common Patterns

### Basic Signal Pass-Through
```c
out = in;
```

### Multi-Input Processing
```c
out = (in * 0.5) + (in2 * 0.5);   // mix two inlets equally
```

### Multi-Output Processing
```c
out = dry_signal;
out1 = wet_signal;
```

### Audio Analysis Output
```c
out = filtered_audio;   // Audio output
out1 = peak_level;      // Level meter output
out2 = frequency;       // Frequency estimate output
```

### Inter-Object LFO Sharing
```c
// LFO Generator Object
send(lfo_tri, 1 - abs((phase * 2) - 1));
send(lfo_sin, sin(phase * twopi));
send(lfo_sq, (phase > 0.5) ? 1 : -1);

// Consumer Object 1
lfo = receive(lfo_tri, 0);
out = in * (0.5 + lfo);

// Consumer Object 2
lfo = receive(lfo_sin, 0);
out = in * (1 + lfo);
```

### Parameter Distribution
```c
// Master Parameter Object
send(cutoff, param1);
send(resonance, param2);
send(drive, param3);

// Filter Object
cutoff_freq = receive(cutoff, 500);
resonance = receive(resonance, 0);
out = filtered;

// Distortion Object
drive = receive(drive, 0);
out = fastpow(in, 1 + drive);
```

### Stereo Processing
```c
out = left_processed;
out1 = right_processed;
```

### Parallel Processing with Mix
```c
send(parallel_in, in);              // send to parallel processors
wet = receive(parallel_out, 0);     // receive processed path
out = mix(in, wet, mix_amount);     // blend dry/wet
```

---

## Message Handling

Messages (control-rate changes) are automatically handled through inlets and parameters. To receive messages at control rate:

```c
cutoff = param_inlet;  // updates when a message arrives
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
