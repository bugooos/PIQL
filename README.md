# Piql Workbench v0.2.0

**Professional IDE for Packed Query Language (Piql)**

Piql is a structured, human-readable query protocol designed for type-safe, bidirectional data communication. This repository contains the Piql runtime engine, validator, planner, and an Electron-based IDE.

---

## 📋 Table of Contents

- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Architecture Overview](#architecture-overview)
- [How to Run](#how-to-run)
- [IDE Features & Buttons](#ide-features--buttons)
- [Query Language Guide](#query-language-guide)
- [Example Queries](#example-queries)
- [Development](#development)
- [Troubleshooting](#troubleshooting)

---

## ✨ Features

### Core Runtime
- **Parser & Lexer**: Tokenizes and parses Piql queries into a typed AST
- **Validator**: Schema-aware semantic validation with rich error messages
- **Planner**: Query cost estimation and execution strategy selection
- **Executor**: Fast, secure, observable query execution engine
- **Security**: Built-in rate limiting, audit logging, and field-level authorization
- **Observability**: Query tracing, latency recording, execution graphs

### IDE (Electron App)
- **Real-time Syntax Highlighting**: Piql-aware code editor with custom themes
- **Query Execution**: Run queries against in-memory databases with live results
- **Multiple Result Views**:
  - **Response**: Raw JSON data
  - **Table**: Tabular data view
  - **Tree**: Hierarchical data explorer
  - **Query Info**: Cost, latency, warnings
  - **Warnings**: Deduplicated warnings panel with badge
- **Schema Browser**: Explore types, fields, relationships
- **AI-Powered Tools**:
  - Natural Language → Query translation
  - Query optimization suggestions
  - Auto-complete & snippet insertion
  - Debug assistant for error analysis
- **Tools & Utilities**:
  - Query benchmarking (latency analysis)
  - API documentation generator
  - Schema diffing and analytics
  - Relationship graph visualization
  - Field usage analytics
- **Menu System**:
  - **File**: New tab, open/save queries
  - **Query**: Execute, format, explain, show AST/plan/diff
  - **AI**: Debug assistant, NL→query, optimize, schema explainer
  - **Schema**: Browser, diff, graph, ERD, field analytics, API docs
  - **Security**: Depth check, audit log, permission map
  - **Team**: Team/workspace management, orchestration
  - **Tools**: Benchmark, SDK generator, webhook manager, observability

### Storage & Persistence
- JSON file-based persistence of databases and schemas
- In-memory execution with optional caching
- History tracking and snapshot management

### SDK & Integration
- Multi-language client code generation (JavaScript, Python, Go, etc.)
- Webhook management and event streaming
- OpenAPI specification generation
- Plugin architecture for extensibility

---

## 📦 Prerequisites

### Minimum Requirements
- **Python**: 3.10 or newer
- **Node.js**: 18 or newer
- **Electron**: 36+
- **Bash**: For launch script (on Linux/macOS)

### Optional Enhancements
- **msgpack**: For faster binary serialization (install: `pip install msgpack`)
- **pytest**: For running tests (install: `pip install pytest`)

### System Support
- ✅ **Linux** (Ubuntu 20.04+, Fedora 38+, Debian 12+)
- ✅ **macOS** (Big Sur 11+)
- ✅ **Windows** (10/11 with WSL2 recommended)

---

## 🚀 Installation

### 1. Clone Repository
```bash
git clone https://github.com/bugooos/PIQL.git
cd PIQL
```

### 2. Install Python Dependencies (Optional)
```bash
# For testing (optional)
pip install pytest pytest-cov

# For faster serialization (optional)
pip install msgpack
```

### 3. Install Node.js Dependencies
```bash
npm install
```

### 4. Verify Setup
```bash
# Check Python version
python3 --version  # Should be 3.10+

# Check Node.js version
node --version  # Should be 18+

# Verify Electron is installed
./node_modules/.bin/electron --version
```

---

## ⚡ Quick Start

### Launch the IDE
```bash
npm start
```

This will:
1. Start the Python runtime server (stdio mode)
2. Launch the Electron IDE
3. Display the Piql Workbench interface

### First Query
In the editor, paste this query and press **Ctrl+Enter**:

```piql
? users { id name email createdAt }
```

You'll see results in the **Response** tab.

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                     IDE Layer (Electron)                    │
│  ┌────────────────────────────────────────────────────────┐ │
│  │ Frontend (index.html, CSS, JS)                         │ │
│  │ - Editor, result tabs, menu bar, sidebar              │ │
│  │ - Syntax highlighting, UI state management            │ │
│  └────────────────────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────────────────────┐ │
│  │ Electron Shell (main.js)                              │ │
│  │ - Window management, menu, app lifecycle              │ │
│  └────────────────────────────────────────────────────────┘ │
└────────────────────┬──────────────────────────────────────┘
                     │ IPC Bridge (ipc/bridge.js)
                     │ Only whitelisted function calls
                     ▼
┌──────────────────────────────────────────────────────────────┐
│              Runtime Server (Python)                         │
├──────────────────────────────────────────────────────────────┤
│  Runtime Entry (server_entry.py) — Function Registry        │
│     │                                                        │
│     ├─► execute()           ──► Executor.execute()          │
│     ├─► query_autocomplete()                                │
│     ├─► nl_to_nexql()       ──► AI Helpers                  │
│     ├─► optimize_query()                                    │
│     ├─► validate()          ──► Validator                   │
│     ├─► infer_schema()      ──► SchemaRegistry              │
│     ├─► build_schema_graph()                                │
│     └─► benchmark()         ──► Executor (multiple runs)    │
│                                                              │
│  ┌──────────┬──────────┬──────────┬──────────┬──────────┐   │
│  │ Parser   │ Validator│ Planner  │ Executor │ Storage  │   │
│  │          │          │          │          │          │   │
│  │ Lexer    │ Semantic │ Optimize │ CRUD Ops │ JSON    │   │
│  │ Parser   │ Rules    │ Cost Est │ Auth     │ Cache   │   │
│  │ AST      │ Warnings │ Strategy │ Security │ History │   │
│  │          │          │ Trace    │ Observ.  │         │   │
│  └──────────┴──────────┴──────────┴──────────┴──────────┘   │
│                                                              │
│  ┌────────────────┬──────────────┬──────────────────────┐   │
│  │    Schema      │   Security   │  Observability       │   │
│  │                │              │                      │   │
│  │ Registry       │ RateLimiter  │ LatencyRecorder      │   │
│  │ Type System    │ AuditLog     │ Tracer               │   │
│  │ Inference      │ AuthProvider │ Execution Graph      │   │
│  │ Relationships  │ Depth Check  │ Query Recorder       │   │
│  └────────────────┴──────────────┴──────────────────────┘   │
└──────────────────────────────────────────────────────────────┘
                     │
                     ▼
         ┌─────────────────────────┐
         │   Executable Database   │
         │   (JSON collections)    │
         └─────────────────────────┘
```

### Query Pipeline

```
Query String
    │
    ├─► Lexer      → List[Token]
    │
    ├─► Parser     → QueryDocument | ParseError
    │
    ├─► Validator  → ValidationResult (with schema awareness)
    │
    ├─► Planner    → ExecutionPlan (cost, strategy)
    │
    ├─► Executor   → ExecutionResult (data + metadata)
    │
    └─► Serializer → JSON-ready dict
```

---

## 🎯 How to Run

### Option 1: Full IDE (Recommended)
```bash
npm start
```

### Option 2: Runtime Server Only (HTTP)
```bash
npm run start:http
# Server listens at http://127.0.0.1:7890
```

### Option 3: Runtime Server (Machine Readable via stdio)
```bash
npm run start:runtime
# Use for programmatic access via pipes
```

### Option 4: CLI Utilities
```bash
# Parse a query and show AST
npm run start -- --parse "? users { id }"

# Tokenize a query
npm run start -- --tokens "? users { id }"

# Validate a query against schema
npm run start -- --validate "? users { id }"
```

---

## 🎨 IDE Features & Buttons

### 1. **Top Toolbar**

| Button | Action | Shortcut |
|--------|--------|----------|
| **Run** | Execute query | Ctrl+Enter |
| **Format** | Auto-format query | (Menu) |
| **Explain** | Show query explanation | ⚡ Explain |
| **Read/Create/Update/Delete** | Insert templates | Buttons |
| **Clear** | Clear editor | ✕ Clear |

### 2. **Query Menu**
- **Execute Query** (Ctrl+Return) — Run current query
- **Format Query** (Ctrl+Shift+F) — Auto-format and beautify
- **Explain Query** — Show query explanation & cost
- **Clear Editor** — Empty the editor
- **Show AST** — Display abstract syntax tree
- **Show Plan** — Display execution plan
- **Show Diff** — Show differences from previous query

### 3. **AI Menu**
- **Debug Assistant** — Analyze and fix errors
- **Natural Language → Query** — Convert English to Piql
- **Optimize Query** — Suggest performance improvements
- **Schema Explainer** — Explain schema structure

### 4. **Schema Menu**
- **Schema Browser** — Explore types and fields
- **Schema Diff** — Compare old vs new schemas
- **Relationship Graph** — Visualize entity relationships
- **Entity Relationship Diagram** — ERD visualization
- **Field Usage Analytics** — Usage patterns and stats
- **API Docs** — Generate OpenAPI documentation

### 5. **Security Menu**
- **Query Depth Check** — Verify nesting limits
- **Audit Log** — View query execution history
- **Permission Map** — See field-level access rules

### 6. **Team Menu**
- **Create Team** — Team management
- **Create Workspace** — Workspace isolation
- **Team Analytics** — Usage statistics
- **Orchestration** — Multi-server coordination

### 7. **Tools Menu**
- **Benchmark** — Performance testing (latency analysis, p95/p99)
- **SDK Generator** — Generate client code (JS, Python, Go, etc.)
- **Webhook Manager** — Manage event subscriptions
- **Latency Statistics** — Query performance metrics
- **Execution Graph** — Visualize operation flow

### 8. **Result Tabs**
- **Response** — Raw JSON results
- **Table** — Tabular view with sorting and columns
- **Tree** — Hierarchical data explorer
- **Query Info** — Execution metadata (cost, time, etc.)
- **Warnings** — Deduplicated warnings with count badge

---

## 📖 Query Language Guide

### Syntax Overview

**Format**: `METHOD target (filter) { projection } @directive`

### Methods

| Method | Operator | Purpose | Example |
|--------|----------|---------|---------|
| Read | `?` | Fetch records | `? users { id name }` |
| Create | `+` | Insert record | `+ post { title "Hello" }` |
| Update | `~` | Modify record | `~ user (id u_1) { name "Bob" }` |
| Delete | `!` | Remove record | `! post (id p_1)` |
| Subscribe | `>>` | Live updates | `>> messages { id body }` |
| Publish | `<<` | Send event | `<< alert { type "warning" }` |

### Filters (Optional)

**Syntax**: `(key operator value ...)`

```piql
? users (
  role admin           # Exact match
  age > 18             # Comparison
  active true          # Boolean
  $limit 10            # Pagination
  $offset 20           # Skip N records
  $sort createdAt desc # Sort descending
)
```

### Projection (Field Selection)

**Syntax**: `{ field1 field2 nested { subfield } }`

```piql
? user (id u_1) {
  id
  name
  email
  profile { bio avatar }
  posts { title createdAt }
}
```

### Directives (Optional)

```piql
? users { id name } @cache(ttl: 3600) @cols
```

| Directive | Purpose | Example |
|-----------|---------|---------|
| `@auth(role:X)` | Restrict by role | `name @auth(role: admin)` |
| `@cache(ttl:N)` | Cache N seconds | `? posts { * } @cache(ttl: 300)` |
| `@skip(if:$VAR)` | Skip conditionally | `email @skip(if: $hideEmail)` |
| `@include(if:$VAR)` | Include conditionally | `phone @include(if: $showPhone)` |
| `@rate(max:N per:UNIT)` | Rate limit | `>> events { * } @rate(max: 100 per: minute)` |
| `@cols` | Return columnar format | `? data { * } @cols` |

### Variables

Use variables in filters and payloads:

```piql
? users (role $userRole createdAt > $minDate) { id name }
```

Pass variables when executing:
```JavaScript
api.invoke('nexql:execute', {
  query: '? users (role $role) { id }',
  variables: { role: 'admin' }
})
```

### Pagination Directives

| Keyword | Type | Purpose |
|---------|------|---------|
| `$limit N` | int > 0 | Return max N records |
| `$offset N` | int ≥ 0 | Skip first N records |
| `$after "cursor"` | string | Cursor-based pagination |
| `$sort field [asc\|desc]` | field | Sort by field |
| `$fields N` | int ≥ 0 | Return first N fields only |

```piql
? posts (
  active true
  $limit 20
  $offset 40
  $sort createdAt desc
) { id title author { name } }
```

---

## 💡 Example Queries

### 1. Simple Read
```piql
? users { id name email }
```

### 2. Filtered Read with Nested Data
```piql
? posts (
  status published
  authorId $userId
  $limit 10
  $sort createdAt desc
) {
  id title body createdAt
  author { id name avatar }
  comments { id body authorName }
}
```

### 3. Create with Nested Data
```piql
+ post {
  title "My First Post"
  body "This is the content"
  authorId u_001
  tags ["piql" "tutorial"]
  metadata {
    published true
    featured false
  }
}
```

### 4. Update Specific Fields
```piql
~ user (id u_001) {
  name "Updated Name"
  settings {
    theme dark
    notifications true
  }
}
```

### 5. Delete Record
```piql
! post (id p_001)
```

### 6. Conditional Field Selection
```piql
? users {
  id
  name
  email @include(if: $showEmail)
  phone @skip(if: $hidePhone)
} @cache(ttl: 600)
```

### 7. Paginated Results with Custom Sort
```piql
? messages (
  channelId $channelId
  $limit 25
  $offset 50
  $sort createdAt desc
  $fields 5
) {
  id body author { name }
}
```

### 8. Read All Fields (Wildcard)
```piql
? user (id u_001) { * }
```

---

## 🛠️ Development

### Project Structure

```
nexql/
├── spec/                    # Language specification (EBNF, types)
├── ast/                     # AST node definitions
├── parser/                  # Lexer + Parser
│   ├── lexer.py
│   ├── parser.py
│   └── tokens.py
├── validator/               # Semantic validation
├── planner/                 # Query cost & strategy
├── runtime/                 # Executor, security, AI helpers
│   ├── executor.py
│   ├── security.py
│   ├── observability.py
│   ├── ai_helpers.py
│   ├── team_features.py
│   ├── visualization.py
│   ├── edge.py
│   └── server_entry.py
├── schema/                  # Schema registry & inference
├── storage/                 # JSON persistence
├── transport/               # HTTP + stdio servers
├── sdk/                     # Client code generation
├── plugins/                 # Plugin loader & lifecycle
├── ide/
│   ├── electron/            # Electron shell
│   ├── frontend/            # HTML/CSS/JS UI
│   └── ipc/                 # IPC bridge
├── tooling/                 # Optional feature wrappers
├── docs/                    # Architecture & migration guide
└── pyproject.toml          # Python configuration
```

### Running Tests

```bash
# Run all tests
python -m pytest

# Run specific test class
python -m pytest -k TestParser -v

# Run with coverage
python -m pytest --cov=nexql
```

### Adding a New Feature

1. **Add specification** → Update `spec/language.md`
2. **Add AST node** → Create class in `ast/nodes.py`
3. **Update parser** → Modify `parser/parser.py` and `parser/lexer.py`
4. **Add validator rule** → Add method to `validator/validator.py`
5. **Update executor** → Handle in `runtime/executor.py`
6. **Add tests** → Create test in `tooling/tests/`
7. **Update documentation** → Update relevant docs

### Building Distribution

```bash
# Linux AppImage
npm run build:appimage

# Linux DEB package
npm run build:deb

# General Linux build
npm run build
```

---

## 🔧 Troubleshooting

### Error: "ModuleNotFoundError: No module named 'nexql.runtime.observability'"
**Cause**: Runtime modules not properly installed.

**Solution**:
```bash
# Verify Python path
python3 -c "import sys; print(sys.path)"

# Run from project root
cd /home/nexu/project/nexql
npm start
```

### Error: "Electron fails to launch"
**Cause**: Node modules not installed.

**Solution**:
```bash
npm install
npm start
```

### Error: "Port 7890 already in use"
**Cause**: Another process using the port.

**Solution**:
```bash
# Use different port
npm run start:http -- --port 7891

# Or find and kill the process
lsof -i :7890
kill <PID>
```

### Queries Execute but Show No Results
**Cause**: Empty database or incorrect schema.

**Solution**:
```bash
# Check storage location
cat ~/.piql-workbench/databases/default.json

# Add test data manually and reload workbench
```

### IDE Shows "Disconnected" Status
**Cause**: IPC bridge communication failure.

**Solution**:
1. Restart the app
2. Check console for errors (Ctrl+Shift+I to open DevTools)
3. Verify no runtime errors in terminal

### Performance Issues / Slow Queries
**Cause**: Large datasets or missing indexes.

**Solution**:
1. Use **Tools → Benchmark** to measure latency
2. Check **Query Info** tab for execution cost
3. Use **Query → Show Plan** to see execution strategy
4. Consider using `$limit` to reduce result size

---

## 📚 Additional Resources

- **Architecture Deep Dive**: See `docs/architecture.md`
- **Language Specification**: See `spec/language.md`
- **API Documentation**: Generated via **Schema → API Docs** button
- **Migration Guide**: Check `docs/architecture.md` section 9

---

## 📝 License

Apache License 2.0 — See LICENSE file for details

---

## 🤝 Contributing

Piql is open source. To contribute:

1. Fork the repository
2. Create a feature branch
3. Add tests for new functionality
4. Submit a pull request with clear description

---

## 📞 Support

- **GitHub Issues**: https://github.com/bugooos/PIQL/issues
- **Documentation**: See `docs/` folder
- **Architecture Reference**: `docs/architecture.md`

---

**Last Updated**: May 24, 2026 | **Version**: 0.2.0
