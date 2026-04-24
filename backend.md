# Backend Development — Super Detailed Interview & Exam Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

---

## 1. Introduction / Overview

**Backend development** refers to the server-side of a web application — everything the user does not directly see. The backend handles business logic, database operations, authentication, API serving, caching, and communication with external services.

**Core Responsibilities:**
- Serve data to frontend via APIs (REST, GraphQL, gRPC)
- Authenticate and authorize users
- Validate and process incoming data
- Interact with databases and caches
- Manage background jobs, queues, and scheduled tasks
- Ensure security, scalability, and reliability

**Common Backend Languages & Frameworks:**

| Language   | Frameworks                              | Best Known For                       |
|------------|-----------------------------------------|--------------------------------------|
| JavaScript | Node.js, Express, Fastify, NestJS       | Event-driven I/O, npm ecosystem      |
| Python     | Django, Flask, FastAPI                  | Rapid development, ML integration    |
| Java       | Spring Boot, Micronaut, Quarkus         | Enterprise, strong typing            |
| Go         | Gin, Echo, Fiber                        | High performance, concurrency        |
| Rust       | Actix-web, Axum                         | Maximum performance, memory safety   |
| Ruby       | Rails, Sinatra                          | Developer happiness, convention      |
| PHP        | Laravel, Symfony                        | Web hosting, WordPress ecosystem     |

---

## 2. HTTP & Web Fundamentals

### 2.1 HTTP Request / Response Cycle

```
[Client] --HTTP Request--> [Server]
         <--HTTP Response--

Request:
  Method: POST
  URL: https://api.example.com/users
  Headers: Content-Type: application/json, Authorization: Bearer <token>
  Body: { "name": "Alice", "email": "alice@example.com" }

Response:
  Status: 201 Created
  Headers: Content-Type: application/json
  Body: { "id": "abc123", "name": "Alice" }
```

### 2.2 HTTP Methods

| Method  | Purpose              | Idempotent | Safe | Body |
|---------|----------------------|------------|------|------|
| GET     | Retrieve resource    | Yes        | Yes  | No   |
| POST    | Create resource      | No         | No   | Yes  |
| PUT     | Replace resource     | Yes        | No   | Yes  |
| PATCH   | Partial update       | No         | No   | Yes  |
| DELETE  | Delete resource      | Yes        | No   | No   |
| HEAD    | GET without body     | Yes        | Yes  | No   |
| OPTIONS | Supported methods    | Yes        | Yes  | No   |

### 2.3 HTTP Status Codes

| Code | Meaning                  | When to Use                                      |
|------|--------------------------|--------------------------------------------------|
| 200  | OK                       | Successful GET, PUT, PATCH                       |
| 201  | Created                  | Successful POST that created a resource          |
| 204  | No Content               | Successful DELETE or action with no response body|
| 400  | Bad Request              | Invalid input / validation error                 |
| 401  | Unauthorized             | Missing or invalid authentication                |
| 403  | Forbidden                | Authenticated but not authorized                 |
| 404  | Not Found                | Resource doesn't exist                           |
| 409  | Conflict                 | Duplicate resource or version conflict           |
| 422  | Unprocessable Entity     | Semantic validation error                        |
| 429  | Too Many Requests        | Rate limit exceeded                              |
| 500  | Internal Server Error    | Unexpected server error                          |
| 502  | Bad Gateway              | Upstream service returned invalid response       |
| 503  | Service Unavailable      | Server temporarily down or overloaded            |

### 2.4 HTTP/1.1 vs HTTP/2 vs HTTP/3

| Feature              | HTTP/1.1         | HTTP/2                   | HTTP/3                       |
|----------------------|------------------|--------------------------|------------------------------|
| Protocol             | TCP              | TCP                      | QUIC (UDP-based)             |
| Multiplexing         | No (head-of-line)| Yes (streams)            | Yes (streams, no HOL block)  |
| Header Compression   | No               | HPACK                    | QPACK                        |
| Server Push          | No               | Yes                      | Yes                          |
| Connection Setup     | Slow (3-way + TLS)| Faster                  | 0-RTT reconnect              |
| Encryption           | Optional         | Practically required     | Always (built-in TLS 1.3)    |

---

## 3. RESTful API Design

### 3.1 REST Principles

- **Stateless:** Each request must contain all info needed; server stores no client context
- **Resource-based URLs:** Nouns, not verbs (`/users`, not `/getUsers`)
- **Uniform Interface:** Consistent HTTP verbs + status codes
- **Cacheable:** GET responses should be cacheable when appropriate
- **Layered System:** Client doesn't know if it speaks to proxy, cache, or origin

### 3.2 URL Design Best Practices

```
# Collections (plural nouns)
GET    /users              → list users
POST   /users              → create user

# Single resource
GET    /users/{id}         → get user
PUT    /users/{id}         → replace user
PATCH  /users/{id}         → partial update
DELETE /users/{id}         → delete user

# Nested resources (max 2 levels deep)
GET    /users/{id}/orders  → orders of a user
POST   /users/{id}/orders  → create order for user

# Filtering / Sorting / Pagination
GET    /products?category=shoes&sort=price&order=asc&page=2&limit=20

# Actions (when REST verbs don't fit)
POST   /orders/{id}/cancel
POST   /users/{id}/resend-verification
```

### 3.3 API Versioning

```
# URI versioning (most common)
/api/v1/users
/api/v2/users

# Header versioning
Accept: application/vnd.myapi.v2+json
```

### 3.4 Pagination Patterns

| Pattern         | Request                               | Response                                        |
|-----------------|---------------------------------------|-------------------------------------------------|
| Offset/Limit    | `?offset=20&limit=10`                 | `{ data: [], total: 100, offset: 20, limit: 10 }` |
| Page/Per-page   | `?page=3&per_page=10`                 | `{ data: [], page: 3, total_pages: 10 }`        |
| Cursor-based    | `?cursor=eyJ...&limit=10`             | `{ data: [], next_cursor: "eyJ..." }`           |
| Keyset          | `?after_id=500&limit=10`              | `{ data: [], last_id: 510 }`                    |

**Cursor-based is best** for real-time data (no missing/duplicate items when data changes between pages).

### 3.5 Standard Response Envelope

```json
{
  "success": true,
  "data": { "id": "1", "name": "Alice" },
  "meta": { "page": 1, "total": 100 },
  "error": null
}

// Error response
{
  "success": false,
  "data": null,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email is required",
    "details": [{ "field": "email", "message": "must not be blank" }]
  }
}
```

---

## 4. Authentication & Authorization

### 4.1 Authentication Methods

#### Session-Based Authentication
```
1. User POSTs credentials
2. Server validates, creates session in Redis/DB
3. Server sets session ID in cookie (HttpOnly, Secure, SameSite=Strict)
4. Client sends cookie on every request
5. Server looks up session on each request
```

**Pros:** Easy to invalidate; server controls session lifetime  
**Cons:** Stateful; harder to scale horizontally; CSRF risk

#### JWT (JSON Web Token)
```
Structure: base64(header).base64(payload).signature

Header:  { "alg": "HS256", "typ": "JWT" }
Payload: { "sub": "user123", "role": "admin", "exp": 1735689600, "iat": 1735603200 }
Signature: HMACSHA256(base64(header) + "." + base64(payload), secret)
```

**Access Token:** Short-lived (15 min); sent in `Authorization: Bearer <token>` header  
**Refresh Token:** Long-lived (7-30 days); stored in HttpOnly cookie; used to get new access tokens

**Token Refresh Flow:**
```
1. Access token expires → 401 Unauthorized
2. Client sends refresh token to /auth/refresh
3. Server validates refresh token from DB + cookie
4. Server issues new access token (+ optionally rotates refresh token)
5. Client retries original request
```

#### OAuth 2.0 Authorization Code Flow
```
1. User clicks "Login with Google"
2. App redirects to Google with: client_id, redirect_uri, scope, state, code_challenge (PKCE)
3. User authenticates with Google; approves scopes
4. Google redirects back with: authorization_code, state
5. App exchanges code + code_verifier for: access_token, refresh_token, id_token
6. App uses access_token to call protected APIs
```

**PKCE (Proof Key for Code Exchange):** Prevents authorization code interception attacks; required for public clients (SPA, mobile).

### 4.2 Authorization Models

| Model        | Description                                              | Example                          |
|--------------|----------------------------------------------------------|----------------------------------|
| RBAC         | Role-Based Access Control; permissions assigned to roles | Admin, Editor, Viewer roles      |
| ABAC         | Attribute-Based; decisions based on user/resource attrs  | "Manager can read docs in dept"  |
| ACL          | Access Control List per resource                         | File system permissions          |
| PBAC         | Policy-Based; centralized policy engine                  | OPA (Open Policy Agent)          |

### 4.3 Password Storage

```
# NEVER store plaintext or simple hash (MD5, SHA1)

# Correct approach — bcrypt with work factor 12+
hash = bcrypt.hash(password, saltRounds=12)

# Verification
isValid = bcrypt.compare(inputPassword, storedHash)
```

**Other modern options:** Argon2id (winner of Password Hashing Competition), scrypt.

### 4.4 Security Headers

```
Strict-Transport-Security: max-age=31536000; includeSubDomains
Content-Security-Policy: default-src 'self'; script-src 'self'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=()
```

---

## 5. Database Interaction

### 5.1 SQL Fundamentals

```sql
-- Joins
SELECT u.name, o.total
FROM users u
INNER JOIN orders o ON u.id = o.user_id
WHERE o.status = 'completed'
ORDER BY o.created_at DESC
LIMIT 10 OFFSET 20;

-- Aggregate
SELECT department, COUNT(*) as count, AVG(salary) as avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 50000;

-- Subquery
SELECT * FROM products
WHERE price > (SELECT AVG(price) FROM products);

-- Window Functions
SELECT name, salary,
  RANK() OVER (PARTITION BY department ORDER BY salary DESC) as dept_rank,
  LAG(salary) OVER (ORDER BY hire_date) as prev_salary
FROM employees;

-- CTE (Common Table Expression)
WITH high_earners AS (
  SELECT * FROM employees WHERE salary > 80000
)
SELECT department, COUNT(*) FROM high_earners GROUP BY department;

-- Upsert (PostgreSQL)
INSERT INTO users (email, name) VALUES ('a@b.com', 'Alice')
ON CONFLICT (email) DO UPDATE SET name = EXCLUDED.name;
```

### 5.2 Indexing

| Index Type         | Best For                                          |
|--------------------|---------------------------------------------------|
| B-Tree (default)   | Equality and range queries on most data types     |
| Hash               | Equality queries only; O(1) lookup                |
| Composite          | Queries filtering on multiple columns             |
| Partial            | Index subset of rows matching a condition         |
| Covering           | Index includes all columns needed by query (no table lookup) |
| Full-Text          | Text search with relevance ranking                |
| GiST / GIN         | Arrays, JSONB, geometric data (PostgreSQL)        |

**Index Best Practices:**
- Index columns in `WHERE`, `JOIN`, `ORDER BY`, `GROUP BY`
- **Composite index column order matters:** put equality-condition columns first
- Too many indexes slow down writes; use `EXPLAIN ANALYZE` to verify usage
- Monitor index bloat; `REINDEX` periodically

### 5.3 Transactions & Isolation Levels

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;  -- or ROLLBACK on error
```

**Isolation Levels (from weakest to strongest):**

| Level              | Dirty Read | Non-Repeatable Read | Phantom Read |
|--------------------|------------|---------------------|--------------|
| Read Uncommitted   | Possible   | Possible            | Possible     |
| Read Committed     | No         | Possible            | Possible     |
| Repeatable Read    | No         | No                  | Possible     |
| Serializable       | No         | No                  | No           |

### 5.4 N+1 Problem & Solutions

**Problem:** Fetching 100 users + 1 query per user for their orders = 101 queries.

**Solutions:**
- **Eager loading / JOIN:** `SELECT users.*, orders.* FROM users LEFT JOIN orders ON...`
- **Batch loading:** Collect all user IDs; `WHERE user_id IN (1, 2, 3, ...)`
- **DataLoader pattern (GraphQL):** Batch + cache within a request

### 5.5 Connection Pooling

Opening a new DB connection is expensive (~50-100ms). A **connection pool** maintains a set of reusable connections.

```
Pool config:
  min connections: 5
  max connections: 20
  idle timeout: 600s
  connection timeout: 30s
```

**Libraries:** HikariCP (Java), pgBouncer (PostgreSQL), `pg` pool (Node.js), SQLAlchemy pool (Python).

---

## 6. Node.js & Express

### 6.1 Event Loop

Node.js is **single-threaded** with a non-blocking event loop. I/O operations (DB, network, file) are offloaded to libuv's thread pool; callbacks are queued when complete.

**Event Loop Phases (in order):**
```
timers         → setTimeout, setInterval callbacks
pending I/O    → I/O callbacks deferred to next iteration
idle/prepare   → internal use
poll           → retrieve new I/O events; execute I/O callbacks
check          → setImmediate callbacks
close callbacks → e.g., socket.on('close')

process.nextTick() → runs before any phase (microtask queue)
Promise.then()     → microtask queue (after nextTick)
```

**Rule:** CPU-intensive tasks block the event loop. Offload to worker threads or separate services.

### 6.2 Express Application Structure

```javascript
// app.js
const express = require('express');
const app = express();

// Built-in middleware
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Custom middleware
app.use((req, res, next) => {
  req.requestTime = Date.now();
  next();
});

// Router
const userRouter = require('./routes/users');
app.use('/api/v1/users', userRouter);

// Error handler (4 params — must be last)
app.use((err, req, res, next) => {
  const status = err.status || 500;
  res.status(status).json({ success: false, error: { message: err.message } });
});

module.exports = app;
```

### 6.3 Middleware Pattern

Middleware functions have access to `req`, `res`, and `next`. They form a pipeline — each middleware calls `next()` to pass control forward.

```javascript
// Authentication middleware
const authenticate = async (req, res, next) => {
  try {
    const token = req.headers.authorization?.split(' ')[1];
    if (!token) return res.status(401).json({ error: 'No token' });
    req.user = await verifyJWT(token);
    next();
  } catch (err) {
    next(err);  // pass to error handler
  }
};

// Rate limiting middleware
const rateLimit = require('express-rate-limit');
const limiter = rateLimit({ windowMs: 15 * 60 * 1000, max: 100 });
app.use('/api/', limiter);
```

### 6.4 Async Error Handling

```javascript
// Wrapper to catch async errors and forward to Express error handler
const asyncHandler = (fn) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);

// Route using wrapper
router.get('/:id', asyncHandler(async (req, res) => {
  const user = await UserService.findById(req.params.id);
  if (!user) throw Object.assign(new Error('User not found'), { status: 404 });
  res.json({ success: true, data: user });
}));
```

---

## 7. NestJS

### 7.1 Architecture Overview

NestJS is an opinionated Node.js framework built on top of Express/Fastify using TypeScript and inspired by Angular.

```
Module
├── Controller  — handles HTTP requests; delegates to services
├── Service     — business logic; injectable; reusable
├── Repository  — data access layer (TypeORM, Prisma, Mongoose)
├── Guard       — authentication/authorization checks
├── Interceptor — transform requests/responses; logging, caching
├── Pipe        — validation and transformation of input
└── Filter      — exception handling; custom error responses
```

### 7.2 Core Decorators

```typescript
@Module({
  imports: [TypeOrmModule.forFeature([User])],
  controllers: [UserController],
  providers: [UserService, UserRepository],
  exports: [UserService],
})
export class UserModule {}

@Controller('users')
@UseGuards(JwtAuthGuard)
export class UserController {
  constructor(private readonly userService: UserService) {}

  @Get()
  findAll(@Query() query: FindUsersDto): Promise<User[]> {
    return this.userService.findAll(query);
  }

  @Post()
  @HttpCode(HttpStatus.CREATED)
  create(@Body() createUserDto: CreateUserDto): Promise<User> {
    return this.userService.create(createUserDto);
  }

  @Get(':id')
  findOne(@Param('id', ParseUUIDPipe) id: string): Promise<User> {
    return this.userService.findOne(id);
  }
}
```

### 7.3 Validation with class-validator

```typescript
import { IsEmail, IsString, MinLength, IsOptional, IsEnum } from 'class-validator';

export class CreateUserDto {
  @IsString()
  @MinLength(2)
  name: string;

  @IsEmail()
  email: string;

  @IsString()
  @MinLength(8)
  password: string;

  @IsOptional()
  @IsEnum(Role)
  role?: Role;
}

// main.ts — enable globally
app.useGlobalPipes(new ValidationPipe({
  whitelist: true,         // strip unknown properties
  forbidNonWhitelisted: true,
  transform: true,         // auto-convert types
}));
```

---

## 8. Database ORMs / Query Builders

### 8.1 Prisma

```typescript
// schema.prisma
model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String
  posts     Post[]
  createdAt DateTime @default(now())
}

// Usage
const user = await prisma.user.create({
  data: { email: 'alice@example.com', name: 'Alice' },
});

const usersWithPosts = await prisma.user.findMany({
  where: { email: { contains: '@gmail.com' } },
  include: { posts: { where: { published: true } } },
  orderBy: { createdAt: 'desc' },
  take: 10,
  skip: 20,
});

// Transaction
await prisma.$transaction([
  prisma.user.update({ where: { id: '1' }, data: { balance: { decrement: 100 } } }),
  prisma.user.update({ where: { id: '2' }, data: { balance: { increment: 100 } } }),
]);
```

### 8.2 TypeORM

```typescript
@Entity()
export class User {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ unique: true })
  email: string;

  @OneToMany(() => Order, order => order.user)
  orders: Order[];

  @CreateDateColumn()
  createdAt: Date;
}

// Repository pattern
const user = await userRepository.findOne({
  where: { id },
  relations: ['orders'],
});

// QueryBuilder for complex queries
const result = await userRepository
  .createQueryBuilder('user')
  .leftJoinAndSelect('user.orders', 'order')
  .where('order.status = :status', { status: 'completed' })
  .orderBy('user.name', 'ASC')
  .getMany();
```

---

## 9. Caching Strategies

### 9.1 Redis with Node.js

```javascript
const redis = require('ioredis');
const client = new redis({ host: 'localhost', port: 6379 });

// Cache-aside pattern
async function getUser(id) {
  const cached = await client.get(`user:${id}`);
  if (cached) return JSON.parse(cached);

  const user = await db.findUser(id);
  if (user) await client.setex(`user:${id}`, 3600, JSON.stringify(user)); // TTL 1hr
  return user;
}

// Invalidate on update
async function updateUser(id, data) {
  const user = await db.updateUser(id, data);
  await client.del(`user:${id}`);  // invalidate cache
  return user;
}

// Distributed lock (Redlock)
const lock = await redlock.acquire([`locks:resource:${id}`], 5000);
try {
  // critical section
} finally {
  await lock.release();
}
```

### 9.2 HTTP Caching Headers

```
Cache-Control: public, max-age=3600          # CDN + browser caches for 1 hour
Cache-Control: private, max-age=0, no-cache  # Only browser caches; must revalidate
Cache-Control: no-store                       # Never cache (sensitive data)
ETag: "abc123"                               # Validator for conditional requests
Last-Modified: Tue, 01 Jan 2025 00:00:00 GMT # Timestamp validator

# Conditional request
If-None-Match: "abc123"   → 304 Not Modified if ETag matches
If-Modified-Since: ...    → 304 Not Modified if unchanged
```

---

## 10. Background Jobs & Queues

### 10.1 Bull / BullMQ (Redis-backed)

```javascript
import { Queue, Worker } from 'bullmq';

// Producer — add jobs
const emailQueue = new Queue('email', { connection });

await emailQueue.add('welcome', {
  to: 'user@example.com',
  subject: 'Welcome!',
}, {
  attempts: 3,
  backoff: { type: 'exponential', delay: 2000 },
  removeOnComplete: 100,
  removeOnFail: 50,
});

// Consumer — process jobs
const worker = new Worker('email', async (job) => {
  await sendEmail(job.data);
}, { connection, concurrency: 5 });

worker.on('completed', job => console.log(`Job ${job.id} done`));
worker.on('failed', (job, err) => console.error(`Job ${job.id} failed`, err));
```

### 10.2 Job Types

| Type            | Description                                          | Example                          |
|-----------------|------------------------------------------------------|----------------------------------|
| Immediate       | Process as soon as possible                          | Send email after signup          |
| Delayed         | Process after a time delay                           | Send reminder in 24 hours        |
| Recurring (Cron)| Process on a schedule                                | Daily report at midnight         |
| Priority        | Higher priority jobs processed first                 | Premium user requests            |
| Batched         | Process multiple items together                      | Bulk notifications               |

---

## 11. WebSockets & Real-Time

### 11.1 WebSocket vs HTTP Polling

| Method             | Description                                       | Latency | Server Load |
|--------------------|---------------------------------------------------|---------|-------------|
| Short Polling      | Client polls every N seconds                      | High    | High        |
| Long Polling       | Client waits; server holds until data available   | Medium  | Medium      |
| Server-Sent Events | Server pushes; one-direction; auto-reconnect      | Low     | Low         |
| WebSocket          | Full-duplex; persistent TCP connection            | Lowest  | Low         |

### 11.2 Socket.IO (Node.js)

```javascript
// Server
const io = require('socket.io')(httpServer, {
  cors: { origin: 'http://localhost:3000' },
});

io.on('connection', (socket) => {
  console.log('Client connected:', socket.id);

  // Join rooms
  socket.on('join-room', (roomId) => {
    socket.join(roomId);
    socket.to(roomId).emit('user-joined', socket.id);
  });

  // Handle messages
  socket.on('send-message', ({ roomId, message }) => {
    io.to(roomId).emit('new-message', {
      from: socket.id,
      message,
      timestamp: Date.now(),
    });
  });

  socket.on('disconnect', () => {
    console.log('Client disconnected:', socket.id);
  });
});
```

---

## 12. File Uploads

### 12.1 Multipart Form Data (Express + Multer)

```javascript
const multer = require('multer');

// Memory storage (for direct S3 upload)
const upload = multer({
  storage: multer.memoryStorage(),
  limits: { fileSize: 5 * 1024 * 1024 },  // 5MB
  fileFilter: (req, file, cb) => {
    const allowed = ['image/jpeg', 'image/png', 'image/webp'];
    cb(null, allowed.includes(file.mimetype));
  },
});

router.post('/avatar', upload.single('image'), async (req, res) => {
  const { originalname, buffer, mimetype } = req.file;
  const key = `avatars/${Date.now()}-${originalname}`;

  await s3.putObject({
    Bucket: process.env.S3_BUCKET,
    Key: key,
    Body: buffer,
    ContentType: mimetype,
  });

  const url = `https://${process.env.S3_BUCKET}.s3.amazonaws.com/${key}`;
  res.json({ url });
});
```

### 12.2 Presigned URLs (S3)

```javascript
// Generate presigned URL for direct browser → S3 upload
const { getSignedUrl } = require('@aws-sdk/s3-request-presigner');

const command = new PutObjectCommand({
  Bucket: 'my-bucket',
  Key: `uploads/${uuid()}.jpg`,
  ContentType: 'image/jpeg',
});
const presignedUrl = await getSignedUrl(s3Client, command, { expiresIn: 300 });
// Return URL to client; client uploads directly to S3
```

---

## 13. Email

### 13.1 Sending with Nodemailer

```javascript
const nodemailer = require('nodemailer');

const transporter = nodemailer.createTransport({
  host: 'smtp.sendgrid.net',
  port: 587,
  auth: { user: 'apikey', pass: process.env.SENDGRID_API_KEY },
});

await transporter.sendMail({
  from: '"MyApp" <noreply@myapp.com>',
  to: user.email,
  subject: 'Verify your email',
  html: `<p>Click <a href="${verificationLink}">here</a> to verify.</p>`,
});
```

### 13.2 Email Best Practices

- Use a dedicated sending service (SendGrid, Mailgun, AWS SES)
- Always send emails asynchronously via a job queue (never block HTTP request)
- Implement **unsubscribe links** (CAN-SPAM / GDPR compliance)
- Set up **SPF, DKIM, DMARC** DNS records for deliverability
- Track bounces and complaints; remove bad addresses from your list

---

## 14. Security Best Practices

### 14.1 Input Validation & Sanitization

```javascript
// Use schema validation (Zod / Joi)
import { z } from 'zod';

const createUserSchema = z.object({
  name: z.string().min(2).max(100).trim(),
  email: z.string().email().toLowerCase(),
  age: z.number().int().min(0).max(120),
});

const parsed = createUserSchema.parse(req.body); // throws ZodError on invalid

// Sanitize HTML to prevent XSS (if storing user HTML)
import DOMPurify from 'dompurify';
const clean = DOMPurify.sanitize(userInput);
```

### 14.2 SQL Injection Prevention

```javascript
// NEVER: string concatenation
const q = `SELECT * FROM users WHERE email = '${email}'`;

// ALWAYS: parameterized queries
const user = await db.query('SELECT * FROM users WHERE email = $1', [email]);

// ORMs handle this automatically
const user = await prisma.user.findUnique({ where: { email } });
```

### 14.3 Rate Limiting

```javascript
const rateLimit = require('express-rate-limit');

// General API limit
app.use('/api/', rateLimit({ windowMs: 15 * 60 * 1000, max: 100 }));

// Stricter limit for auth endpoints
app.use('/api/auth/', rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 10,
  message: { error: 'Too many auth attempts; try again in 15 minutes' },
}));
```

### 14.4 Environment Variables & Secrets

```
# .env (never commit to git)
DATABASE_URL=postgres://user:pass@localhost:5432/mydb
JWT_SECRET=supersecretrandomstring
REDIS_URL=redis://localhost:6379

# Use a secrets manager in production:
# AWS Secrets Manager, HashiCorp Vault, GCP Secret Manager
```

### 14.5 CORS Configuration

```javascript
const cors = require('cors');

app.use(cors({
  origin: ['https://myapp.com', 'https://www.myapp.com'],
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  credentials: true,  // allow cookies
  maxAge: 86400,      // preflight cache 24h
}));
```

---

## 15. Logging & Monitoring

### 15.1 Structured Logging with Winston

```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json(),
  ),
  defaultMeta: { service: 'user-service' },
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'logs/error.log', level: 'error' }),
  ],
});

// Log with context
logger.info('User created', { userId: user.id, email: user.email });
logger.error('DB query failed', { error: err.message, stack: err.stack, query });
```

### 15.2 Request Logging Middleware

```javascript
app.use((req, res, next) => {
  const start = Date.now();
  res.on('finish', () => {
    logger.info('HTTP request', {
      method: req.method,
      url: req.originalUrl,
      status: res.statusCode,
      duration: Date.now() - start,
      ip: req.ip,
      userAgent: req.get('User-Agent'),
    });
  });
  next();
});
```

### 15.3 Health Check Endpoint

```javascript
router.get('/health', async (req, res) => {
  const checks = {
    database: 'ok',
    redis: 'ok',
    uptime: process.uptime(),
    timestamp: new Date().toISOString(),
  };

  try { await db.query('SELECT 1'); } catch { checks.database = 'error'; }
  try { await redis.ping(); }       catch { checks.redis = 'error'; }

  const allOk = Object.values(checks).every(v => v === 'ok' || typeof v !== 'string');
  res.status(allOk ? 200 : 503).json(checks);
});
```

---

## 16. Testing

### 16.1 Testing Pyramid

```
         /\
        /  \  ← E2E Tests (few; slow; expensive)
       /    \
      /──────\  ← Integration Tests (moderate)
     /        \
    /──────────\  ← Unit Tests (many; fast; cheap)
```

### 16.2 Unit Testing with Jest

```javascript
// userService.test.js
describe('UserService', () => {
  let userService;
  let mockRepo;

  beforeEach(() => {
    mockRepo = {
      findById: jest.fn(),
      save: jest.fn(),
    };
    userService = new UserService(mockRepo);
  });

  test('findUser returns user when found', async () => {
    const mockUser = { id: '1', name: 'Alice' };
    mockRepo.findById.mockResolvedValue(mockUser);

    const result = await userService.findUser('1');

    expect(result).toEqual(mockUser);
    expect(mockRepo.findById).toHaveBeenCalledWith('1');
  });

  test('findUser throws 404 when not found', async () => {
    mockRepo.findById.mockResolvedValue(null);
    await expect(userService.findUser('999')).rejects.toThrow('User not found');
  });
});
```

### 16.3 Integration Testing with Supertest

```javascript
const request = require('supertest');
const app = require('../app');

describe('POST /api/users', () => {
  it('creates a user and returns 201', async () => {
    const res = await request(app)
      .post('/api/users')
      .send({ name: 'Alice', email: 'alice@example.com', password: 'Secret123!' })
      .expect(201);

    expect(res.body.data).toMatchObject({ name: 'Alice', email: 'alice@example.com' });
    expect(res.body.data).not.toHaveProperty('password');
  });

  it('returns 400 for invalid email', async () => {
    const res = await request(app)
      .post('/api/users')
      .send({ name: 'Alice', email: 'not-an-email' })
      .expect(400);

    expect(res.body.error.code).toBe('VALIDATION_ERROR');
  });
});
```

---

## 17. Deployment & Production Checklist

### 17.1 Environment Configuration

- All secrets in environment variables / secrets manager (never in code)
- Different configs for development, staging, production
- Feature flags for gradual rollouts
- Graceful shutdown handler for zero-downtime deploys

```javascript
// Graceful shutdown
process.on('SIGTERM', async () => {
  console.log('SIGTERM received, shutting down gracefully...');
  server.close(async () => {
    await db.end();     // close DB connections
    await redis.quit(); // close Redis connections
    process.exit(0);
  });
  setTimeout(() => process.exit(1), 30000); // force quit after 30s
});
```

### 17.2 Production Best Practices

- Run Node.js with **PM2** or in containers (Docker + Kubernetes)
- Set `NODE_ENV=production` (disables stack traces in responses, enables optimizations)
- Enable **gzip/brotli compression** (reduces response size 60-90%)
- Set sensible **timeouts** on all outgoing HTTP requests
- Use **circuit breakers** for downstream services
- Implement **idempotency keys** for payment and critical mutation APIs
- Enable **HTTPS only**; redirect HTTP → HTTPS
- Rotate secrets and tokens regularly

---

## 18. Quick Reference — Backend Interview Topics

| Topic                  | Key Points                                                         |
|------------------------|--------------------------------------------------------------------|
| REST vs GraphQL vs gRPC| REST: simple; GraphQL: flexible queries; gRPC: binary, streaming  |
| JWT vs Sessions        | JWT: stateless; Sessions: easy invalidation                        |
| SQL vs NoSQL           | SQL: ACID, relations; NoSQL: scale, flexibility                    |
| Caching                | Cache-aside, write-through, TTL, eviction (LRU/LFU)               |
| Rate Limiting          | Token bucket, sliding window; per IP / per user                    |
| Background Jobs        | BullMQ + Redis; retry, backoff, DLQ                                |
| WebSockets             | Persistent connection; Socket.IO; scale with Redis adapter         |
| Indexing               | B-Tree, composite, covering; EXPLAIN ANALYZE                       |
| N+1 Problem            | Eager load / batch load / DataLoader                               |
| Security               | HTTPS, helmet, CORS, rate limit, parameterized queries, bcrypt     |
