# core-notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

A structured, interview-ready **Computer Science and Software Engineering knowledge base** covering DSA, programming languages, core CS, web development, system design, DevOps/cloud, aptitude, and HR preparation.

The goal is simple: **learn the concept → understand the trade-offs → implement it → practice interview questions → revise quickly.**

---

## 📚 What's Inside

```text
core-notes/
│
├── dsa/                    # Data Structures & Algorithms
├── languages/              # C++, Java, JavaScript, Python
├── core-cs/                # OS, DBMS, Computer Networks
├── system-design/          # HLD, LLD, OOP, Design Patterns
├── web/                    # Frontend, Backend, Node, React, Next.js, Web3, AI/LLM
├── devops/                 # Docker, Cloud, CI/CD, Kubernetes, Observability
├── aptitude/               # Quantitative, Reasoning, Verbal, Puzzles
└── interview/              # HR, Behavioural & Interview Strategy
```

---

## 🧠 DSA — Data Structures & Algorithms

| File | Focus |
|------|-------|
| [dsa/dsa.md](dsa/dsa.md) | Arrays, Strings, Linked Lists, Stacks, Queues, Trees, Graphs, Heaps, Hashing, Sorting, Searching, Greedy, DP, Backtracking, Tries, Segment Trees, Bit Manipulation |

### Language coverage

DSA examples are designed for interview preparation across different company stacks and include **C++, Python and JavaScript** implementations for major patterns.

Topics include:

- Complexity analysis
- Two pointers
- Sliding window
- Prefix sums
- Binary search
- Linked lists
- Trees and BSTs
- Graph algorithms
- Heaps / Top-K
- Greedy
- Backtracking
- Dynamic programming
- Tries
- Union-Find / DSU
- Shortest paths
- Monotonic stacks
- Bit manipulation
- Interview patterns and problem-solving strategy

---

## 💻 Programming Languages

| File | Focus |
|------|-------|
| [languages/cpp.md](languages/cpp.md) | C++ fundamentals, pointers, references, memory, OOP, templates, STL, move semantics, concurrency |
| [languages/java.md](languages/java.md) | JVM/JDK/JRE, OOP, collections, generics, exceptions, streams, concurrency, GC |
| [languages/javascript.md](languages/javascript.md) | Scope, closures, hoisting, prototypes, async/await, event loop, promises, ES6+, browser concepts |
| [languages/python.md](languages/python.md) | Python syntax, data model, collections, functions, OOP, iterators, generators, decorators, async, tooling |

---

## 🖥️ Core Computer Science

### Operating Systems

[core-cs/operating-system.md](core-cs/operating-system.md)

- Processes and threads
- CPU scheduling
- Synchronization
- Mutexes, semaphores and monitors
- Deadlocks
- Virtual memory
- Paging and segmentation
- Memory allocation
- File systems
- I/O
- IPC
- Common interview questions

### DBMS

[core-cs/dbms.md](core-cs/dbms.md)

- Relational model
- SQL
- Joins
- Aggregations
- Subqueries
- Indexing
- Transactions
- ACID
- Isolation levels
- Concurrency control
- Normalization
- Query optimization
- NoSQL
- CAP theorem
- Database interview questions

### Computer Networks

[core-cs/computer-network.md](core-cs/computer-network.md)

- OSI and TCP/IP
- TCP vs UDP
- IP addressing
- Routing
- DNS
- HTTP/HTTPS
- TLS
- Cookies and sessions
- Proxies and load balancers
- Network security
- Common troubleshooting scenarios

---

## 🏗️ System Design

| File | Focus |
|------|-------|
| [system-design/hld.md](system-design/hld.md) | Scalability, availability, reliability, load balancing, caching, databases, queues, CDNs, microservices, consistency |
| [system-design/lld.md](system-design/lld.md) | SOLID, design patterns, class design, UML, extensibility and maintainability |
| [system-design/oops.md](system-design/oops.md) | Encapsulation, abstraction, inheritance, polymorphism, composition and SOLID |

The system-design notes emphasize **requirements → estimation → architecture → bottlenecks → trade-offs → failure handling → observability**.

---

## 🌐 Web Development

| File | Focus |
|------|-------|
| [web/frontend.md](web/frontend.md) | HTML, CSS, JavaScript, React, browser rendering, performance, accessibility, bundling |
| [web/backend.md](web/backend.md) | REST, GraphQL, authentication, databases, caching, API design, Node.js and Express |
| [web/nodejs.md](web/nodejs.md) | Node.js runtime, event loop, modules, streams, buffers, HTTP, async programming, APIs and interview Q&A |
| [web/react.md](web/react.md) | Components, JSX, state, props, hooks, rendering, reconciliation, performance and interview Q&A |
| [web/nextjs.md](web/nextjs.md) | App Router, server/client components, rendering strategies, routing, data fetching, caching and deployment |
| [web/web3.md](web/web3.md) | Blockchain, Ethereum/EVM, Solana, DeFi, MEV, security, scaling, bridges and governance |
| [web/ai-llm-rag.md](web/ai-llm-rag.md) | AI/ML basics, transformers, LLMs, prompting, RAG, vector databases, agents, guardrails and LLMOps |

---

## 🐳 DevOps, Cloud & Production Engineering

The DevOps section now goes beyond Docker and cloud basics into **Kubernetes and production observability**.

| File | Focus |
|------|-------|
| [devops/docker.md](devops/docker.md) | Images, containers, Dockerfiles, networking, volumes, Compose, security and registries |
| [devops/cloudcomputing.md](devops/cloudcomputing.md) | IaaS/PaaS/SaaS, AWS/Azure/GCP, networking, IAM, storage, serverless, IaC and cost optimization |
| [devops/ci-cd.md](devops/ci-cd.md) | CI/CD pipelines, testing, artifacts, deployment strategies, rollbacks, GitOps and supply-chain security |
| [devops/kubernetes.md](devops/kubernetes.md) | Pods, Deployments, Services, Ingress, ConfigMaps, Secrets, storage, scheduling, HPA, RBAC, networking and troubleshooting |
| [devops/observability.md](devops/observability.md) | Monitoring, Prometheus, PromQL, Grafana, Alertmanager, logging, Loki, OpenTelemetry, tracing, SLI/SLO/SLA and incident response |

### Production mental model

```text
Code
  ↓
Build → Test → Package → Secure
  ↓
Deploy
  ↓
Run
  ↓
Observe
  ├── Metrics → Prometheus → Grafana
  ├── Logs → Log pipeline → Loki / log backend
  └── Traces → OpenTelemetry → Trace backend
  ↓
Alert → Investigate → Remediate → Learn
```

---

## 🎯 Aptitude

A dedicated placement and interview aptitude section:

| File | Focus |
|------|-------|
| [aptitude/quantitative.md](aptitude/quantitative.md) | Percentages, ratios, averages, profit/loss, interest, mixtures, work, speed, algebra, probability, P&C, geometry and DI |
| [aptitude/reasoning.md](aptitude/reasoning.md) | Series, coding-decoding, directions, blood relations, arrangements, syllogisms, statements, assumptions and data sufficiency |
| [aptitude/verbal.md](aptitude/verbal.md) | Grammar, vocabulary, sentence correction, para-jumbles, reading comprehension and critical reasoning |
| [aptitude/puzzles.md](aptitude/puzzles.md) | Interview brainteasers, logic puzzles, scheduling, probability, weighing, switches, lateral thinking and optimization |

---

## 🎤 Interview Preparation

[interview/hr.md](interview/hr.md)

A detailed HR and behavioural preparation guide covering:

- Tell me about yourself
- Resume walkthrough
- Why software engineering?
- Why this company?
- Why should we hire you?
- Strengths and weaknesses
- Failure and conflict questions
- Leadership and ownership
- Handling feedback
- Working under pressure
- Career goals
- Salary expectations
- Relocation / higher studies
- Project discussion
- Technical + HR crossover questions
- STAR framework
- Questions to ask interviewers
- Interview-day strategy
- Rapid-fire HR questions

Answers are structured to stay **truthful, specific and defensible in follow-up questions** rather than relying on generic memorized stories.

---

## 🧭 Recommended Learning Path

If starting from fundamentals:

```text
1. Programming Language
        ↓
2. DSA
        ↓
3. OS + DBMS + Networks
        ↓
4. OOP + LLD
        ↓
5. Web Development
        ↓
6. HLD / System Design
        ↓
7. Docker + Cloud
        ↓
8. CI/CD
        ↓
9. Kubernetes
        ↓
10. Monitoring + Logging + Observability
        ↓
11. Aptitude + HR
```

For interview revision, use the shorter loop:

```text
Concept → Example → Implementation → Trade-off → Interview Question → Revision
```

---

## 📝 What Each Note Tries to Include

The deeper notes follow a consistent structure where applicable:

- Clear definitions
- Mental models
- Architecture diagrams
- Comparison tables
- Code examples
- Complexity analysis
- Real-world use cases
- Common mistakes
- Production considerations
- Troubleshooting scenarios
- Frequently asked interview questions
- Questions with answers
- Quick revision checklists

For coding-heavy topics, examples increasingly cover **C++, Python and JavaScript** so the same algorithm can be practiced according to the company's technology stack.

---

## 🎯 Purpose

These notes are intended for:

- **Software engineering interviews**
- **Product-company interviews**
- **Startup interviews**
- **Technical screening rounds**
- **Campus / placement preparation**
- **GATE and university-level CS revision**
- **Last-minute interview revision**
- **Building strong engineering fundamentals**

The emphasis is not just on memorizing definitions, but on being able to answer:

> **What is it? Why does it work? When should I use it? What are the trade-offs? What can fail? How would I debug it?**

---

## ⭐ Core Principle

```text
Strong fundamentals
        +
Problem-solving ability
        +
Practical engineering knowledge
        +
Clear communication
        +
Interview practice
        =
Strong software-engineering interview preparation
```

---

## 🔗 Repository

urlcore-notes on GitHubhttps://github.com/nirajkr26/core-notes
