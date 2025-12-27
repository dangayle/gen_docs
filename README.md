# gen_docs

A comprehensive reference documentation site for Max/MSP typed code languages, starting with Gen (GenExpr and Codebox).

## Project Vision

This project creates machine-readable and human-friendly reference documentation for domain-specific languages used in Max/MSP 9 for audio, signal, and real-time processing. The documentation is designed to be consumable by both humans and AI/LLM agents.

### What is Gen?

Gen is a **domain-specific language (DSL)** for writing optimized audio and signal processing code in Max/MSP. It provides two syntax flavors:

- **GenExpr**: A functional, expression-oriented syntax inspired by Scheme
- **Codebox**: An imperative, C-like syntax for more traditional control flow

Both compile to highly optimized C code that runs at audio rates in real-time, making Gen ideal for performance-critical DSP tasks.

## Scope

### Phase 1: GenExpr & Codebox Reference Documentation

- **GenExpr Language Reference**: Complete syntax, operators, functions, and semantics
- **Codebox Language Reference**: Complete syntax, operators, functions, and semantics
- **Type System**: Detailed explanation of Gen's type system, type coercion, and conversions
- **Operators & Built-in Functions**: Comprehensive reference for all available operations
- **Gen-Specific Concepts**: 
  - Signal processing model and execution phases
  - Memory and state management
  - Inlets/outlets and data flow
  - Block size and sample rate semantics
  - History and delay constructs
  - Vector operations (if supported)
- **Performance & Constraints**: Real-time safety rules, execution context restrictions, and numerical characteristics
- **Code Examples**: Practical, minimal examples for each feature
- **Common Patterns**: Idiomatic usage patterns and best practices in Gen code

### Phase 2+: Extended Coverage

- Tutorials and guides (post-reference phase)
- Additional Max 9 typed code languages (jit.gen, mc.*, etc.)
- Algorithm implementations and design patterns

## Non-Goals

- Max/MSP GUI, patcher design, or object documentation
- Audio DSP theory or signal processing fundamentals
- Integration with Max objects or patching
- Max/MSP general tutorials or learning materials

## Documentation Format

All documentation is:
- **Markdown-based** for version control and portability
- **Structured hierarchically** for easy navigation and parsing
- **Machine-parseable** with consistent formatting for LLM consumption
- **Cross-referenced** with clear links between related concepts

### Reference Entry Template

Each operator, function, or language construct is documented using this consistent format for optimal LLM parsing:

```markdown
## `operator_or_function_name`

**Category:** [e.g., Arithmetic, Logic, Signal Processing, Type Conversion]

**Signature:**
\`\`\`
(operator_name arg1 arg2 ...) → return_type
\`\`\`

**Description:**
One-sentence summary. Followed by expanded explanation of what this does, when to use it, and any important context.

**Parameters:**
- **arg1** [type]: Description of parameter and constraints
- **arg2** [type]: Description of parameter and constraints

**Returns:**
[type] Description of return value and behavior

**Examples:**

GenExpr:
\`\`\`scheme
(+ 1 2)           ; → 3
(* 2.5 4)         ; → 10.0
\`\`\`

Codebox:
\`\`\`c
y = x + 5;        // Add 5 to input
\`\`\`

**Related:**
- [`related_function`](#related_function)
- [Category: Arithmetic Operators](path/to/category)

**Notes:**
- Performance characteristics if relevant (e.g., "DSP-optimized")
- Precision or type coercion behavior
- Max/MSP version requirements
- Any constraints or special behaviors

**See Also:**
- Similar operators: [`operator_a`](#operator_a), [`operator_b`](#operator_b)
- Use cases: [Filter Design](path), [Envelope Generation](path)
```

### Example: Complete Operator Documentation

```markdown
## `+` (Addition)

**Category:** Arithmetic Operators

**Signature:**
\`\`\`
(+ a b [c ...]) → number
\`\`\`

**Description:**
Adds two or more numbers and returns their sum. Works with integers and floats. In GenExpr, supports variable argument count. All operands are coerced to a common numeric type.

**Parameters:**
- **a** [int | float | signal]: First operand
- **b** [int | float | signal]: Second operand
- **c ...** [int | float | signal] *(optional)*: Additional operands

**Returns:**
[number] Sum of all arguments. Type matches the widest operand type (float if any operand is float, int otherwise).

**Examples:**

GenExpr:
\`\`\`scheme
(+ 2 3)           ; → 5
(+ 2.5 3.5)       ; → 6.0
(+ 1 2 3 4 5)     ; → 15
(+ x 0.1)         ; Add offset to signal x
\`\`\`

Codebox:
\`\`\`c
y = x + 5;        // Add constant to input signal
z = a + b + c;    // Sum multiple signals
\`\`\`

**Related:**
- [`-` (Subtraction)](#subtraction)
- [`*` (Multiplication)](#multiplication)
- [Arithmetic Operators](../operators/arithmetic)

**Notes:**
- Addition is left-associative: `(+ a b c)` = `((a + b) + c)`
- Performance: Single cycle on typical DSP hardware
- Float addition may lose precision with very large sums; consider rearranging operands
- Both GenExpr and Codebox support variable argument count

**See Also:**
- [Type Coercion Rules](../concepts/type-coercion)
- [Signals and Sample Rates](../concepts/signals)
```

## Target Audience

- **Max/MSP users**: Seeking detailed language reference
- **DSP developers**: Migrating from other languages to Gen
- **LLM agents**: Trained on this documentation to assist with Gen code generation and explanation
- **Educators**: Teaching real-time audio DSP concepts

## Usage by LLM Agents

This documentation is structured to be easily consumed by language models. Each reference entry includes:
- Clear syntax definitions
- Type signatures
- Parameter descriptions
- Return values
- Usage examples
- Related functions/operators

This enables AI assistants to:
- Generate correct Gen code
- Explain Gen syntax and semantics
- Suggest appropriate operators for tasks
- Debug and improve Gen code

## Project Structure

```
gen_docs/
├── README.md                          # This file
├── LICENSE                            # License
├── docs/
│   ├── index.md                      # Documentation home
│   ├── genexpr/
│   │   ├── overview.md               # GenExpr language overview
│   │   ├── syntax.md                 # Syntax reference
│   │   ├── types.md                  # Type system
│   │   ├── operators.md              # Operators reference
│   │   └── functions.md              # Built-in functions reference
│   ├── codebox/
│   │   ├── overview.md               # Codebox language overview
│   │   ├── syntax.md                 # Syntax reference
│   │   ├── types.md                  # Type system
│   │   ├── operators.md              # Operators reference
│   │   └── functions.md              # Built-in functions reference
│   ├── examples/                     # Code examples (organized by topic)
│   └── glossary.md                   # Terminology reference
```

## Contributing

Documentation contributions welcome. Please ensure all documentation follows the machine-readable format guidelines.

## License

See LICENSE file.

## Related Resources

- [Cycling '74 Gen Documentation](https://docs.cycling74.com/latest/vignettes/gen-overview) (official)
- Max/MSP 9 Reference Manual