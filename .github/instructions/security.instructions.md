# Security Instructions

This document provides detailed security guidelines for developers working on this repository.

## Input Validation & Sanitization

- Always validate and sanitize user input before processing
- Use allowlists for acceptable input patterns when possible
- Implement strict input type checking
- Never trust client-side validation alone

## String Interpolation & Query Security

- **Never use string concatenation for SQL queries** - use parameterized queries or prepared statements
- Avoid f-strings or template literals with untrusted data
- Use ORM methods that handle escaping automatically
- Example (Python):
  ```python
  # ❌ UNSAFE
  query = f"SELECT * FROM users WHERE id = {user_id}"
  
  # ✅ SAFE
  query = "SELECT * FROM users WHERE id = %s"
  cursor.execute(query, (user_id,))
  ```

## Secrets Management

- Never hardcode API keys, passwords, or tokens in source code
- Use `.env` files (not committed to git) for local development
- Store secrets in GitHub Secrets for CI/CD workflows
- Rotate credentials regularly
- Use environment-specific configuration files

## Authentication & Authorization

- Implement strong password requirements (minimum 12 characters, complexity rules)
- Use industry-standard authentication libraries (OAuth 2.0, JWT with RS256)
- Validate authentication tokens on every protected endpoint
- Implement proper session management with expiration
- Check user permissions before granting access to resources
- Use role-based access control (RBAC) where applicable

## Data Protection

- Encrypt sensitive data at rest (database encryption, file encryption)
- Use HTTPS/TLS for all data in transit
- Never log sensitive information (passwords, tokens, API keys)
- Implement data retention policies and secure deletion
- Use secure hash algorithms (bcrypt, scrypt, Argon2) for password storage

## Dependency Security

- Keep dependencies up to date
- Regularly scan for known vulnerabilities using tools like Dependabot
- Review dependency licenses for compatibility
- Use dependency pinning in production environments
- Audit new dependencies before adding them

## Code Review Standards

- All code changes require security-focused review
- Pay special attention to authentication, authorization, and data handling code
- Use automated security scanning tools in CI/CD pipelines
- Address security warnings before merging

## Incident Response

- Report security vulnerabilities responsibly through GitHub Security Advisory
- Do not publicly disclose security issues until patches are available
- Document and learn from security incidents
