# Supabase Test Database Protocol

## Overview

This protocol defines the standardized approach for setting up and managing isolated Supabase test databases for automated testing. It ensures fast, reliable, and conflict-free test execution across projects using Supabase.

## Core Principles

1. **Isolation**: Test databases run on separate ports from development databases
2. **Speed**: Database snapshots enable fast restoration between test runs
3. **Consistency**: Migration tracking ensures test databases stay synchronized
4. **Automation**: Minimal manual intervention required for test execution
5. **Portability**: Works across different environments (local, CI/CD)

## Directory Structure

### Standard Test Database Layout

```
project/
├── supabase/                  # Main Supabase configuration
│   ├── config.toml           # Development database config
│   ├── migrations/           # Database migrations
│   ├── seeds/                # Seed data files
│   │   ├── dev.sql          # Development seed data
│   │   ├── test.sql         # Test seed data
│   │   └── stage.sql        # Staging seed data
│   └── seed.sql             # Default seed file
│
├── supabase-test/            # Test-specific Supabase instance
│   ├── config.toml          # Test database config (custom ports)
│   ├── migrations/          # Symlink → ../supabase/migrations
│   ├── seed.sql             # Symlink → ../supabase/seeds/test.sql
│   ├── snapshots/           # Database snapshots
│   │   ├── clean-db.sql     # Clean database dump
│   │   └── metadata.json    # Snapshot metadata
│   └── supabase/            # Supabase internal files (auto-generated)
│
├── scripts/
│   ├── test-db-snapshot.ts  # Snapshot management script
│   └── clean-test-db.sql    # Database cleanup script
│
├── .env.local               # Development environment variables
├── .env.local.test          # Test environment variables
└── vitest.config.ts         # Test runner configuration
```

## Port Allocation Strategy

### Following Port Management Protocol

Reference: `protocol port-management`

Test databases should use different port ranges than development to avoid conflicts:

```toml
# Development Instance (Block X, Instance 1)
[api]
port = 30X03  # e.g., 30203 for Block 2

[db]
port = 30X02  # e.g., 30202 for Block 2

# Test Instance (Block X, Instance 2)
[api]
port = 30X13  # e.g., 30213 for Block 2

[db]
port = 30X12  # e.g., 30212 for Block 2
```

### Legacy Port Allocation (if not using port management protocol)

For projects still using legacy ports:

```toml
# Standard Supabase Development
API: 54321
DB: 54322

# Test Database (avoid conflicts)
API: 54331 or 54341
DB: 54332 or 54342
```

## Configuration Files

### 1. Test Supabase Configuration (`supabase-test/config.toml`)

```toml
project_id = "project-name-test"

[api]
enabled = true
port = 54331  # Different from dev (54321)
schemas = ["public", "graphql_public"]
extra_search_path = ["public", "extensions"]
max_rows = 1000

[db]
port = 54332  # Different from dev (54322)
shadow_port = 54330
major_version = 17

[db.pooler]
enabled = false
port = 54339

[studio]
enabled = true
port = 54333  # Different from dev (54323)

[inbucket]
enabled = true
port = 54334  # Different from dev (54324)
```

### 2. Test Environment Variables (`.env.local.test`)

```bash
# Test Database Configuration
NEXT_PUBLIC_SUPABASE_URL=http://127.0.0.1:54331
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
SUPABASE_SERVICE_ROLE_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

# Database connection for scripts
DATABASE_URL=postgresql://postgres:postgres@localhost:54332/postgres
DB_HOST=localhost
DB_PORT=54332
DB_NAME=postgres
DB_USER=postgres
DB_PASSWORD=postgres

# Test environment flag
NODE_ENV=test

# Copy other required variables from .env.local
# (API keys, external services, etc.)
```

### 3. Package.json Scripts

```json
{
  "scripts": {
    // Test execution scripts
    "test": "pnpm run test:db:restore && vitest",
    "test:ui": "pnpm run test:db:restore && vitest --ui",
    "test:coverage": "pnpm run test:db:restore && vitest --coverage",
    "test:watch": "pnpm run test:db:restore && vitest --watch",
    "test:ci": "pnpm run test:db:reset && vitest",
    "test:only": "vitest",
    
    // Test database management
    "test:db:start": "cd supabase-test && pnpx supabase start",
    "test:db:stop": "cd supabase-test && pnpx supabase stop",
    "test:db:reset": "cd supabase-test && pnpx supabase db reset --local",
    "test:db:status": "cd supabase-test && pnpx supabase status",
    
    // Snapshot management
    "test:db:snapshot": "tsx scripts/test-db-snapshot.ts --create",
    "test:db:restore": "tsx scripts/test-db-snapshot.ts --restore",
    "test:db:validate": "tsx scripts/test-db-snapshot.ts --validate"
  }
}
```

## Snapshot Management System

### Purpose

Database snapshots provide fast restoration between test runs, avoiding the overhead of running migrations repeatedly.

### Snapshot Script Template (`scripts/test-db-snapshot.ts`)

```typescript
#!/usr/bin/env tsx
import { execSync } from 'child_process'
import { existsSync, mkdirSync, writeFileSync, readFileSync } from 'fs'
import { join } from 'path'
import { config } from 'dotenv'
import crypto from 'crypto'

// Load test environment
config({ path: '.env.local.test' })

const SNAPSHOT_DIR = join(process.cwd(), 'supabase-test', 'snapshots')
const SNAPSHOT_FILE = join(SNAPSHOT_DIR, 'clean-db.sql')
const SNAPSHOT_METADATA = join(SNAPSHOT_DIR, 'metadata.json')

interface SnapshotMetadata {
  version: string
  createdAt: string
  migrationsHash: string
}

// Ensure snapshot directory exists
if (!existsSync(SNAPSHOT_DIR)) {
  mkdirSync(SNAPSHOT_DIR, { recursive: true })
}

function getMigrationsHash(): string {
  const migrationsDir = join(process.cwd(), 'supabase-test', 'migrations')
  try {
    const fs = require('fs')
    const files = fs.readdirSync(migrationsDir)
      .filter((f: string) => f.endsWith('.sql'))
      .sort()
    
    const hash = crypto.createHash('md5')
    for (const file of files) {
      const content = fs.readFileSync(join(migrationsDir, file), 'utf8')
      hash.update(file + content)
    }
    
    return hash.digest('hex')
  } catch (error) {
    console.warn('Could not compute migrations hash:', error)
    return 'unknown'
  }
}

function createSnapshot(): void {
  console.log('Creating database snapshot...')
  
  try {
    // Reset database to clean state
    console.log('Resetting database to clean state...')
    execSync('cd supabase-test && pnpx supabase db reset --local', {
      stdio: 'inherit'
    })
    
    // Create database dump
    console.log('Creating database dump...')
    execSync(`cd supabase-test && pnpx supabase db dump --local > "${SNAPSHOT_FILE}"`, { 
      stdio: 'inherit',
      shell: true 
    })
    
    // Save metadata
    const metadata: SnapshotMetadata = {
      version: '1.0',
      createdAt: new Date().toISOString(),
      migrationsHash: getMigrationsHash()
    }
    
    writeFileSync(SNAPSHOT_METADATA, JSON.stringify(metadata, null, 2))
    
    console.log('✅ Snapshot created successfully!')
  } catch (error) {
    console.error('❌ Failed to create snapshot:', error)
    process.exit(1)
  }
}

function restoreSnapshot(): void {
  console.log('Restoring database from snapshot...')
  
  if (!existsSync(SNAPSHOT_FILE) || !existsSync(SNAPSHOT_METADATA)) {
    console.warn('⚠️  Snapshot files not found. Creating initial snapshot...')
    createSnapshot()
    return
  }
  
  try {
    // Check if migrations have changed
    const metadata: SnapshotMetadata = JSON.parse(
      readFileSync(SNAPSHOT_METADATA, 'utf8')
    )
    const currentHash = getMigrationsHash()
    
    if (metadata.migrationsHash !== currentHash) {
      console.warn('⚠️  Migrations have changed. Recreating snapshot...')
      createSnapshot()
      return
    }
    
    // Clean and restore database
    const databaseUrl = process.env.DATABASE_URL || 
      `postgresql://postgres:postgres@localhost:${process.env.DB_PORT || '54332'}/postgres`
    
    // Clean database
    const cleanScript = join(process.cwd(), 'scripts', 'clean-test-db.sql')
    execSync(`psql "${databaseUrl}" -f "${cleanScript}"`, { stdio: 'ignore' })
    
    // Restore snapshot
    execSync(`psql "${databaseUrl}" -q -f "${SNAPSHOT_FILE}"`, { stdio: 'pipe' })
    
    console.log('✅ Database restored from snapshot!')
  } catch (error) {
    console.error('❌ Failed to restore snapshot:', error)
    process.exit(1)
  }
}

function validateSnapshot(): boolean {
  if (!existsSync(SNAPSHOT_FILE) || !existsSync(SNAPSHOT_METADATA)) {
    return false
  }
  
  try {
    const metadata: SnapshotMetadata = JSON.parse(
      readFileSync(SNAPSHOT_METADATA, 'utf8')
    )
    const currentHash = getMigrationsHash()
    return metadata.migrationsHash === currentHash
  } catch {
    return false
  }
}

// CLI handling
const command = process.argv[2]

switch (command) {
  case '--create':
    createSnapshot()
    break
  case '--restore':
    restoreSnapshot()
    break
  case '--validate':
    const isValid = validateSnapshot()
    console.log(isValid ? '✅ Snapshot is valid' : '❌ Snapshot is invalid')
    process.exit(isValid ? 0 : 1)
    break
  default:
    console.log('Usage: tsx scripts/test-db-snapshot.ts [--create|--restore|--validate]')
    process.exit(1)
}
```

### Database Cleanup Script (`scripts/clean-test-db.sql`)

```sql
-- Clean database for snapshot restoration
-- Approach 1: Drop all user schemas (aggressive)
DO $$
DECLARE
    schema_name TEXT;
BEGIN
    FOR schema_name IN 
        SELECT s.schema_name 
        FROM information_schema.schemata s
        WHERE s.schema_name NOT IN (
            'information_schema', 
            'pg_catalog', 
            'pg_toast',
            'pg_statistic'
        )
        AND s.schema_name NOT LIKE 'pg_temp_%'
        AND s.schema_name NOT LIKE 'pg_toast_temp_%'
    LOOP
        EXECUTE 'DROP SCHEMA IF EXISTS ' || quote_ident(schema_name) || ' CASCADE';
    END LOOP;
END $$;

-- Approach 2: Clean public schema only (conservative)
-- Use this if Approach 1 causes issues with Supabase internals
DO $$ 
DECLARE 
    r RECORD;
BEGIN
    -- Drop all tables in public schema
    FOR r IN (SELECT tablename FROM pg_tables WHERE schemaname = 'public') 
    LOOP
        EXECUTE 'DROP TABLE IF EXISTS public.' || quote_ident(r.tablename) || ' CASCADE';
    END LOOP;
    
    -- Drop all views in public schema
    FOR r IN (SELECT viewname FROM pg_views WHERE schemaname = 'public') 
    LOOP
        EXECUTE 'DROP VIEW IF EXISTS public.' || quote_ident(r.viewname) || ' CASCADE';
    END LOOP;
    
    -- Drop all functions in public schema
    FOR r IN (
        SELECT proname, pg_get_function_identity_arguments(oid) as args 
        FROM pg_proc 
        WHERE pronamespace = 'public'::regnamespace
    ) 
    LOOP
        EXECUTE 'DROP FUNCTION IF EXISTS public.' || quote_ident(r.proname) || '(' || r.args || ') CASCADE';
    END LOOP;
END $$;
```

## Test Runner Integration

### Vitest Configuration (`vitest.config.ts`)

```typescript
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    globals: true,
    setupFiles: './src/test/setup.ts',
    silent: process.env.CI === 'true',
    reporters: process.env.VERBOSE ? 'verbose' : ['default'],
    onConsoleLog(log, type) {
      // Filter out known Supabase warnings
      if (log.includes('Multiple GoTrueClient instances detected')) return false
      if (log.includes('It is not an error')) return false
      return true
    },
  },
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
})
```

### Test Setup File (`src/test/setup.ts`)

```typescript
import '@testing-library/jest-dom'
import { config } from 'dotenv'
import { join } from 'path'

// Load test environment variables
config({ path: join(process.cwd(), '.env.local.test') })

// Fix for BroadcastChannel errors in Node.js test environment
global.BroadcastChannel = class BroadcastChannel {
  name: string
  onmessage: ((event: any) => void) | null = null
  private listeners: Map<string, Set<Function>> = new Map()
  
  constructor(name: string) {
    this.name = name
  }
  
  postMessage(message: any) {
    // Mock implementation for tests
  }
  
  close() {
    this.onmessage = null
    this.listeners.clear()
  }
  
  addEventListener(type: string, listener: Function) {
    if (!this.listeners.has(type)) {
      this.listeners.set(type, new Set())
    }
    this.listeners.get(type)!.add(listener)
  }
  
  removeEventListener(type: string, listener: Function) {
    this.listeners.get(type)?.delete(listener)
  }
  
  dispatchEvent(event: any): boolean {
    return true
  }
} as any

// Mock Next.js router if using Next.js
vi.mock('next/navigation', () => ({
  useRouter() {
    return {
      push: vi.fn(),
      replace: vi.fn(),
      prefetch: vi.fn(),
      back: vi.fn(),
      forward: vi.fn(),
      refresh: vi.fn(),
    }
  },
  useSearchParams() {
    return new URLSearchParams()
  },
  usePathname() {
    return ''
  },
}))
```

## Setup Instructions

### Initial Setup

1. **Create test database directory structure:**
   ```bash
   mkdir -p supabase-test/snapshots
   ```

2. **Create symlinks to share migrations:**
   ```bash
   cd supabase-test
   ln -s ../supabase/migrations migrations
   ln -s ../supabase/seeds/test.sql seed.sql
   ```

3. **Copy and modify config.toml:**
   ```bash
   cp supabase/config.toml supabase-test/config.toml
   # Edit ports to avoid conflicts
   ```

4. **Create `.env.local.test`:**
   ```bash
   cp .env.local .env.local.test
   # Update ports to match test config.toml
   ```

5. **Initialize test database:**
   ```bash
   pnpm run test:db:start
   pnpm run test:db:snapshot
   ```

### Daily Usage

```bash
# Run tests (auto-restores database)
pnpm test

# Run specific test file
pnpm test src/components/Button.test.tsx

# Run tests in watch mode
pnpm test:watch

# Run with coverage
pnpm test:coverage

# Recreate snapshot after migration changes
pnpm run test:db:snapshot
```

## CI/CD Integration

### GitHub Actions Example

```yaml
name: Test

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - uses: pnpm/action-setup@v2
        with:
          version: 8
          
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'pnpm'
          
      - name: Install dependencies
        run: pnpm install
        
      - name: Start Supabase test database
        run: pnpm run test:db:start
        
      - name: Run tests
        run: pnpm run test:ci
        env:
          CI: true
          
      - name: Stop Supabase
        if: always()
        run: pnpm run test:db:stop
```

## Best Practices

### 1. Snapshot Management

- **Create snapshots** after initial setup and migration changes
- **Validate snapshots** in CI to ensure they're up-to-date
- **Store snapshots** in version control for consistency
- **Document** snapshot recreation steps in README

### 2. Test Data

- **Use minimal seed data** for faster restoration
- **Create test-specific seeds** separate from development seeds
- **Avoid large datasets** in test seeds
- **Use factories** for test-specific data creation

### 3. Performance Optimization

- **Restore from snapshot** instead of running migrations
- **Clean database** between test suites, not individual tests
- **Use transactions** for test isolation when possible
- **Parallelize tests** that don't share state

### 4. Environment Isolation

- **Never share databases** between development and test
- **Use different port ranges** to avoid conflicts
- **Separate environment files** for clarity
- **Document port allocations** in project README

### 5. Debugging Tips

- **Check port availability:** `lsof -i :54332`
- **Verify database status:** `pnpm run test:db:status`
- **Inspect snapshot validity:** `pnpm run test:db:validate`
- **Force recreate snapshot:** `pnpm run test:db:snapshot`
- **View Supabase logs:** `cd supabase-test && pnpx supabase logs`

## Common Issues and Solutions

### Issue: Port Already in Use

**Solution:** Check for running Supabase instances:
```bash
# Find process using port
lsof -i :54332

# Stop all Supabase instances
pnpm run db:stop
pnpm run test:db:stop
```

### Issue: Snapshot Restoration Fails

**Solution:** Recreate snapshot from scratch:
```bash
pnpm run test:db:reset
pnpm run test:db:snapshot
```

### Issue: BroadcastChannel Errors in Tests

**Solution:** Ensure test setup file includes BroadcastChannel mock (see Test Setup File section)

### Issue: Migrations Out of Sync

**Solution:** The snapshot system auto-detects migration changes via hash comparison and recreates snapshots automatically

### Issue: Tests Timeout

**Solution:** 
- Increase test timeout in vitest.config.ts
- Ensure database is started before tests
- Check snapshot restoration performance

## Migration from Existing Setup

### Step 1: Audit Current Setup

1. Document current port usage
2. Identify test data requirements
3. Review existing test scripts

### Step 2: Create Parallel Setup

1. Set up supabase-test directory alongside existing setup
2. Configure non-conflicting ports
3. Create initial snapshot

### Step 3: Gradual Migration

1. Update test scripts to use new setup
2. Run tests in parallel to verify
3. Remove old setup once stable

### Step 4: Document Changes

1. Update README with new test commands
2. Document port allocations
3. Update CI/CD pipelines

## Verification Checklist

- [ ] Test database runs on different ports than development
- [ ] Migrations are symlinked, not duplicated
- [ ] Snapshot system creates and restores successfully
- [ ] Test environment variables are properly isolated
- [ ] Package.json includes all necessary scripts
- [ ] CI/CD pipeline runs tests successfully
- [ ] Port allocations follow project standards
- [ ] Documentation is updated with setup instructions
- [ ] Team members can run tests locally
- [ ] Snapshot validation detects migration changes