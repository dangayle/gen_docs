# Gen Documentation

Complete reference documentation for Max/MSP Gen (GenExpr and Codebox), covering language syntax, operators, and semantics.

## Quick Navigation

### Language References
- **[GenExpr](./genexpr/overview.md)** - Functional, expression-oriented language
- **[Codebox](./codebox/overview.md)** - Imperative, C-like syntax

### By Topic
- **[Operators](./operators/)** - Complete operator reference (arithmetic, logic, signal processing)
- **[Concepts](./concepts/)** - Type system, execution model, memory, state management
- **[Examples](./examples/)** - Practical code examples

## Getting Started

### New to Gen?
1. Start with [GenExpr Overview](./genexpr/overview.md) for language basics
2. Review [Type System](./concepts/types.md) to understand Gen's typing
3. Browse [Common Operators](./operators/arithmetic.md)

### Converting to Gen?
1. Review [Execution Model](./concepts/execution-model.md)
2. Understand [Type Coercion](./concepts/types.md#coercion)
3. Check [Performance & Constraints](./concepts/performance.md)

### Looking for Specific Operator?
- [Operator Index](./operators/) - Browse by category
- [Search Operators](./operators/index.md) - Searchable reference

## Max 9 Version

This documentation is for **Max 9** and covers:
- GenExpr language and syntax
- Codebox language and syntax  
- All operators (arithmetic, logic, comparison, bitwise, signal processing)
- Type system and coercion rules
- Execution phases and constraints
- State management and variables

## How to Use This Documentation

Each operator entry follows a consistent format:

```markdown
## `operator_name`

**Category:** [Category]

**Signature:**
\`\`\`
(operator_name arg1 arg2 ...) → return_type
\`\`\`

**Description:** One-sentence summary, then detailed explanation.

**Parameters:** List with types and descriptions.

**Returns:** Type and behavior description.

**Examples:** Code in both GenExpr and Codebox.

**Related:** Cross-references to similar operators.

**Notes:** Performance, constraints, edge cases.
```

This structure is optimized for both human reading and LLM training, with consistent formatting for reliable parsing.

## Documentation Structure

```
docs/
├── index.md                    # This file
├── genexpr/
│   ├── overview.md            # GenExpr language overview
│   ├── syntax.md              # Complete syntax reference
│   ├── functions.md           # Function definition and usage
│   └── examples.md            # GenExpr code examples
├── codebox/
│   ├── overview.md            # Codebox language overview
│   ├── syntax.md              # Complete syntax reference
│   ├── functions.md           # Function definition and usage
│   └── examples.md            # Codebox code examples
├── operators/
│   ├── index.md               # Operator index and search
│   ├── arithmetic.md          # Arithmetic operators
│   ├── logic.md               # Logical operators
│   ├── comparison.md          # Comparison operators
│   ├── bitwise.md             # Bitwise operators
│   ├── type-conversion.md     # Type conversion operators
│   ├── signal-processing.md   # Signal/audio operators
│   └── special.md             # Special/utility operators
└── concepts/
    ├── types.md               # Type system and coercion
    ├── execution-model.md     # Initialization and execution phases
    ├── memory-state.md        # Variables, scope, state
    ├── performance.md         # Performance characteristics and constraints
    └── glossary.md            # Terminology reference
```

## Source Material

This documentation is compiled from:
- Max 9 built-in help files
- Official Cycling '74 reference material
- GenExpr language specification

See [RESEARCH_INVENTORY.md](../RESEARCH_INVENTORY.md) for complete source listing.
