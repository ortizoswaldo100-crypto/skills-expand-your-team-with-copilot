# Copilot Instructions

This file contains guidelines for GitHub Copilot to follow when generating code for this repository.

## Security Guidelines

- **Focus on security and avoid unsafe string interpolation.** Always use parameterized queries, prepared statements, or safe string formatting methods instead of string concatenation or f-strings with untrusted input.
- **Validate all user input** before processing it. Implement input sanitization and validation for all external data sources.
- **Use environment variables** for sensitive configuration like API keys, database credentials, and tokens. Never hardcode secrets in the code.
- **Implement proper authentication and authorization** checks before accessing protected resources or endpoints.
- **Use HTTPS** for all external API calls and network requests.
- **Avoid using eval() or similar dynamic code execution** functions with user-supplied data.

## Code Quality Guidelines

- **Ensure functions have docstrings** explaining parameters and return types. This includes:
  - A brief description of what the function does
  - A list of parameters with their types and descriptions
  - A description of the return value and its type
  - Any exceptions that may be raised

Example (Python):
```python
def calculate_total(items: list, tax_rate: float) -> float:
    """
    Calculate the total cost of items including tax.
    
    Args:
        items (list): List of item prices as floats
        tax_rate (float): Tax rate as a decimal (e.g., 0.08 for 8%)
    
    Returns:
        float: Total cost including tax
    """
    pass
```

Example (JavaScript):
```javascript
/**
 * Calculate the total cost of items including tax.
 *
 * @param {number[]} items - Array of item prices
 * @param {number} taxRate - Tax rate as a decimal (e.g., 0.08 for 8%)
 * @returns {number} Total cost including tax
 */
function calculateTotal(items, taxRate) {
  // implementation
}
```

- **Write clear and meaningful variable names** that describe their purpose.
- **Keep functions small and focused** on a single responsibility.
- **Add comments** for complex logic or non-obvious implementation details.
- **Follow the existing code style** and conventions used in the repository.
- **Write unit tests** for new functionality with clear test descriptions.

## Performance Guidelines

- **Avoid unnecessary loops and redundant operations**.
- **Use appropriate data structures** for the use case (e.g., sets for membership checks, dictionaries for lookups).
- **Minimize database queries** by batching operations where possible.

## Accessibility Guidelines

- **Ensure code is accessible** to users with disabilities by following WCAG guidelines when applicable.
- **Use semantic HTML** when generating HTML content.
- **Provide meaningful alt text** for images and other visual content.
