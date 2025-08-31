# Developer Helper Scripts Protocol

## Overview

This protocol defines the standardized approach for creating and managing developer helper scripts that simplify common development tasks. These scripts provide shortcuts for frequently used commands, ensure consistent environment configuration, and integrate with the port management system.

## Purpose

Helper scripts serve to:
- Reduce repetitive typing of complex commands
- Ensure consistent use of environment variables
- Integrate with port management for conflict-free development
- Provide a unified interface for common tasks across projects
- Simplify onboarding for new developers

## Standard Script Pattern

### Basic Structure

All helper scripts follow this pattern:

```bash
#!/bin/bash
source .env.local

# Script logic here
command_with_args $ENV_VAR
```

### Key Components

1. **Shebang Line**: `#!/bin/bash` - Specifies the interpreter
2. **Environment Loading**: `source .env.local` - Loads environment variables
3. **Command Execution**: The actual command using environment variables

## Environment Integration

### Loading Environment Variables

Scripts should source the appropriate environment file:

```bash
# For development
source .env.local

# For testing
source .env.local.test

# For production (if applicable)
source .env.production
```

### Using Environment Variables

Access variables after sourcing:

```bash
source .env.local

# Use PORT from environment
echo "Starting server on port $PORT"

# Use database configuration
psql "$DATABASE_URL"
```

## Port Management Integration

### Port-Aware Scripts

Helper scripts should use ports defined in `.env.local` following the port management protocol:

```bash
#!/bin/bash
source .env.local

# PORT is defined in .env.local as 30200 (Block 2, Instance 1)
pnpm next dev -p $PORT
```

### Database Connection Scripts

For database access, use the allocated database port:

```bash
#!/bin/bash
source .env.local

# DB connection using port from Block 2, Instance 1
# Port 30202 for Supabase DB
psql "postgresql://postgres:postgres@localhost:30202/postgres" "$@"
```

## Common Script Templates

### 1. Development Server Launcher (`./dev`)

**Purpose**: Start the development server with the correct port

```bash
#!/bin/bash
source .env.local
pnpm next dev -p $PORT
```

**Alternative with options**:
```bash
#!/bin/bash
source .env.local

# Support additional Next.js options
# Usage: ./dev --turbo
pnpm next dev -p $PORT "$@"
```

### 2. Database Client Wrapper (`./psql`)

**Purpose**: Quick access to the development database

```bash
#!/bin/bash
source .env.local

# Support both interactive and command mode
if [ $# -gt 0 ]; then
    # Execute SQL command directly
    psql "postgresql://postgres:postgres@localhost:30202/postgres" -c "$*"
else
    # Interactive mode
    psql "postgresql://postgres:postgres@localhost:30202/postgres"
fi
```

**Alternative using DATABASE_URL**:
```bash
#!/bin/bash
source .env.local

# Use DATABASE_URL if defined
DB_URL=${DATABASE_URL:-"postgresql://postgres:postgres@localhost:30202/postgres"}

if [ $# -gt 0 ]; then
    psql "$DB_URL" -c "$*"
else
    psql "$DB_URL"
fi
```

### 3. Test Runner Shortcut (`./test`)

**Purpose**: Run tests with proper environment

```bash
#!/bin/bash
source .env.local.test

# Run tests with test database
pnpm test "$@"
```

### 4. Database Reset Script (`./db-reset`)

**Purpose**: Quick database reset for development

```bash
#!/bin/bash
source .env.local

echo "Resetting development database..."
pnpm run db:reset:dev
echo "Database reset complete!"
```

### 5. Full Stack Launcher (`./start`)

**Purpose**: Start all services (database + app)

```bash
#!/bin/bash
source .env.local

echo "Starting Supabase..."
pnpm run db:start

echo "Waiting for database to be ready..."
sleep 3

echo "Starting development server on port $PORT..."
pnpm next dev -p $PORT
```

### 6. Type Check Script (`./typecheck`)

**Purpose**: Quick type checking

```bash
#!/bin/bash
pnpm run type:check "$@"
```

### 7. Quick Lint (`./lint`)

**Purpose**: Run linting with auto-fix

```bash
#!/bin/bash
pnpm run lint:fix "$@"
```

## Setup Instructions

### Creating a New Helper Script

1. **Create the script file** in the project root:
   ```bash
   touch ./scriptname
   ```

2. **Add the script content**:
   ```bash
   cat > ./scriptname << 'EOF'
   #!/bin/bash
   source .env.local
   
   # Your command here
   EOF
   ```

3. **Make it executable**:
   ```bash
   chmod +x ./scriptname
   ```

4. **Add to .gitignore** (optional):
   ```bash
   echo "./scriptname" >> .gitignore
   ```

### Standard Scripts Checklist

For a new project, consider creating these standard scripts:

- [ ] `./dev` - Development server launcher
- [ ] `./psql` - Database client wrapper
- [ ] `./test` - Test runner
- [ ] `./lint` - Linting shortcut
- [ ] `./typecheck` - Type checking
- [ ] `./build` - Build shortcut
- [ ] `./db-reset` - Database reset

## Best Practices

### 1. Naming Conventions

- Use lowercase with no extension: `./dev`, not `./dev.sh`
- Use clear, short names: `./test`, not `./run-tests`
- Use hyphens for multi-word scripts: `./db-reset`

### 2. Error Handling

Add error handling for robustness:

```bash
#!/bin/bash
set -e  # Exit on error
source .env.local || { echo "Failed to load .env.local"; exit 1; }

# Your commands
```

### 3. Help Text

For complex scripts, add help:

```bash
#!/bin/bash

if [ "$1" = "--help" ] || [ "$1" = "-h" ]; then
    echo "Usage: ./scriptname [options]"
    echo "  Options:"
    echo "    -h, --help    Show this help"
    echo "    -v, --verbose Enable verbose output"
    exit 0
fi

source .env.local
# Script logic
```

### 4. Idempotency

Make scripts safe to run multiple times:

```bash
#!/bin/bash
source .env.local

# Check if already running
if lsof -i:$PORT > /dev/null; then
    echo "Server already running on port $PORT"
    exit 1
fi

pnpm next dev -p $PORT
```

### 5. Documentation

Add comments for complex logic:

```bash
#!/bin/bash
# Development server launcher
# Uses port from .env.local (Block 2, Instance 1)
source .env.local

# Check if port is available before starting
if lsof -i:$PORT > /dev/null; then
    echo "Port $PORT is already in use"
    exit 1
fi

pnpm next dev -p $PORT
```

## Integration with Port Management

### Using Allocated Ports

Always reference ports from environment variables that follow the port management protocol:

```bash
# Good - uses managed port
source .env.local
pnpm next dev -p $PORT

# Bad - hardcoded port
pnpm next dev -p 3000
```

### Multiple Instances

For running multiple instances, create instance-specific scripts:

```bash
# ./dev-instance-2
#!/bin/bash
source .env.local.instance2
pnpm next dev -p $PORT  # Uses port 30210 (Block 2, Instance 2)
```

## Security Considerations

### 1. Never Hardcode Secrets

```bash
# Bad
psql "postgresql://user:secretpassword@localhost/db"

# Good
source .env.local
psql "$DATABASE_URL"
```

### 2. Validate Input

For scripts accepting arguments:

```bash
#!/bin/bash
source .env.local

# Validate input
if [ -z "$1" ]; then
    echo "Error: Argument required"
    exit 1
fi

# Use quotes to prevent injection
psql "$DATABASE_URL" -c "$1"
```

### 3. File Permissions

Keep scripts executable but not writable by others:

```bash
chmod 755 ./scriptname  # rwxr-xr-x
```

## Troubleshooting

### Script Not Found

```bash
# Make sure script is executable
chmod +x ./scriptname

# Check shebang line
head -1 ./scriptname  # Should be #!/bin/bash
```

### Environment Variables Not Loading

```bash
# Debug environment loading
#!/bin/bash
set -x  # Enable debug output
source .env.local
echo "PORT=$PORT"  # Check if loaded
```

### Port Already in Use

```bash
# Find process using port
lsof -i:30200

# Kill process if needed
kill $(lsof -t -i:30200)
```

## Examples from Assessment Tracker

### `./dev` Script

```bash
#!/bin/bash
source .env.local
pnpm next dev -p $PORT
```

- Sources `.env.local` to get PORT=30200
- Starts Next.js dev server on allocated port
- Simple one-line command wrapper

### `./psql` Script

```bash
#!/bin/bash
source .env.local

# If SQL is passed as argument, execute it with -c
# Otherwise run interactive psql (which also accepts piped input)
if [ $# -gt 0 ]; then
    psql "postgresql://postgres:postgres@localhost:30202/postgres" -c "$*"
else
    psql "postgresql://postgres:postgres@localhost:30202/postgres"
fi
```

- Sources environment for consistency
- Supports both interactive and command modes
- Uses port 30202 (Block 2, Instance 1, DB port)
- Allows piped input: `echo "SELECT * FROM users" | ./psql`

## Version Control

### What to Commit

Generally commit helper scripts to version control:

```gitignore
# .gitignore
# Don't ignore helper scripts - they're useful for the team
# Only ignore if they contain sensitive configuration
```

### Scripts with Sensitive Data

If a script must contain sensitive data:

1. Create a template: `./scriptname.template`
2. Add actual script to `.gitignore`
3. Document setup in README

```bash
# scriptname.template
#!/bin/bash
export API_KEY="YOUR_API_KEY_HERE"
./run-with-key
```

## Testing Helper Scripts

### Dry Run Mode

Add dry-run support for testing:

```bash
#!/bin/bash
source .env.local

if [ "$1" = "--dry-run" ]; then
    echo "Would run: pnpm next dev -p $PORT"
    exit 0
fi

pnpm next dev -p $PORT
```

### Validation Script

Create a script to validate all helper scripts:

```bash
#!/bin/bash
# ./validate-scripts

for script in ./dev ./psql ./test; do
    if [ -x "$script" ]; then
        echo "✓ $script is executable"
    else
        echo "✗ $script is not executable"
    fi
done
```

## Migration Guide

### Converting npm Scripts to Helper Scripts

Instead of:
```json
"scripts": {
  "dev": "next dev -p 3000"
}
```

Create:
```bash
#!/bin/bash
source .env.local
pnpm next dev -p $PORT
```

Benefits:
- Environment-aware
- Shorter commands (`./dev` vs `pnpm run dev`)
- Can add logic (pre-checks, logging, etc.)
- Consistent with port management

## Conclusion

Helper scripts are a powerful tool for streamlining development workflows. By following this protocol, teams can create consistent, maintainable, and user-friendly command shortcuts that integrate seamlessly with the port management system and environment configuration.