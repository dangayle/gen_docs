# GenExpr Examples

Practical examples demonstrating common GenExpr patterns and use cases.

## Basic Examples

### Simple Passthrough

```scheme
; Read from inlet, write to outlet
out = in;
```

### Scaling Signal

```scheme
param scale(0.5);

out = in * scale;
```

### Adding Offset

```scheme
param offset(0.0);

out = in + offset;
```

## Arithmetic and Control

### Scale and Offset Combined

```scheme
param scale(1.0);
param offset(0.0);

out = (in * scale) + offset;
```

### Conditional Amplification

```scheme
param threshold(0.5);
param gain_low(0.5);
param gain_high(2.0);

if (in > threshold) {
  out = in * gain_high;
} else {
  out = in * gain_low;
}
```

### Clamping Values

```scheme
param min_val(-1.0);
param max_val(1.0);

clamped = max(min_val, min(in, max_val));
out = clamped;
```

### Absolute Value

```scheme
; Use abs() function
out = abs(in);
```

## Signals and Filtering

### One-Sample Delay

```scheme
prev = history(in, 1);
out = prev;  ; Output previous sample
```

### Difference (Highpass)

```scheme
prev = history(in, 1);
out = in - prev;
```

### Simple Moving Average

```scheme
avg = (in + history(in, 1) + history(in, 2) + history(in, 3)) / 4.0;
out = avg;
```

### Lowpass Filter (Single-Pole)

```scheme
param cutoff(1000.0);
param samplerate(44100.0);

; Calculate coefficient
coeff = 2.0 * pi * cutoff / samplerate;
coeff = coeff / (1.0 + coeff);

; Apply filter with feedback
state = state + (in - state) * coeff;
out = state;
```

### Highpass Filter

```scheme
param cutoff(100.0);
param samplerate(44100.0);

; Calculate coefficient
coeff = 2.0 * pi * cutoff / samplerate;
coeff = 1.0 / (1.0 + coeff);

; Highpass: difference between input and lowpass
lp_state = lp_state + (in - lp_state) * (1.0 - coeff);
out = in - lp_state;
```

## Oscillators

### Simple Sine Oscillator

```scheme
param frequency(440.0);
param samplerate(44100.0);
param amplitude(0.1);

; Accumulate phase
phase = phase + frequency / samplerate;

; Wrap phase (0 to 1)
if (phase > 1.0) {
  phase = phase - 1.0;
}

; Generate sine
out = sin(phase * 2.0 * pi) * amplitude;
```

### Phasor (Sawtooth 0-1)

```scheme
param frequency(1.0);
param samplerate(44100.0);

phase = phase + frequency / samplerate;

if (phase > 1.0) {
  phase = phase - 1.0;
}

out = phase;
```

### Square Wave Oscillator

```scheme
param frequency(440.0);
param samplerate(44100.0);
param pulsewidth(0.5);

phase = phase + frequency / samplerate;

if (phase > 1.0) {
  phase = phase - 1.0;
}

out = (phase < pulsewidth) ? 1.0 : -1.0;
```

### Modulated Oscillator

```scheme
param carrier_freq(440.0);
param modulation_freq(5.0);
param modulation_amount(50.0);
param samplerate(44100.0);

; Modulation LFO
mod_phase = mod_phase + modulation_freq / samplerate;
if (mod_phase > 1.0) {
  mod_phase = mod_phase - 1.0;
}

modulator = sin(mod_phase * 2.0 * pi) * modulation_amount;

; Carrier frequency modulated by LFO
freq = carrier_freq + modulator;
phase = phase + freq / samplerate;

if (phase > 1.0) {
  phase = phase - 1.0;
}

out = sin(phase * 2.0 * pi) * 0.1;
```

## Envelopes

### ADSR Envelope (Simple State Machine)

```scheme
param attack_time(0.01);
param decay_time(0.1);
param sustain_level(0.7);
param release_time(0.2);
param samplerate(44100.0);
param gate(0);

param mode(0);  ; 0=release, 1=attack, 2=decay, 3=sustain

; Coefficients
attack_coeff = 1.0 / (attack_time * samplerate);
decay_coeff = (sustain_level - 1.0) / (decay_time * samplerate);
release_coeff = sustain_level / (release_time * samplerate);

; State machine
if (gate > 0.5) {
  if (env < sustain_level) {
    env = env + attack_coeff;  ; Attack
  } else {
    env = sustain_level;        ; Sustain
  }
} else {
  env = max(0.0, env - release_coeff);  ; Release
}

out = env;
```

### Exponential Attack/Release

```scheme
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

out = env * in;
```

## Effects

### Tremolo (Amplitude Modulation)

```scheme
param speed(5.0);
param depth(0.5);
param samplerate(44100.0);

lfo_phase = lfo_phase + speed / samplerate;

if (lfo_phase > 1.0) {
  lfo_phase = lfo_phase - 1.0;
}

modulation = sin(lfo_phase * 2.0 * pi);
modulation = 1.0 + modulation * depth;

out = in * modulation;
```

### Simple Distortion

```scheme
param drive(1.0);
param samplerate(44100.0);

driven = in * drive;

; Soft clip using tanh approximation
if (driven > 1.0) {
  clipped = 1.0;
} else if (driven < -1.0) {
  clipped = -1.0;
} else {
  clipped = driven;
}

out = clipped / drive;
```

### Bitcrushing (Lossy Compression)

```scheme
param bit_depth(12);
param samplerate(44100.0);

; Reduce bit depth
steps = pow(2.0, bit_depth);
quantized = floor(in * steps) / steps;

out = quantized;
```

### Simple Delay

```scheme
param delay_ms(500.0);
param samplerate(44100.0);
param feedback(0.5);

; Calculate delay in samples (max 48000 samples = 1 second at 48kHz)
delay_samples = floor((delay_ms / 1000.0) * samplerate);

; Get delayed sample
delayed = history(in, delay_samples);

; Mix with feedback
out = in + delayed * feedback;
```

## Analysis

### Peak Detector

```scheme
param attack_time(0.01);
param release_time(0.1);
param samplerate(44100.0);

attack_coeff = pow(0.1, 1.0 / (attack_time * samplerate));
release_coeff = pow(0.1, 1.0 / (release_time * samplerate));

current = abs(in);

if (current > peak) {
  peak = current * attack_coeff + peak * (1.0 - attack_coeff);
} else {
  peak = peak * release_coeff + current * (1.0 - release_coeff);
}

out = peak;
```

### RMS (Root Mean Square) Detector

```scheme
param averaging_time(0.1);
param samplerate(44100.0);

coeff = pow(0.1, 1.0 / (averaging_time * samplerate));
squared = in * in;
mean_square = mean_square * coeff + squared * (1.0 - coeff);
rms = sqrt(max(0.0, mean_square));

out = rms;
```

### Zero Crossing Detector

```scheme
prev = history(in, 1);

; Detect zero crossing
crossed = (prev < 0 && in > 0) ? 1 : 0;
crossed = crossed || (prev > 0 && in < 0) ? 1 : 0;

out = crossed;
```

## Multi-Channel Examples

### Sum Two Channels

```scheme
out = in1 + in2;
```

### Mix Two Channels

```scheme
param mix(0.5);  ; 0 = all ch1, 1 = all ch2

out = in1 * (1.0 - mix) + in2 * mix;
```

### Cross-Fade

```scheme
param fade(0.0);  ; 0 to 1

out1 = in1 * (1.0 - fade) + in2 * fade;
out2 = in1 * fade + in2 * (1.0 - fade);
```

### Stereo Width Control

```scheme
param width(1.0);

; Decode M/S (mid/side)
mid = (in1 + in2) / 2.0;
side = (in1 - in2) / 2.0;

; Adjust width
side = side * width;

; Re-encode to L/R
out1 = mid + side;
out2 = mid - side;
```

## Using Functions

### Function Example: Clip

```scheme
clip(value, min, max) {
  if (value > max) {
    max
  } else if (value < min) {
    min
  } else {
    value
  }
}

out = clip(in, -1.0, 1.0);
```

### Function Example: Map Range

```scheme
map(value, in_min, in_max, out_min, out_max) {
  normalized = (value - in_min) / (in_max - in_min);
  (normalized * (out_max - out_min)) + out_min
}

; Map 0-127 MIDI to 20-20000 Hz
frequency = map(midi_value, 0, 127, 20, 20000);
```

### Function Example: Exponential Smoother

```scheme
smooth(input, time_constant) {
  coeff = pow(0.1, 1.0 / (time_constant * 44100.0));
  state = state * coeff + input * (1.0 - coeff);
  state
}

smoothed = smooth(in, 0.1);  ; 0.1 second smoothing
```

## Complete Synth Example

```scheme
; Simple sine synthesizer with ADSR envelope

param note(60);           ; MIDI note number
param velocity(100);      ; MIDI velocity (0-127)
param attack(0.01);
param decay(0.1);
param sustain(0.7);
param release(0.2);
param gate(0);

param samplerate(44100.0);

; Convert MIDI to frequency
freq = 440.0 * pow(2.0, (note - 69) / 12.0);

; Oscillator phase accumulation
phase = phase + freq / samplerate;
if (phase > 1.0) { phase = phase - 1.0; }

; Generate oscillator
osc = sin(phase * 2.0 * pi);

; ADSR envelope
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
  env = max(0.0, env - release_coeff);
}

; Output with velocity scaling
out = osc * env * (velocity / 127.0) * 0.1;
```

## Related Topics

- [GenExpr Syntax](./syntax.md) - Language syntax reference
- [Function Definitions](./functions.md) - Function patterns
- [Operators](../operators/index.md) - Available operators
- [Performance](../concepts/performance.md) - Optimization tips
- [Execution Model](../concepts/execution-model.md) - How code executes
