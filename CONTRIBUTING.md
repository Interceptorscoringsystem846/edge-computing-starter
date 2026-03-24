# Contributing to Edge Computing Starter

Thank you for your interest in contributing! This starter template is used by many developers, so quality and stability are important.

## 🚀 Getting Started

### Prerequisites

- Node.js 18+ and npm
- Cloudflare account (free tier works)
- Wrangler CLI installed
- Git

### Development Setup

1. Fork and clone the repository
2. Install dependencies:

```bash
npm install
```

3. Set up Wrangler:

```bash
npx wrangler login
```

4. Create a feature branch:

```bash
git checkout -b feature/your-feature-name
```

## 🏗️ Architecture Overview

The template follows this architecture:

```
User Request
    ↓
Middleware (CORS, Auth, Logging)
    ↓
Router (Hono)
    ↓
Controller/Handler
    ↓
Service (Business Logic)
    ↓
Database/Cache (D1, KV, R2)
```

## 📝 Contribution Guidelines

### Types of Contributions

We welcome:

- **Bug Fixes**: Issues with the template
- **New Features**: Additional middleware, services, examples
- **Documentation**: Improved guides and comments
- **Performance**: Optimizations and benchmarks
- **Tests**: Increased test coverage

### Code Style

- Use TypeScript for all new code
- Follow existing formatting (Prettier)
- Use functional patterns
- Handle errors gracefully
- Add JSDoc comments

```typescript
/**
 * Validates user input and creates a new user
 * @param data - User creation data
 * @param env - Cloudflare environment
 * @returns Created user record
 * @throws {ValidationError} If validation fails
 */
export async function createUser(
  data: CreateUserInput,
  env: Env
): Promise<User> {
  // Implementation
}
```

### Adding New Features

1. **Plan the feature**:
   - Open an issue to discuss
   - Get feedback from maintainers
   - Consider breaking changes

2. **Implement with tests**:
   ```bash
   # Create feature branch
   git checkout -b feature/new-auth-method

   # Write tests first
   npm test -- --watch

   # Implement feature
   # Add documentation
   ```

3. **Test thoroughly**:
   - Unit tests for functions
   - Integration tests for flows
   - Manual testing with Wrangler

4. **Update documentation**:
   - Add to README
   - Update CHANGELOG.md
   - Add examples

### Adding New Middleware

Create middleware in `src/middleware/`:

```typescript
import type { MiddlewareHandler } from 'hono';

export const myMiddleware: MiddlewareHandler = async (c, next) => {
  // Before handler
  const start = Date.now();

  await next();

  // After handler
  const duration = Date.now() - start;
  c.header('X-Response-Time', `${duration}ms`);
};
```

Add to `src/middleware/index.ts`:

```typescript
export * from './my-middleware';
```

Update README with usage example.

### Adding New Examples

Add practical examples to `docs/examples/`:

```typescript
// docs/examples/feature-name.md
# Feature Name

## Overview
Brief description

## Usage
\`\`\`typescript
import { feature } from '@edge-computing-starter/feature';

const result = await feature(options);
\`\`\`

## Use Cases
- Use case 1
- Use case 2
```

## 🧪 Testing

### Test Structure

```typescript
import { describe, it, expect, beforeEach } from 'vitest';
import { app } from '../src/index';

describe('Feature Name', () => {
  beforeEach(async () => {
    // Setup test data
  });

  it('should handle successful requests', async () => {
    const res = await app.request('/api/endpoint', {
      method: 'POST',
      body: JSON.stringify({ test: 'data' }),
    });

    expect(res.status).toBe(200);
    const json = await res.json();
    expect(json).toMatchObject({ success: true });
  });

  it('should handle errors gracefully', async () => {
    const res = await app.request('/api/endpoint');
    expect(res.status).toBe(400);
  });
});
```

### Running Tests

```bash
# Run all tests
npm test

# Watch mode
npm run test:watch

# Coverage
npm run test:coverage

# Specific test file
npm test -- auth.test.ts
```

## 📤 Submitting Changes

### Commit Messages

Use conventional commits:

```
feat(auth): add OAuth2 support

- Implement OAuth2 flow
- Add token refresh logic
- Include tests and examples

Closes #123
```

### Pull Request Process

1. **Ensure all tests pass**: `npm test`
2. **Run linting**: `npm run lint`
3. **Check types**: `npm run type-check`
4. **Update documentation**
5. **Submit PR** with:

```markdown
## Description
Brief description of changes

## Type
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation

## Changes
- List of changes

## Testing
- [ ] Tests added/updated
- [ ] All tests passing
- [ ] Manual testing completed

## Documentation
- [ ] README updated
- [ ] Examples added
- [ ] Types documented

## Breaking Changes
If applicable, describe breaking changes and migration path.

## Checklist
- [ ] Code follows style guide
- [ ] Self-review completed
- [ ] No new warnings
- [ ] Backward compatible
```

## 🔒 Security Issues

For security vulnerabilities:

1. **Do NOT open a public issue**
2. Email: hello@groovyweb.co
3. Include details and reproduction steps
4. We'll respond within 48 hours

## 🐛 Reporting Bugs

### Bug Report Template

```markdown
## Bug Description
Clear description of the issue

## Reproduction Steps
1. Step one
2. Step two
3. See error

## Expected Behavior
What should happen

## Actual Behavior
What actually happens

## Environment
- Node version:
- Cloudflare Workers:
- Wrangler version:
- OS:

## Code Sample
\`\`\`typescript
// Code that reproduces issue
\`\`\`

## Error Message
\`\`\`
Paste error here
\`\`\`
```

## 💡 Contribution Ideas

Looking to contribute? Here are some ideas:

- Add more authentication providers (Auth0, Firebase)
- Create monitoring/analytics integration
- Build admin dashboard template
- Add WebSocket examples
- Create rate limiting middleware
- Build caching strategies
- Add GraphQL support (with Yoga)
- Create gRPC integration
- Add more database examples
- Build file upload handling
- Create email service examples
- Add webhooks handling
- Build job queue system
- Create internationalization (i18n) support

## 📜 Code of Conduct

- Be respectful and inclusive
- Provide constructive feedback
- Focus on what is best for the community
- Show empathy towards other community members

## 📄 License

By contributing, you agree that your contributions will be licensed under the MIT License.

## 🆘 Getting Help

- Read existing documentation
- Search existing issues
- Join our Discord community
- Open a discussion

## 🎯 Priority Areas

We're prioritizing:

1. **Stability**: Bug fixes and reliability
2. **Performance**: Optimization and profiling
3. **Documentation**: Examples and guides
4. **Testing**: Increased coverage
5. **Developer Experience**: Better tooling

---

Thank you for contributing to Edge Computing Starter!
