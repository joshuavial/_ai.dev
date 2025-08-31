# Port Management Policy

> **Version:** 1.0  
> **Last Updated:** 2024-08-31  
> **Owner:** Development Team

## Overview

This document defines the port management policy for all development projects on this machine. The policy ensures that projects can run multiple instances simultaneously without port conflicts, while maintaining predictable and organized port assignments.

## Core Principles

1. **100-Port Blocks**: Each project is assigned a dedicated 100-port block
2. **10 Instances per Project**: Each project can run up to 10 simultaneous instances
3. **10 Ports per Instance**: Each instance gets 10 consecutive ports for all services
4. **Predictable Assignment**: Port numbers follow a consistent formula
5. **No Conflicts**: Proper registration prevents overlapping assignments

## Port Block Structure

### Main Port Ranges

| Range | Purpose | Notes |
|-------|---------|-------|
| `30000-39999` | Project port blocks | 100 blocks of 100 ports each |
| `54320-54999` | Reserved Supabase | Default Supabase installations |
| `3000-3999` | Legacy frontend | Gradual migration to new system |
| `8000-8999` | Legacy backend | Gradual migration to new system |
| `9000-9999` | System utilities | Docker, proxies, monitoring |

### Project Block Assignment

Projects are assigned sequential 100-port blocks:

- **Block 0**: `30000-30099` (Project 1)
- **Block 1**: `30100-30199` (Project 2)  
- **Block 2**: `30200-30299` (Project 3)
- **Block 3**: `30300-30399` (Project 4)
- And so on...

**Formula**: Project Block = `30000 + (ProjectID × 100)`

### Instance Subdivision

Within each 100-port project block, instances are assigned 10-port ranges:

- **Instance 1**: `+00-09` (Development/Main)
- **Instance 2**: `+10-19` (Test/CI)
- **Instance 3**: `+20-29` (Feature Branch 1)
- **Instance 4**: `+30-39` (Feature Branch 2)
- **Instance 5**: `+40-49` (Staging)
- **Instance 6**: `+50-59` (Performance Testing)
- **Instance 7**: `+60-69` (Integration Testing)
- **Instance 8**: `+70-79` (Preview/Demo)
- **Instance 9**: `+80-89` (Backup/Failover)
- **Instance 10**: `+90-99` (Experimental/Research)

**Formula**: Instance Ports = `ProjectBasePort + (InstanceID × 10)`

### Service Port Assignment

Within each 10-port instance block:

| Offset | Service | Description |
|--------|---------|-------------|
| `+0` | Frontend Dev Server | Next.js, React, Vue, etc. |
| `+1` | API/Backend Server | Express, FastAPI, etc. |
| `+2` | Database | Supabase DB, PostgreSQL |
| `+3` | Supabase API | Supabase REST API |
| `+4` | Supabase Studio | Database admin interface |
| `+5` | Supabase Inbucket | Email testing |
| `+6` | Analytics/Monitoring | Logs, metrics, analytics |
| `+7` | Vector/Search | Vector DB, search services |
| `+8` | Connection Pooler | DB pooling, connection management |
| `+9` | Reserved/Auxiliary | Additional services as needed |

## Registration Process

### 1. Check Availability

Before starting a new project:
1. Check the registry at `~/_ai/ports.md`
2. Find the next available 100-port block
3. Ensure no conflicts with existing assignments

### 2. Reserve Port Block

Add entry to `~/_ai/ports.md`:

```markdown
| Project Name | Block | Ports | Path | Status | Owner | Date |
|--------------|-------|-------|------|--------|-------|------|
| my-project | 30500-30599 | Block 5 | /path/to/project | reserved | username | 2024-08-31 |
```

### 3. Configure Project

Update your project configuration files:

**Environment Variables** (`.env.local`, `.env.test`):
```bash
# Instance 1 (Development)
FRONTEND_PORT=30500
API_PORT=30501
DB_PORT=30502
SUPABASE_API_PORT=30503
SUPABASE_STUDIO_PORT=30504
# ... etc
```

**Supabase Config** (`supabase/config.toml`):
```toml
[api]
port = 30503

[db]
port = 30502

[studio]
port = 30504
# ... etc
```

### 4. Document Instance Usage

When spinning up additional instances, document them:

```markdown
| Project | Instance | Ports | Purpose | Status | Branch/Environment |
|---------|----------|-------|---------|--------|--------------------|
| my-project | 1 | 30500-30509 | Development | active | main |
| my-project | 2 | 30510-30519 | Testing | active | test-suite |
| my-project | 3 | 30520-30529 | Feature Work | active | feature/auth |
```

## Example Configurations

### Example 1: Assessment Tracker

**Port Block**: `30200-30299` (Block 2)

**Instance 1 - Development**:
- Ports: `30200-30209`
- Frontend: `30200`
- API: `30201`
- Supabase DB: `30202`
- Supabase API: `30203`
- Supabase Studio: `30204`

**Instance 2 - Testing**:
- Ports: `30210-30219`
- Frontend: `30210`
- Supabase DB: `30212`
- Supabase API: `30213`

### Example 2: Spousey App

**Port Block**: `30100-30199` (Block 1)

**Instance 1 - Development**:
- Ports: `30100-30109`
- Frontend: `30100`
- Supabase DB: `30102`
- Supabase API: `30103`

**Instance 2 - Test Database**:
- Ports: `30110-30119`
- Test DB: `30112`
- Test API: `30113`

## Migration from Legacy Ports

### Phase 1: Document Current State
1. Identify all projects using legacy ports (3000-3999, 8000-8999, 54320+)
2. Document current port usage in the registry
3. Note conflicts and dependencies

### Phase 2: Plan Migration
1. Assign new port blocks to existing projects
2. Create migration schedule to avoid downtime
3. Update documentation and team communication

### Phase 3: Execute Migration
1. Update configuration files
2. Test new port assignments
3. Update CI/CD pipelines
4. Communicate changes to team

## Best Practices

### Development Workflow
1. **Always start with Instance 1** (base ports) for main development
2. **Use Instance 2** for test databases and CI
3. **Use higher instances** for feature branches and experiments
4. **Clean up unused instances** regularly to free resources

### Configuration Management
1. Use environment variables for all port assignments
2. Keep `.env.example` files updated with port ranges
3. Document instance purpose in project README
4. Use consistent naming conventions

### Troubleshooting
1. Check port conflicts: `lsof -i :PORT_NUMBER`
2. Verify registry for assignments: `cat ~/_ai/ports.md`
3. Kill processes on conflicting ports if needed
4. Update registry when decommissioning projects

## Port Conflict Resolution

### If Conflicts Arise
1. **Check the registry** first - verify assignment ownership
2. **Identify the conflicting services** using system tools
3. **Contact project owners** if needed
4. **Reassign if necessary** and update registry

### Emergency Procedures
1. **Immediate conflicts**: Use temporary high ports (40000+)
2. **Update registry** with emergency assignments
3. **Plan proper reassignment** as soon as possible
4. **Document the incident** for future reference

## Registry Maintenance

### Regular Tasks
- [ ] Review and clean up inactive projects monthly
- [ ] Verify port assignments match actual usage
- [ ] Update project status and ownership information
- [ ] Archive deprecated project entries

### Annual Review
- [ ] Assess overall port usage patterns
- [ ] Consider expanding or reorganizing port ranges
- [ ] Update policy based on team feedback
- [ ] Plan for future scaling needs

## Tools and Scripts

### Useful Commands

**Check port usage**:
```bash
# List all listening ports
lsof -i -P -n | grep LISTEN

# Check specific port
lsof -i :30200

# Check port range
lsof -i :30200-30209
```

**Kill process on port**:
```bash
# Find and kill process
lsof -ti:PORT_NUMBER | xargs kill -9
```

### Recommended Scripts

Consider creating helper scripts in your projects:

**`scripts/ports.sh`** - Show assigned ports for current project
**`scripts/check-ports.sh`** - Verify no conflicts before starting
**`scripts/next-instance.sh`** - Calculate next available instance ports

## FAQ

### Q: What if I need more than 10 instances?
**A:** Contact the team to assign an additional 100-port block, or use higher temporary ports (40000+) and document in registry.

### Q: Can I use ports outside my assigned block?
**A:** Only for temporary testing. All permanent assignments must be registered and within assigned blocks.

### Q: What about Docker port mapping?
**A:** Docker internal ports can use any values. External/host ports must follow this policy.

### Q: How do I handle legacy projects?
**A:** Document current usage in registry with "legacy" status, then plan migration during next major update.

---

## Registry Location

**Central Registry**: `~/_ai/ports.md`  
**Backup**: Consider version controlling the registry file  
**Access**: All team members should have read access, designated admins have write access

For questions or updates to this policy, contact the development team lead.