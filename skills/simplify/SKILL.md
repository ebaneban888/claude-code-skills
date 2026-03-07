---
name: simplify
description: Review changed code for reuse, quality, and efficiency, then fix any issues found. Use when the user asks to simplify code, review code quality, clean up recently modified code, or improve code clarity and maintainability.
version: 1.0.0
---

# Simplify

Simplifies and refines code for clarity, consistency, and maintainability while preserving all functionality. Focuses on recently modified code unless instructed otherwise.

## When This Skill Applies

This skill activates when the user:
- Asks to simplify or clean up code
- Wants to review recently changed code for quality
- Mentions code reuse, efficiency, or maintainability improvements
- Asks for code review focused on clarity

## Core Principles

### 1. Preserve Functionality

Never change what the code does — only how it does it. All original features, outputs, and behaviors must remain intact.

### 2. Apply Project Standards

Follow the established coding standards from CLAUDE.md including:
- Project-specific import patterns and module conventions
- Preferred function/method styles
- Type annotation conventions
- Error handling patterns
- Naming conventions

### 3. Enhance Clarity

Simplify code structure by:
- Reducing unnecessary complexity and nesting
- Eliminating redundant code and abstractions
- Improving readability through clear variable and function names
- Consolidating related logic
- Removing unnecessary comments that describe obvious code
- Avoiding nested ternary operators — prefer switch statements or if/else chains for multiple conditions
- Choosing clarity over brevity — explicit code is often better than overly compact code

### 4. Maintain Balance

Avoid over-simplification that could:
- Reduce code clarity or maintainability
- Create overly clever solutions that are hard to understand
- Combine too many concerns into single functions or components
- Remove helpful abstractions that improve code organization
- Prioritize "fewer lines" over readability (e.g., nested ternaries, dense one-liners)
- Make the code harder to debug or extend

### 5. Focus Scope

Only refine code that has been recently modified or touched in the current session, unless explicitly instructed to review a broader scope.

## Refinement Process

1. Identify the recently modified code sections
2. Analyze for opportunities to improve elegance and consistency
3. Apply project-specific best practices and coding standards
4. Ensure all functionality remains unchanged
5. Verify the refined code is simpler and more maintainable
6. Document only significant changes that affect understanding

## Examples of Improvements

- **Dead code removal**: Remove unused variables, imports, and functions
- **Simplify conditionals**: Flatten deeply nested if/else chains
- **Extract common logic**: Identify repeated patterns and consolidate
- **Improve naming**: Replace cryptic names with descriptive ones
- **Reduce complexity**: Break large functions into smaller, focused units
- **Consistent style**: Align with project conventions (spacing, quotes, semicolons)
