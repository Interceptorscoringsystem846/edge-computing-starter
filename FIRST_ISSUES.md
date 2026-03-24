# First Issues for Edge Computing Starter

This document lists the first 5 issues/tasks for contributors to get started with.

## 🌟 Good First Issues

### Issue #1: Add Rate Limiting Middleware
**Difficulty:** Easy
**Labels:** `enhancement`, `middleware`, `good-first-issue`

**Description:**
Implement a rate limiting middleware to prevent API abuse and ensure fair usage.

**Requirements:**
- Create `RateLimiter` class using Cloudflare KV
- Support multiple strategies:
  - Fixed window (e.g., 100 requests/minute)
  - Sliding window (more accurate)
  - Token bucket (burst handling)
- Configure per route or globally
- Return appropriate headers (X-RateLimit-*)
- Handle exceeded limits gracefully

**Files to Create:**
- `src/middleware/rate-limit.ts`
- `src/lib/rate-limiter.ts`
- `tests/middleware/rate-limit.test.ts`

**Files to Modify:**
- `src/middleware/index.ts` - Export new middleware
- `README.md` - Add documentation

**Example Usage:**
```typescript
import { rateLimit } from './middleware';

// Global rate limit
app.use('*', rateLimit({
  requests: 100,
  window: 60, // seconds
  storage: 'KV',
}));

// Per-route limit
app.use('/api/expensive', rateLimit({
  requests: 10,
  window: 60,
}));
```

**Acceptance Criteria:**
- [ ] RateLimiter class implemented
- [ ] Multiple strategies supported
- [ ] Middleware created and documented
- [ ] Tests with >80% coverage
- [ ] README updated with examples
- [ ] Edge cases handled (IP spoofing, distributed systems)

---

### Issue #2: Implement Request ID Middleware
**Difficulty:** Easy
**Labels:** `enhancement`, `middleware`, `debugging`, `good-first-issue`

**Description:**
Add request ID generation and tracking for better debugging and monitoring.

**Requirements:**
- Generate unique IDs for each request
- Support custom ID generation (UUID, nanoid, etc.)
- Add ID to response headers
- Pass ID through service calls
- Optional: Add to logs for tracing
- Handle forwarded IDs from upstream services

**Files to Create:**
- `src/middleware/request-id.ts`
- `tests/middleware/request-id.test.ts`

**Files to Modify:**
- `src/middleware/index.ts`
- `src/middleware/logger.ts` - Add ID to logs
- `README.md`

**Example Usage:**
```typescript
import { requestId } from './middleware';

app.use('*', requestId());

// Later in the code
app.get('/api/data', async (c) => {
  const id = c.get('requestId');
  console.log(`Processing request ${id}`);
  // ... handler logic
});
```

**Acceptance Criteria:**
- [ ] Middleware implemented
- [ ] Configurable ID generation
- [ ] Response header set (X-Request-ID)
- [ ] Logging integration
- [ ] Tests for various scenarios
- [ ] Documentation with examples

---

### Issue #3: Create API Documentation with OpenAPI
**Difficulty:** Medium
**Labels:** `enhancement`, `documentation`, `tooling`

**Description:**
Integrate OpenAPI/Swagger documentation generation for better API discoverability.

**Requirements:**
- Set up OpenAPI schema generation
- Integrate with Hono routes
- Auto-generate from TypeScript types
- Serve Swagger UI at `/docs`
- Support for authentication documentation
- Request/response examples
- Validation from schemas

**Files to Create:**
- `src/lib/openapi.ts`
- `src/routes/docs.ts`
- `docs/openapi-config.md`

**Files to Modify:**
- `src/index.ts` - Add docs route
- `src/routes/*.ts` - Add OpenAPI decorators
- `package.json` - Add dependencies
- `README.md` - Document usage

**Dependencies:**
```bash
npm install @hono/zod-openapi
npm install -D @hono/zod-openapi-validator
```

**Example Usage:**
```typescript
import { OpenAPIHono, createRoute } from '@hono/zod-openapi';

const app = new OpenAPIHono();

const route = createRoute({
  method: 'get',
  path: '/users',
  responses: {
    200: {
      content: {
        'application/json': {
          schema: z.array(UserSchema),
        },
      },
    },
  },
});

app.openapi(route, (c) => {
  return c.json([{ id: 1, name: 'John' }]);
});
```

**Acceptance Criteria:**
- [ ] OpenAPI integration working
- [ ] Swagger UI served at `/docs`
- [ ] Schema generation from types
- [ ] At least 5 routes documented
- [ ] Authentication docs included
- [ ] README updated

---

### Issue #4: Add Health Check and Monitoring
**Difficulty:** Medium
**Labels:** `enhancement`, `monitoring`, `production`

**Description:**
Implement comprehensive health checks and monitoring endpoints.

**Requirements:**
- Create `/health` endpoint (basic)
- Create `/health/ready` endpoint (readiness probe)
- Create `/health/live` endpoint (liveness probe)
- Check dependencies:
  - Database (D1)
  - KV cache
  - R2 storage
  - External APIs
- Return detailed status with timings
- Support for graceful degradation
- Metrics endpoint (optional Prometheus format)

**Files to Create:**
- `src/routes/health.ts`
- `src/lib/health-checks.ts`
- `tests/routes/health.test.ts`

**Files to Modify:**
- `src/routes/index.ts`
- `README.md`

**Example Response:**
```json
{
  "status": "healthy",
  "timestamp": "2024-01-15T10:30:00Z",
  "checks": {
    "database": {
      "status": "healthy",
      "latency": 12
    },
    "cache": {
      "status": "healthy",
      "latency": 5
    },
    "storage": {
      "status": "degraded",
      "message": "High latency detected"
    }
  }
}
```

**Acceptance Criteria:**
- [ ] Health endpoints implemented
- [ ] Dependency checks working
- [ ] Detailed status with timings
- [ ] Error handling and fallbacks
- [ ] Comprehensive tests
- [ ] Documentation with examples

---

### Issue #5: Build Authentication Service
**Difficulty:** Medium
**Labels:** `enhancement`, `authentication`, `service`

**Description:**
Create a reusable authentication service with multiple strategies.

**Requirements:**
Implement 3 authentication methods:

1. **JWT (JSON Web Tokens)**
   - Token generation and validation
   - Refresh token support
   - Token blacklisting (with KV)
   - Claims and scopes

2. **API Keys**
   - Key generation and hashing
   - Key validation
   - Key rotation
   - Permissions/scopes

3. **OAuth2 Integration**
   - GitHub OAuth
   - Google OAuth
   - Token exchange
   - User profile fetching

**Files to Create:**
- `src/services/auth.ts`
- `src/services/jwt.ts`
- `src/services/api-keys.ts`
- `src/services/oauth.ts`
- `src/middleware/auth.ts`
- `tests/services/auth.test.ts`

**Files to Modify:**
- `src/routes/auth.ts` - Auth endpoints
- `README.md` - Auth guide
- `wrangler.example.toml` - Add env vars

**Example Usage:**
```typescript
import { auth } from './services/auth';

// Generate JWT
const token = await auth.jwt.sign({
  userId: user.id,
  email: user.email,
});

// Validate API key
const valid = await auth.apiKeys.validate(key);

// OAuth flow
const { user, token } = await auth.oauth.callback(code);
```

**Acceptance Criteria:**
- [ ] JWT service implemented
- [ ] API key service implemented
- [ ] OAuth2 service implemented
- [ ] Middleware for protecting routes
- [ ] Comprehensive test suite
- [ ] Migration guide/documentation
- [ ] Security best practices followed

## 📋 How to Contribute

1. **Choose an issue** from the list above
2. **Comment on the issue** to claim it
3. **Read CONTRIBUTING.md** for setup
4. **Create a branch**: `git checkout -b issue-X-description`
5. **Implement with tests**
6. **Submit PR** with clear description

## 💡 Tips for First-Time Contributors

- Start with "easy" labeled issues
- Ask questions in the issue
- Focus on code quality and tests
- Add plenty of comments
- Update documentation
- Keep changes focused
- Follow Cloudflare Workers best practices

## 🎓 Learning Resources

- [Cloudflare Workers Docs](https://developers.cloudflare.com/workers/)
- [Hono Framework](https://hono.dev/)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [Testing Best Practices](https://vitest.dev/guide/)

## 🤝 Need Help?

- Open a [discussion](https://github.com/groovy-web/edge-computing-starter/discussions)
- Join our [Discord](https://discord.gg/groovy-web)
- Check [Cloudflare Discord](https://discord.cloudflare.com)

---

Happy contributing! 🎉
