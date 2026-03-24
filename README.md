# Edge Computing Starter

[![Built by Groovy Web](https://img.shields.io/badge/Built%20by-Groovy%20Web-0f3460?logo=github&logoColor=white)](https://www.groovyweb.co/?utm_source=github&utm_medium=readme&utm_campaign=edge-computing)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> Production-ready template for building serverless applications with Cloudflare Workers and Hono

## 🚀 Overview

Edge Computing Starter is a comprehensive, production-ready template for building serverless applications at the edge. It combines Cloudflare Workers for global deployment with Hono for elegant TypeScript routing, providing everything you need to build fast, scalable applications.

## ✨ Features

- **Cloudflare Workers**: Deploy to 300+ locations globally
- **Hono Framework**: Ultra-fast web framework with TypeScript support
- **TypeScript**: Full type safety and excellent DX
- **Zero Config**: Works out of the box with sensible defaults
- **Best Practices**: Production-ready with error handling, logging, testing
- **CI/CD Ready**: GitHub Actions workflows included
- **Database Support**: D1, R2, and KV integration examples
- **Authentication**: Built-in auth patterns (JWT, OAuth, API keys)
- **OpenAPI**: Auto-generated API documentation
- **Testing**: Vitest for unit and integration tests

## 🏗️ Quick Start

### Using CLI (Recommended)

```bash
npm create edge-app@latest my-app
cd my-app
npm run dev
```

### Manual Clone

```bash
git clone https://github.com/groovy-web/edge-computing-starter.git my-app
cd my-app
npm install
npm run dev
```

Your app will be available at `http://localhost:8787`

## 📁 Project Structure

```
edge-computing-starter/
├── src/
│   ├── index.ts           # Entry point
│   ├── routes/            # API routes
│   │   ├── index.ts       # Route aggregation
│   │   ├── auth.ts        # Authentication routes
│   │   ├── users.ts       # Example user routes
│   │   └── health.ts      # Health check
│   ├── middleware/        # Custom middleware
│   │   ├── auth.ts        # Auth middleware
│   │   ├── error.ts       # Error handling
│   │   └── logger.ts      # Request logging
│   ├── services/          # Business logic
│   │   ├── database.ts    # DB client
│   │   └── cache.ts       # KV cache
│   ├── lib/               # Utilities
│   │   ├── env.ts         # Environment variables
│   │   └── validator.ts   # Input validation
│   └── types/             # TypeScript types
├── tests/                 # Test files
├── wrangler.toml          # Cloudflare config
├── package.json
└── tsconfig.json
```

## 🎯 Usage Examples

### Basic Routing

```typescript
import { Hono } from 'hono';

const app = new Hono();

app.get('/', (c) => {
  return c.json({ message: 'Hello from the edge!' });
});

app.get('/api/hello/:name', (c) => {
  const name = c.req.param('name');
  return c.json({ greeting: `Hello, ${name}!` });
});

export default app;
```

### Middleware

```typescript
import { cors } from 'hono/cors';
import { logger } from 'hono/logger';

app.use('*', cors());
app.use('*', logger());

// Custom auth middleware
app.use('/api/protected/*', async (c, next) => {
  const token = c.req.header('Authorization');
  if (!token) {
    return c.json({ error: 'Unauthorized' }, 401);
  }
  await next();
});
```

### Database (D1)

```typescript
import { getDB } from './services/database';

app.get('/api/users/:id', async (c) => {
  const db = getDB(c.env.DB);
  const user = await db
    .prepare('SELECT * FROM users WHERE id = ?')
    .bind(c.req.param('id'))
    .first();

  if (!user) {
    return c.json({ error: 'User not found' }, 404);
  }

  return c.json(user);
});
```

### KV Cache

```typescript
app.get('/api/data', async (c) => {
  const cache = c.env.CACHE;

  // Try to get from cache
  const cached = await cache.get('data:key', 'json');
  if (cached) {
    return c.json({ data: cached, cached: true });
  }

  // Generate fresh data
  const data = await fetchData();

  // Cache for 60 seconds
  await cache.put('data:key', JSON.stringify(data), {
    expirationTtl: 60,
  });

  return c.json({ data, cached: false });
});
```

### R2 Storage

```typescript
app.put('/api/upload', async (c) => {
  const formData = await c.req.formData();
  const file = formData.get('file') as File;

  const key = `uploads/${Date.now()}-${file.name}`;
  await c.env.R2.put(key, file.stream());

  return c.json({
    success: true,
    url: `${c.env.R2_PUBLIC_URL}/${key}`,
  });
});
```

### Validation with Zod

```typescript
import { z } from 'zod';
import { zValidator } from '@hono/zod-validator';

const userSchema = z.object({
  name: z.string().min(2),
  email: z.string().email(),
  age: z.number().min(18),
});

app.post('/api/users', zValidator('json', userSchema), async (c) => {
  const data = c.req.valid('json');
  // Save to database
  return c.json({ success: true, user: data }, 201);
});
```

## 🔧 Configuration

### Environment Variables

```bash
# Copy example env file
cp wrangler.example.toml wrangler.toml
```

Edit `wrangler.toml`:

```toml
name = "my-edge-app"
main = "src/index.ts"
compatibility_date = "2024-01-01"

[env.production]
vars = { ENVIRONMENT = "production" }

[[env.production.d1_databases]]
binding = "DB"
database_name = "my-db"
database_id = "your-database-id"

[[env.production.r2_buckets]]
binding = "R2"
bucket_name = "my-bucket"
```

### Local Development

```bash
# Start dev server
npm run dev

# Run with local D1 database
npm run dev:local

# Run tests
npm test

# Type checking
npm run type-check

# Linting
npm run lint

# Deploy to Cloudflare
npm run deploy
```

## 📚 Available Scripts

| Script | Description |
|--------|-------------|
| `dev` | Start development server |
| `dev:local` | Start with local D1 database |
| `deploy` | Deploy to Cloudflare Workers |
| `deploy:production` | Deploy to production |
| `test` | Run tests |
| `test:watch` | Watch mode for tests |
| `test:coverage` | Generate coverage report |
| `type-check` | TypeScript type checking |
| `lint` | Run ESLint |
| `lint:fix` | Fix linting issues |
| `format` | Format code with Prettier |
| `wrangler` | Run Wrangler CLI commands |

## 🧪 Testing

```typescript
import { describe, it, expect } from 'vitest';
import { app } from '../src/index';

describe('API Routes', () => {
  it('should return hello message', async () => {
    const res = await app.request('/');
    expect(res.status).toBe(200);
    const json = await res.json();
    expect(json.message).toBe('Hello from the edge!');
  });

  it('should handle protected routes', async () => {
    const res = await app.request('/api/protected/data');
    expect(res.status).toBe(401);
  });
});
```

## 🚀 Deployment

### Automatic Deployment

Push to `main` branch triggers automatic deployment via GitHub Actions.

### Manual Deployment

```bash
# Deploy to production
npm run deploy

# Deploy specific environment
npx wrangler deploy --env production
```

## 📖 Advanced Examples

### WebSockets

```typescript
app.get('/ws', upgradeWebSocket(() => ({
  onMessage(evt, ws) {
    ws.send(JSON.stringify({ echo: evt.data }));
  },
  onClose() {
    console.log('Connection closed');
  },
})));
```

### Scheduled Tasks (Cron Triggers)

```typescript
export default {
  fetch: app.fetch,
  scheduled: async (event, env, ctx) => {
    // Run daily cleanup
    await cleanupOldData(env.DB);
  },
};
```

### Email Sending (with MailChannels)

```typescript
app.post('/api/send-email', async (c) => {
  const message = {
    from: 'noreply@example.com',
    to: 'user@example.com',
    subject: 'Hello',
    content: 'Hello from Cloudflare Workers!',
  };

  await fetch('https://api.mailchannels.net/tx/v1/send', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(message),
  });

  return c.json({ success: true });
});
```

## 🔒 Authentication Patterns

### API Key

```typescript
const apiKeyMiddleware = async (c, next) => {
  const apiKey = c.req.header('X-API-Key');
  const valid = await c.env.DB
    .prepare('SELECT * FROM api_keys WHERE key = ?')
    .bind(apiKey)
    .first();

  if (!valid) {
    return c.json({ error: 'Invalid API key' }, 401);
  }
  await next();
};
```

### JWT

```typescript
import { verify } from 'jsonwebtoken';

const jwtMiddleware = async (c, next) => {
  const token = c.req.header('Authorization')?.replace('Bearer ', '');
  try {
    const decoded = verify(token, c.env.JWT_SECRET);
    c.set('user', decoded);
    await next();
  } catch (err) {
    return c.json({ error: 'Invalid token' }, 401);
  }
};
```

## 📊 Monitoring & Logging

```typescript
// Custom logger middleware
import {语境 } from 'hono';

app.use('*', async (c, next) => {
  const start = Date.now();
  await next();
  const duration = Date.now() - start;

  console.log(JSON.stringify({
    method: c.req.method,
    path: c.req.path,
    status: c.res.status,
    duration,
    timestamp: new Date().toISOString(),
  }));
});
```

## 🤝 Contributing

We welcome contributions! See [CONTRIBUTING.md](./CONTRIBUTING.md)

## 📄 License

MIT License - see [LICENSE](./LICENSE)

## 🔗 Resources

- [Cloudflare Workers Documentation](https://developers.cloudflare.com/workers/)
- [Hono Documentation](https://hono.dev/)
- [Wrangler CLI](https://developers.cloudflare.com/workers/wrangler/)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)

## ⭐ Support

If you find this helpful, please give us a star!

---

Made with ❤️ by Groovy Web
