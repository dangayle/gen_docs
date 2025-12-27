# Codebox Examples

Practical examples demonstrating common Codebox patterns and use cases.

## Basic Examples

### Simple Passthrough

```c
y = x;
```

### Scaling Signal

```c
param scale(0.5);

y = x * scale;
```

### Adding Offset

```c
param offset(0.0);

y = x + offset;
```

## Arithmetic and Control

### Scale and Offset Combined

```c
param scale(1.0);
param offset(0.0);

y = (x * scale) + offset;
```

### Conditional Amplification

```c
param threshold(0.5);
param gain_low(0.5);
param gain_high(2.0);

if (x > threshold) {
  y = x * gain_high;
} else {
  y = x * gain_low;
}
```

### Clamping Values

```c
param min_val(-1.0);
param max_val(1.0);

if (x > max_val) {
  y = max_val;
} else if (x < min_val) {
  y = min_val;
} else {
  y = x;
}
```

### Absolute Value

```c
if (x < 0) {
  y = -x;
} else {
  y = x;
}
```

### Sign Function

```c
if (x > 0) {
  y = 1;
} else if (x < 0) {
  y = -1;
} else {
  y = 0;
}
```

## Loops

### Sum Loop

```c
sum = 0;
for (i = 0; i < 10; i += 1) {
  sum += i;
}
y = sum;
```

### Accumulation with Condition

```c
param max_sum(100);

total = 0;
for (i = 0; i < 10; i += 1) {
  if (total + i <= max_sum) {
    total += i;
  }
}
y = total;
```

### Early Exit with Break

```c
for (i = 0; i < 10; i += 1) {
  if (x < i) {
    break;  // Exit when condition met
  }
  count = i;
}
y = count;
```

### Skip with Continue

```c
for (i = 0; i < 10; i += 1) {
  if (i == 5) {
    continue;  // Skip this iteration
  }
  y += i;
}
```

## Signals and Filtering

### One-Sample Delay

```c
prev = history(x, 1);
y = prev;
```

### Difference (Highpass)

```c
prev = history(x, 1);
y = x - prev;
```

### Moving Average (3-tap)

```c
y1 = history(x, 1);
y2 = history(x, 2);
avg = (x + y1 + y2) / 3.0;
y = avg;
```

### Simple Lowpass Filter

```c
param cutoff(1000.0);
param samplerate(44100.0);

// Calculate coefficient
coeff = 2.0 * 3.14159 * cutoff / samplerate;
coeff = coeff / (1.0 + coeff);

// Apply filter with feedback
state = state + (x - state) * coeff;
y = state;
```

### First-Order IIR Filter

```c
param a(0.1);
param b(0.9);

y = a * x + b * history(y, 1);
```

### Multi-tap FIR Filter

```c
// 4-tap FIR filter
tap0 = x;
tap1 = history(x, 1);
tap2 = history(x, 2);
tap3 = history(x, 3);

y = (tap0 * 0.4) + (tap1 * 0.3) + (tap2 * 0.2) + (tap3 * 0.1);
```

## Oscillators

### Simple Sine Oscillator

```c
param frequency(440.0);
param samplerate(44100.0);
param amplitude(0.1);

// Accumulate phase
phase = phase + frequency / samplerate;

// Wrap phase (0 to 1)
if (phase > 1.0) {
  phase = phase - 1.0;
}

// Generate sine
y = sin(phase * 2.0 * 3.14159) * amplitude;
```

### Phasor (Sawtooth 0-1)

```c
param frequency(1.0);
param samplerate(44100.0);

phase = phase + frequency / samplerate;

if (phase > 1.0) {
  phase = phase - 1.0;
}

y = phase;
```

### Square Wave Oscillator

```c
param frequency(440.0);
param samplerate(44100.0);
param pulsewidth(0.5);

phase = phase + frequency / samplerate;

if (phase > 1.0) {
  phase = phase - 1.0;
}

y = (phase < pulsewidth) ? 1.0 : -1.0;
```

### Sawtooth Wave

```c
param frequency(440.0);
param samplerate(44100.0);

phase = phase + frequency / samplerate;

if (phase > 1.0) {
  phase = phase - 1.0;
}

// Convert 0-1 ramp to -1 to 1
y = (phase * 2.0) - 1.0;
```

### Triangle Wave

```c
param frequency(440.0);
param samplerate(44100.0);

phase = phase + frequency / samplerate;

if (phase > 1.0) {
  phase = phase - 1.0;
}

// Triangle shape
if (phase < 0.5) {
  y = phase * 4.0 - 1.0;  // 0-0.5 -> -1 to 1
} else {
  y = (1.0 - phase) * 4.0 - 1.0;  // 0.5-1 -> 1 to -1
}
```

### Frequency Modulated Oscillator

```c
param carrier_freq(440.0);
param mod_freq(5.0);
param mod_amount(50.0);
param samplerate(44100.0);

// Modulation LFO
mod_phase = mod_phase + mod_freq / samplerate;
if (mod_phase > 1.0) {
  mod_phase = mod_phase - 1.0;
}
modulator = sin(mod_phase * 2.0 * 3.14159) * mod_amount;

// Carrier frequency modulated
freq = carrier_freq + modulator;
phase = phase + freq / samplerate;

if (phase > 1.0) {
  phase = phase - 1.0;
}

y = sin(phase * 2.0 * 3.14159) * 0.1;
```

## Envelopes

### Simple ADSR Envelope

```c
param attack_time(0.01);
param decay_time(0.1);
param sustain_level(0.7);
param release_time(0.2);
param samplerate(44100.0);
param gate(0);

// Calculate coefficients
attack_coeff = 1.0 / (attack_time * samplerate);
decay_coeff = (sustain_level - 1.0) / (decay_time * samplerate);
release_coeff = sustain_level / (release_time * samplerate);

// State machine
if (gate > 0.5) {
  if (env < sustain_level) {
    env = env + attack_coeff;  // Attack
  } else {
    env = sustain_level;        // Sustain
  }
} else {
  // Release
  if (env > 0) {
    env = env - release_coeff;
  }
}

y = env;
```

### Exponential Attack/Release

```c
param attack_time(0.1);
param release_time(0.5);
param samplerate(44100.0);
param gate(0);

attack_coeff = pow(0.001, 1.0 / (attack_time * samplerate));
release_coeff = pow(0.001, 1.0 / (release_time * samplerate));

if (gate > 0.5) {
  env = env + (1.0 - env) * (1.0 - attack_coeff);
} else {
  env = env * release_coeff;
}

y = env * x;
```

### Linear Attack, Exponential Decay

```c
param attack_time(0.05);
param decay_time(0.3);
param sustain_level(0.5);
param samplerate(44100.0);
param gate(0);

if (gate > 0.5) {
  // Attack phase
  if (env < 1.0) {
    env = env + 1.0 / (attack_time * samplerate);
    if (env > 1.0) env = 1.0;
  }
} else {
  // Decay/release phase
  decay_coeff = pow(sustain_level, 1.0 / (decay_time * samplerate));
  env = env * decay_coeff;
}

y = env;
```

## Effects

### Tremolo (Amplitude Modulation)

```c
param speed(5.0);
param depth(0.5);
param samplerate(44100.0);

lfo_phase = lfo_phase + speed / samplerate;

if (lfo_phase > 1.0) {
  lfo_phase = lfo_phase - 1.0;
}

modulation = sin(lfo_phase * 2.0 * 3.14159);
modulation = 1.0 + modulation * depth;

y = x * modulation;
```

### Simple Distortion

```c
param drive(1.0);

driven = x * drive;

// Hard clipping
if (driven > 1.0) {
  clipped = 1.0;
} else if (driven < -1.0) {
  clipped = -1.0;
} else {
  clipped = driven;
}

y = clipped / drive;
```

### Soft Clipping with Tanh Approximation

```c
param drive(1.0);

driven = x * drive;

// Soft clip approximation
temp = abs(driven);
if (temp > 2.0) {
  clipped = (driven > 0) ? 1.0 : -1.0;
} else {
  clipped = driven / (1.0 + abs(driven));
}

y = clipped;
```

### Bitcrushing

```c
param bit_depth(12);

steps = pow(2.0, bit_depth);
quantized = floor(x * steps) / steps;

y = quantized;
```

### Simple Delay with Feedback

```c
param delay_ms(500.0);
param samplerate(44100.0);
param feedback(0.5);
param mix(0.5);

// Calculate delay in samples
delay_samples = floor((delay_ms / 1000.0) * samplerate);

// Get delayed sample
delayed = history(x, delay_samples);

// Mix with feedback
output = x + delayed * feedback;

// Mix wet and dry
y = x * (1.0 - mix) + output * mix;
```

## Analysis

### Peak Detector with Attack/Release

```c
param attack_time(0.01);
param release_time(0.1);
param samplerate(44100.0);

attack_coeff = pow(0.1, 1.0 / (attack_time * samplerate));
release_coeff = pow(0.1, 1.0 / (release_time * samplerate));

current = abs(x);

if (current > peak) {
  peak = current * attack_coeff + peak * (1.0 - attack_coeff);
} else {
  peak = peak * release_coeff + current * (1.0 - release_coeff);
}

y = peak;
```

### RMS (Root Mean Square) Energy

```c
param averaging_time(0.1);
param samplerate(44100.0);

coeff = pow(0.1, 1.0 / (averaging_time * samplerate));
squared = x * x;
mean_square = mean_square * coeff + squared * (1.0 - coeff);
rms = sqrt(mean_square);

y = rms;
```

### Zero Crossing Counter

```c
prev = history(x, 1);

// Detect zero crossing
if ((prev < 0 && x > 0) || (prev > 0 && x < 0)) {
  zero_cross_count = zero_cross_count + 1;
}

y = zero_cross_count;
```

## Multi-Channel Examples

### Mix Two Channels

```c
param mix(0.5);

y = x1 * (1.0 - mix) + x2 * mix;
```

### Sum Channels

```c
y = x1 + x2;
```

### Cross-Fade Between Channels

```c
param fade(0.0);

y1 = x1 * (1.0 - fade) + x2 * fade;
y2 = x1 * fade + x2 * (1.0 - fade);
```

### Stereo Width Control

```c
param width(1.0);

// Decode M/S (mid/side)
mid = (x1 + x2) / 2.0;
side = (x1 - x2) / 2.0;

// Adjust width
side = side * width;

// Re-encode to L/R
y1 = mid + side;
y2 = mid - side;
```

## Using Functions

### Utility Function: Clamp

```c
clamp(value, min, max) {
  if (value > max) {
    return max;
  } else if (value < min) {
    return min;
  } else {
    return value;
  }
}

y = clamp(x, -1.0, 1.0);
```

### Utility Function: Map Range

```c
map(value, in_min, in_max, out_min, out_max) {
  normalized = (value - in_min) / (in_max - in_min);
  return (normalized * (out_max - out_min)) + out_min;
}

frequency = map(midi_value, 0, 127, 20, 20000);
```

### Utility Function: Lerp

```c
lerp(a, b, t) {
  return a * (1.0 - t) + b * t;
}

interpolated = lerp(start_value, end_value, blend_amount);
```

### Filter Function

```c
lowpass(input, coefficient) {
  state = state + (input - state) * coefficient;
  return state;
}

filtered = lowpass(x, 0.1);
```

## Complete Synth Example

```c
// Simple sine synthesizer with ADSR envelope

param note(60);           // MIDI note number
param velocity(100);      // MIDI velocity (0-127)
param attack(0.01);
param decay(0.1);
param sustain(0.7);
param release(0.2);
param gate(0);

param samplerate(44100.0);

// Convert MIDI to frequency
freq = 440.0 * pow(2.0, (note - 69) / 12.0);

// Oscillator phase accumulation
phase = phase + freq / samplerate;
if (phase > 1.0) {
  phase = phase - 1.0;
}

// Generate sine
osc = sin(phase * 2.0 * 3.14159);

// ADSR envelope
attack_coeff = 1.0 / (attack * samplerate);
decay_coeff = (sustain - 1.0) / (decay * samplerate);
release_coeff = sustain / (release * samplerate);

if (gate > 0.5) {
  if (env < sustain) {
    env = env + attack_coeff;
  } else {
    env = sustain;
  }
} else {
  if (env > 0) {
    env = env - release_coeff;
  }
}

// Output with velocity scaling
y = osc * env * (velocity / 127.0) * 0.1;
```

## Related Topics

- [Codebox Syntax](./syntax.md) - Language syntax reference
- [Function Definitions](./functions.md) - Function patterns
- [Operators](../operators/index.md) - Available operators
- [Performance](../concepts/performance.md) - Optimization tips
- [Execution Model](../concepts/execution-model.md) - How code executes
