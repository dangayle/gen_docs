# Signal Processing Operators

Audio DSP-specific operators for oscillation, noise, filtering, and analysis.

## History and State

### History `history`

**Syntax:**
```scheme
; GenExpr
(history x [delay-samples])
(history x 1)    ; Default: 1 sample delay

; Codebox
history(x)
history(x, samples)
```

**Description:**
Returns a previous value of x. Fundamental for creating feedback and state.

**Parameters:**
- `x` - Current value to delay
- `delay-samples` - Number of samples to delay (default 1)

**Returns:**
- Previous value of x from N samples ago

**Examples:**

GenExpr:
```scheme
; 1-sample delay (default)
delayed = (history in)

; Multi-sample delay (feedback delay line)
y0 = in;
y1 = (history y0 1);
y2 = (history y1 1);

; Simple 2-point moving average
avg = (* 0.5 (+ in (history in)))

; Difference operator (delta)
delta = (- in (history in))
```

Codebox:
```c
// 1-sample delay
delayed = history(x);

// Multi-sample delay
float y0 = in;
float y1 = history(y0, 1);
float y2 = history(y1, 1);

// Moving average
avg = (x + history(x)) * 0.5;

// Delta
delta = x - history(x);
```

**Notes:**
- Each call creates a state variable
- delay-samples must be constant (compile-time)
- Essential for filters and state machines
- delay=0 returns current value (useless)

---

### DC Block `dcblock`

**Syntax:**
```scheme
; GenExpr
(dcblock x [cutoff])
(dcblock x)       ; Default cutoff ~5 Hz

; Codebox
dcblock(x)
dcblock(x, cutoff)
```

**Description:**
High-pass filter removing DC (frequency 0) and very low frequencies.

**Parameters:**
- `x` - Input signal
- `cutoff` - Cutoff frequency in Hz (optional)

**Returns:**
- High-pass filtered signal

**Examples:**

GenExpr:
```scheme
; Remove DC from input
filtered = (dcblock in)

; Remove low frequencies below 10 Hz
filtered = (dcblock in 10)
```

Codebox:
```c
filtered = dcblock(x);
filtered = dcblock(x, 10);
```

**Notes:**
- Always include after arithmetic operations that might introduce DC
- Default ~5 Hz cutoff is suitable for audio
- Essential before peak detection or RMS analysis

---

## Oscillators

### Phasor `phasor`

**Syntax:**
```scheme
; GenExpr
(phasor frequency)

; Codebox
phasor(frequency)
```

**Description:**
Sawtooth oscillator returning values from 0 to 1. Phase wraps at 1.

**Parameters:**
- `frequency` - Oscillation frequency in Hz

**Returns:**
- Sawtooth wave (0 to ~1, wraps to 0)

**Examples:**

GenExpr:
```scheme
; Basic phasor
phase = (phasor 440)

; Convert to -1 to 1 range
wave = (- (* phase 2) 1)

; Create square wave from phasor
square = (? (< phase 0.5) 1 -1)

; Create triangle wave
triangle = (- 1 (abs (- (* phase 2) 1)))
```

Codebox:
```c
// Basic phasor
y = phasor(440);

// Square wave from phasor
if (phasor(440) < 0.5) 
  y = 1; 
else 
  y = -1;

// Triangle from phasor
y = 1 - abs(phasor(440) * 2 - 1);
```

**Notes:**
- Output range: [0, 1)
- Automatically wraps at 1 (no manual phase management)
- Useful building block for waveform generation
- Frequency in Hz (automatic sample-rate scaling)

---

## Noise

### Noise `noise`

**Syntax:**
```scheme
; GenExpr
(noise)
(noise seed)

; Codebox
noise()
noise(seed)
```

**Description:**
Returns white noise (-1 to 1 range).

**Parameters:**
- `seed` - Optional seed value (influences which noise is generated)

**Returns:**
- Random value between -1 and 1

**Examples:**

GenExpr:
```scheme
; Basic white noise
out = (noise)

; Pink noise approximation (3-point moving average)
n0 = (noise);
n1 = (history n0);
n2 = (history n1);
pink = (* 0.33 (+ n0 n1 n2))

; Noise burst
out = (* (noise) (< (random) 0.1))
```

Codebox:
```c
// White noise
y = noise();

// Pink noise approximation
float n0 = noise();
float n1 = history(n0);
float n2 = history(n1);
y = (n0 + n1 + n2) * 0.33;
```

**Notes:**
- New random value each sample
- Suitable for white noise synthesis
- For pink noise, use averaging (history + mix)
- For noise bursts, gate with conditions

---

## Coordinate Conversion

### Cartesian to Polar `cartopol`

**Syntax:**
```scheme
; GenExpr
(cartopol x y)

; Codebox
cartopol(x, y)
```

**Description:**
Converts Cartesian (x, y) to polar (magnitude, angle). Angle in radians.

**Parameters:**
- `x` - X coordinate
- `y` - Y coordinate

**Returns:**
- Two values: magnitude, angle (in radians)

**Examples:**

GenExpr:
```scheme
; Convert point to polar
(cartopol 3 4)     ; Returns: 5, atan2(4, 3)

; Rotate a point around center
(mag angle) = (cartopol x y);
angle = (+ angle rotation);
(poltocar mag angle)
```

Codebox:
```c
// Get magnitude and angle
float mag, angle;
cartopol(x, y, &mag, &angle);

// Rotate and convert back
angle += rotation;
poltocar(mag, angle, &x, &y);
```

---

### Polar to Cartesian `poltocar`

**Syntax:**
```scheme
; GenExpr
(poltocar magnitude angle)

; Codebox
poltocar(magnitude, angle)
```

**Description:**
Converts polar (magnitude, angle) to Cartesian (x, y).

**Parameters:**
- `magnitude` - Distance from origin
- `angle` - Angle in radians

**Returns:**
- Two values: x, y

**Examples:**

GenExpr:
```scheme
; Convert polar to Cartesian
(poltocar 5 0)     ; Returns: 5, 0 (point right)

; Create rotating circle
angle = (phasor 1); ; 1 Hz rotation
angle = (* angle twopi); ; Convert to radians
(x y) = (poltocar 1 angle)
```

Codebox:
```c
// Simple rotation
float angle = phasor(1) * 2 * pi;
float x, y;
poltocar(1.0, angle, &x, &y);
```

---

## Analysis Operators

### Hypot `hypot`

**Syntax:**
```scheme
; GenExpr
(hypot x y)

; Codebox
hypot(x, y)
```

**Description:**
Calculates magnitude of 2D vector. Equivalent to sqrt(x² + y²) but more numerically stable.

**Parameters:**
- `x` - X component
- `y` - Y component

**Returns:**
- Magnitude (always positive)

**Examples:**

GenExpr:
```scheme
; Magnitude of vector
(hypot x y)

; RMS of two channels
rms = (hypot left right)

; Stereo width
width = (hypot (- left right) (* 0.5 (+ left right)))
```

Codebox:
```c
// Magnitude
mag = hypot(x, y);

// RMS of stereo pair
rms = hypot(left, right) / sqrt(2);
```

**Notes:**
- More stable than sqrt(x*x + y*y) for very large or very small values
- Useful for magnitude measurements
- Essential for 2D rotations with poltocar/cartopol

---

## Performance

| Function | Speed | Notes |
|----------|-------|-------|
| `history` | ~1 cycle | State variable |
| `dcblock` | ~5 cycles | IIR high-pass filter |
| `phasor` | ~10 cycles | Automatic phase management |
| `noise` | ~20 cycles | RNG with state |
| `cartopol` | ~30 cycles | Includes atan2 |
| `poltocar` | ~30 cycles | Includes sin/cos |
| `hypot` | ~15 cycles | 2D magnitude |

---

## Common Patterns

### Simple Low-Pass Filter (1-pole IIR)
```scheme
; GenExpr
lpf-out = (+ (* 0.9 (history lpf-out)) (* 0.1 in))

; Codebox
lpf_out = 0.9 * history(lpf_out) + 0.1 * in;
```

### Simple High-Pass Filter (Differentiator)
```scheme
; GenExpr
hpf-out = (- in (history in))

; Codebox
hpf_out = x - history(x);
```

### Stereo to Mid-Side
```scheme
; GenExpr
mid = (* 0.5 (+ left right))
side = (* 0.5 (- left right))

; Codebox
mid = (left + right) * 0.5;
side = (left - right) * 0.5;
```

### Mid-Side to Stereo
```scheme
; GenExpr
left = (+ mid side)
right = (- mid side)

; Codebox
left = mid + side;
right = mid - side;
```

### Peak Detector
```scheme
; GenExpr
peak = (history peak);
peak = (? (> (abs in) peak) (abs in) (* 0.9 peak))

; Codebox
peak = history(peak);
peak = abs(x) > peak ? abs(x) : peak * 0.9;
```

### RMS Detector
```scheme
; GenExpr
; Running average of squared values
rms-sq = (+ (* 0.99 (history rms-sq)) (* 0.01 (* in in)))
rms = (sqrt rms-sq)

; Codebox
rms_sq = 0.99 * history(rms_sq) + 0.01 * (x * x);
rms = sqrt(rms_sq);
```

### Rotating Point (XY Pad)
```scheme
; GenExpr
angle = (phasor 1)        ; 1 Hz rotation
angle = (* angle twopi)
(x y) = (poltocar 1 angle)

; Codebox
angle = phasor(1) * 2 * pi;
poltocar(1.0, angle, &x, &y);
```

### Feedback Delay Network
```scheme
; GenExpr
(delay1 delay2 delay3) = (history (delay1 delay2 delay3) 441)
feedback1 = (+ (* 0.8 delay1) (* 0.2 in))
feedback2 = (+ (* 0.7 delay2) (* 0.1 delay1))
feedback3 = (+ (* 0.6 delay3) (* 0.1 delay2))

; Codebox
float d1 = history(d1, 441);
float d2 = history(d2, 441);
float d3 = history(d3, 441);

// Update for next sample
d1 = 0.8 * d1 + 0.2 * in;
d2 = 0.7 * d2 + 0.1 * d1;
d3 = 0.6 * d3 + 0.1 * d2;
```

---

## Related Topics

- [Math Functions](./math.md) - abs, clamp, scale, hypot
- [Trigonometry](./trigonometry.md) - sin, cos, atan2
- [Arithmetic](./arithmetic.md) - Basic signal mixing
- [Concepts: Memory & State](../concepts/memory-state.md) - Feedback patterns
