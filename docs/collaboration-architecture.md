# Team Collaboration Architecture
## Design System Framework - Multi-User Support

**Version:** 1.0.0
**Date:** November 5, 2025
**Status:** Draft
**Phase:** Phase 2-3 Feature

---

## Table of Contents
1. [Overview](#1-overview)
2. [Collaboration Modes](#2-collaboration-modes)
3. [Architecture Options](#3-architecture-options)
4. [Recommended Architecture](#4-recommended-architecture)
5. [Technical Implementation](#5-technical-implementation)
6. [Security & Permissions](#6-security--permissions)
7. [Data Synchronization](#7-data-synchronization)
8. [Conflict Resolution](#8-conflict-resolution)

---

## 1. Overview

### 1.1 Requirements for Team Collaboration

**Core Needs:**
- Multiple users working on the same design system
- Real-time or near-real-time updates
- Role-based permissions (designers, developers, viewers)
- Version history and change tracking
- Conflict resolution
- Commenting and feedback

**User Types:**
1. **Design System Owner** - Full control, admin
2. **Designers** - Edit design tokens, create components
3. **Developers** - View tokens, export code, implement
4. **Stakeholders** - View-only, comment
5. **Contributors** - Suggest changes, create proposals

---

## 2. Collaboration Modes

### Mode 1: Solo Mode (Current - Phase 1)
**Storage:** Browser only (IndexedDB)
**Sync:** None
**Users:** Single user
**Cost:** Free
**Offline:** Yes

**Use case:** Individual designers, personal projects

---

### Mode 2: Async Collaboration (Phase 2)
**Storage:** Browser + Cloud backup
**Sync:** Manual or periodic
**Users:** 1-10 team members
**Cost:** Free or low-cost
**Offline:** Yes, with sync when online

**Use case:** Small teams, occasional collaboration

**How it works:**
```
User A makes changes
    ↓
Saves project
    ↓
Cloud sync (background)
    ↓
User B opens project
    ↓
Pulls latest changes
    ↓
Makes changes
    ↓
Sync (merge or conflict resolution)
```

---

### Mode 3: Real-Time Collaboration (Phase 3)
**Storage:** Cloud-first with local cache
**Sync:** Real-time (WebSocket)
**Users:** Unlimited
**Cost:** Paid tier
**Offline:** Limited (read-only cache)

**Use case:** Large teams, enterprise, active collaboration

**How it works:**
```
User A makes change
    ↓
WebSocket → Server
    ↓
Server broadcasts to all connected users
    ↓
User B/C/D receive update (instant)
    ↓
UI updates in real-time
```

---

## 3. Architecture Options

### Option A: Git-Based Collaboration (Simplest)

**Architecture:**
```
User's Browser
    ↓
Export project as JSON
    ↓
Commit to GitHub repository
    ↓
Team members pull changes
    ↓
Import JSON into their browser
```

**Pros:**
- No backend needed
- Version control built-in
- Free (GitHub)
- Familiar to developers
- Full audit trail

**Cons:**
- Manual sync process
- Not user-friendly for non-technical users
- No real-time updates
- Merge conflicts need Git knowledge

**Best for:** Developer-heavy teams, budget-conscious projects

---

### Option B: Cloud Storage Sync (Simple)

**Architecture:**
```
User's Browser
    ↓
IndexedDB (local)
    ↓
Sync Service (background)
    ↓
Cloud Storage (S3, Dropbox, Google Drive)
    ↓
Other users sync down
```

**Pros:**
- Simple to implement
- Low cost
- Works offline
- Familiar UX (like Dropbox)

**Cons:**
- Not real-time
- Conflict resolution challenging
- No fine-grained permissions
- Limited collaboration features

**Best for:** Small teams (2-5 people), simple use cases

---

### Option C: Backend API + Database (Recommended)

**Architecture:**
```
┌─────────────────────────────────────────────────────────────┐
│                         User Browser                        │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  React App                                            │  │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐     │  │
│  │  │  Wizard    │  │  Library   │  │   Sync     │     │  │
│  │  │  Module    │  │  Module    │  │  Service   │     │  │
│  │  └────────────┘  └────────────┘  └────────────┘     │  │
│  │                                         ↕              │  │
│  │  ┌────────────┐                  ┌────────────┐     │  │
│  │  │ IndexedDB  │                  │ WebSocket  │     │  │
│  │  │  (Cache)   │                  │  Client    │     │  │
│  │  └────────────┘                  └────────────┘     │  │
│  └──────────────────────────────────────────────────────┘  │
└───────────────────────────────┬─────────────────────────────┘
                                │ HTTPS / WSS
                                │
┌───────────────────────────────▼─────────────────────────────┐
│                      Backend API Server                     │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Node.js + Express/Fastify                           │  │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐     │  │
│  │  │  REST API  │  │ WebSocket  │  │   Auth     │     │  │
│  │  │ Endpoints  │  │   Server   │  │  Service   │     │  │
│  │  └────────────┘  └────────────┘  └────────────┘     │  │
│  └──────────────────────────────────────────────────────┘  │
│                                ↕                            │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Business Logic                                       │  │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐     │  │
│  │  │  Project   │  │   User     │  │   Team     │     │  │
│  │  │  Service   │  │  Service   │  │  Service   │     │  │
│  │  └────────────┘  └────────────┘  └────────────┘     │  │
│  └──────────────────────────────────────────────────────┘  │
└───────────────────────────────┬─────────────────────────────┘
                                │
┌───────────────────────────────▼─────────────────────────────┐
│                      Data Layer                             │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐           │
│  │ PostgreSQL │  │   Redis    │  │     S3     │           │
│  │ (Projects, │  │  (Cache,   │  │  (Assets,  │           │
│  │  Users,    │  │  Sessions, │  │  Exports)  │           │
│  │  Teams)    │  │  Realtime) │  │            │           │
│  └────────────┘  └────────────┘  └────────────┘           │
└─────────────────────────────────────────────────────────────┘
```

**Pros:**
- Full collaboration features
- Real-time updates possible
- Fine-grained permissions
- Scalable
- Professional features (audit logs, comments, etc.)

**Cons:**
- Complex to build
- Infrastructure costs
- Requires backend maintenance
- More security considerations

**Best for:** Medium to large teams, enterprise, serious collaboration

---

### Option D: Hybrid Local-First + Sync (Best of Both)

**Architecture:**
```
Browser (Works offline)
    ↓
IndexedDB (primary storage)
    ↓
Background Sync Service
    ↓
Cloud Backend (optional)
    ↓
Sync when online, work offline
```

**Pros:**
- Works offline (best UX)
- Optional collaboration
- Privacy by default
- No vendor lock-in
- Scalable

**Cons:**
- Complex sync logic
- Conflict resolution needed
- Two data sources to manage

**Best for:** This project! (Open source, flexible, user-friendly)

---

## 4. Recommended Architecture

### Phase 2: Async Collaboration (Months 4-6)

**Implementation: Hybrid Local-First + Cloud Sync**

#### 4.1 Core Principles

1. **Local-First:** App works 100% offline
2. **Optional Sync:** Users opt-in to team collaboration
3. **Privacy:** Solo projects stay local, never sent to cloud
4. **Open Source:** Self-hostable backend

#### 4.2 Technical Stack

**Frontend (No changes):**
- React + TypeScript
- IndexedDB (primary storage)
- Service Worker (offline)

**Backend (New):**
- **Runtime:** Node.js 20+
- **Framework:** Fastify or Hono (fast, lightweight)
- **Database:** PostgreSQL 15+ (projects, users, teams)
- **Cache:** Redis (sessions, rate limiting, pub/sub)
- **Storage:** S3-compatible (project exports, assets)
- **Auth:** JWT + OAuth2 (GitHub, Google)
- **WebSocket:** Socket.io or native WebSocket

**Infrastructure:**
- **API Hosting:** Railway, Fly.io, or self-hosted
- **Database:** Supabase, Neon, or self-hosted PostgreSQL
- **Cache:** Upstash Redis or self-hosted
- **Storage:** Cloudflare R2, AWS S3, or MinIO

---

## 5. Technical Implementation

### 5.1 Data Models

#### User Model
```typescript
interface User {
  id: string;
  email: string;
  name: string;
  avatar?: string;
  role: 'admin' | 'user';
  createdAt: Date;
  updatedAt: Date;
}
```

#### Team Model
```typescript
interface Team {
  id: string;
  name: string;
  slug: string;
  ownerId: string;
  members: TeamMember[];
  plan: 'free' | 'pro' | 'enterprise';
  createdAt: Date;
  updatedAt: Date;
}

interface TeamMember {
  userId: string;
  role: 'owner' | 'admin' | 'editor' | 'viewer';
  joinedAt: Date;
}
```

#### Project Model (Extended)
```typescript
interface Project {
  // Existing fields
  id: string;
  version: string;
  metadata: ProjectMetadata;
  foundations: DesignFoundations;
  components: ComponentLibrary;
  // ... other fields

  // New collaboration fields
  teamId?: string;           // null = solo project
  ownerId: string;           // User who created it
  collaborators: Collaborator[];
  permissions: ProjectPermissions;
  syncStatus: 'local' | 'synced' | 'conflict';
  lastSyncedAt?: Date;
  versionHistory: ProjectVersion[];
  comments: Comment[];
}

interface Collaborator {
  userId: string;
  role: 'owner' | 'editor' | 'commenter' | 'viewer';
  lastSeenAt: Date;
}

interface ProjectVersion {
  id: string;
  projectId: string;
  version: number;
  changes: Change[];
  createdBy: string;
  createdAt: Date;
  message?: string;
}
```

### 5.2 API Endpoints

#### Authentication
```
POST   /api/auth/signup
POST   /api/auth/login
POST   /api/auth/logout
GET    /api/auth/me
POST   /api/auth/oauth/github
POST   /api/auth/oauth/google
```

#### Projects
```
GET    /api/projects              # List user's projects
POST   /api/projects              # Create new project
GET    /api/projects/:id          # Get project
PUT    /api/projects/:id          # Update project
DELETE /api/projects/:id          # Delete project
POST   /api/projects/:id/sync     # Sync local changes
GET    /api/projects/:id/versions # Version history
POST   /api/projects/:id/fork     # Fork project
```

#### Teams
```
GET    /api/teams                 # List user's teams
POST   /api/teams                 # Create team
GET    /api/teams/:id             # Get team
PUT    /api/teams/:id             # Update team
DELETE /api/teams/:id             # Delete team
POST   /api/teams/:id/members     # Add member
DELETE /api/teams/:id/members/:userId  # Remove member
```

#### Collaboration
```
GET    /api/projects/:id/collaborators
POST   /api/projects/:id/collaborators
DELETE /api/projects/:id/collaborators/:userId
GET    /api/projects/:id/comments
POST   /api/projects/:id/comments
PUT    /api/comments/:id
DELETE /api/comments/:id
```

#### Real-Time (WebSocket)
```
WS     /api/ws/projects/:id       # Real-time updates
```

### 5.3 Sync Logic

#### Sync Flow
```typescript
// Client-side sync service
class ProjectSyncService {
  async sync(projectId: string) {
    const localProject = await db.projects.get(projectId);

    // 1. Check if project has team
    if (!localProject.teamId) {
      return; // Solo project, no sync
    }

    // 2. Get server version
    const serverProject = await api.getProject(projectId);

    // 3. Compare versions
    if (localProject.version === serverProject.version) {
      return; // Already synced
    }

    // 4. Check for conflicts
    if (localProject.lastSyncedAt < serverProject.updatedAt) {
      // Server has newer changes
      if (localProject.updatedAt > localProject.lastSyncedAt) {
        // Local changes too - CONFLICT!
        await this.handleConflict(localProject, serverProject);
      } else {
        // No local changes, safe to pull
        await this.pullChanges(serverProject);
      }
    } else {
      // Local is newer, push changes
      await this.pushChanges(localProject);
    }
  }

  private async handleConflict(local, server) {
    // Show conflict UI
    // Let user choose: keep local, use server, or merge
  }
}
```

#### Auto-Sync Strategy
```typescript
// Sync every 30 seconds if online and project is shared
setInterval(async () => {
  if (navigator.onLine && currentProject.teamId) {
    await syncService.sync(currentProject.id);
  }
}, 30000);

// Sync on visibility change (user switches tabs)
document.addEventListener('visibilitychange', async () => {
  if (!document.hidden && currentProject.teamId) {
    await syncService.sync(currentProject.id);
  }
});

// Sync before closing
window.addEventListener('beforeunload', async (e) => {
  if (currentProject.teamId && hasUnsyncedChanges) {
    e.preventDefault();
    await syncService.sync(currentProject.id);
  }
});
```

---

## 6. Security & Permissions

### 6.1 Permission Levels

| Role | View | Comment | Edit | Delete | Manage Members | Manage Settings |
|------|------|---------|------|--------|----------------|-----------------|
| **Owner** | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Admin** | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| **Editor** | ✅ | ✅ | ✅ | ❌ | ❌ | ❌ |
| **Commenter** | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |
| **Viewer** | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ |

### 6.2 Access Control

```typescript
// Middleware for permission checking
async function requirePermission(
  req: Request,
  projectId: string,
  permission: 'view' | 'comment' | 'edit' | 'delete'
) {
  const user = req.user;
  const project = await db.projects.findOne({ id: projectId });

  // Check if user has access
  const collaborator = project.collaborators.find(
    c => c.userId === user.id
  );

  if (!collaborator) {
    throw new ForbiddenError('No access to this project');
  }

  // Check permission level
  const hasPermission = checkPermission(collaborator.role, permission);

  if (!hasPermission) {
    throw new ForbiddenError(`Requires ${permission} permission`);
  }

  return true;
}
```

### 6.3 Data Encryption

```typescript
// Encrypt sensitive project data before sending to server
class EncryptionService {
  async encryptProject(project: Project, teamKey: string) {
    const encrypted = await crypto.subtle.encrypt(
      { name: 'AES-GCM', iv: generateIV() },
      teamKey,
      JSON.stringify(project)
    );
    return encrypted;
  }

  async decryptProject(encrypted: ArrayBuffer, teamKey: string) {
    const decrypted = await crypto.subtle.decrypt(
      { name: 'AES-GCM', iv: getIV() },
      teamKey,
      encrypted
    );
    return JSON.parse(new TextDecoder().decode(decrypted));
  }
}
```

---

## 7. Data Synchronization

### 7.1 Operational Transformation (OT)

For real-time collaboration, we use Operational Transformation to handle concurrent edits:

```typescript
// Simple OT for color changes
class ColorOperation {
  type: 'set-color' | 'delete-color' | 'update-shade';
  path: string; // e.g., "primary.500"
  value: string;
  timestamp: number;
  userId: string;
}

function transform(op1: ColorOperation, op2: ColorOperation) {
  // If both operations affect same path
  if (op1.path === op2.path) {
    // Last write wins (timestamp-based)
    return op1.timestamp > op2.timestamp ? op1 : op2;
  }
  // Different paths, both can apply
  return [op1, op2];
}
```

### 7.2 Conflict Resolution Strategies

**1. Last Write Wins (Simple)**
```typescript
// Newest change wins based on timestamp
const resolvedValue = localTimestamp > serverTimestamp
  ? localValue
  : serverValue;
```

**2. User Choice (Interactive)**
```typescript
// Show conflict modal
<ConflictResolutionModal>
  <div>Local: {localValue}</div>
  <div>Server: {serverValue}</div>
  <Button onClick={() => resolve('local')}>Keep Mine</Button>
  <Button onClick={() => resolve('server')}>Use Theirs</Button>
  <Button onClick={() => resolve('merge')}>Merge</Button>
</ConflictResolutionModal>
```

**3. Automatic Merge (Smart)**
```typescript
// Non-conflicting changes merged automatically
function autoMerge(local: Project, server: Project) {
  return {
    ...server,
    foundations: {
      colors: mergeColors(local.foundations.colors, server.foundations.colors),
      typography: local.foundations.typography, // No conflict
      spacing: server.foundations.spacing,
    }
  };
}
```

---

## 8. Conflict Resolution

### 8.1 Conflict Detection

```typescript
interface Conflict {
  field: string;
  localValue: any;
  serverValue: any;
  localTimestamp: Date;
  serverTimestamp: Date;
  resolution?: 'local' | 'server' | 'merge';
}

function detectConflicts(local: Project, server: Project): Conflict[] {
  const conflicts: Conflict[] = [];

  // Compare each field
  Object.keys(local).forEach(key => {
    if (JSON.stringify(local[key]) !== JSON.stringify(server[key])) {
      conflicts.push({
        field: key,
        localValue: local[key],
        serverValue: server[key],
        localTimestamp: local.updatedAt,
        serverTimestamp: server.updatedAt
      });
    }
  });

  return conflicts;
}
```

### 8.2 Conflict UI

```tsx
function ConflictResolver({ conflicts, onResolve }) {
  const [resolutions, setResolutions] = useState({});

  return (
    <Modal title="Resolve Conflicts">
      <p>Changes detected from other team members. Please resolve conflicts:</p>

      {conflicts.map(conflict => (
        <ConflictItem key={conflict.field}>
          <h3>{conflict.field}</h3>

          <div className="choice">
            <Radio
              checked={resolutions[conflict.field] === 'local'}
              onChange={() => setResolution(conflict.field, 'local')}
            />
            <div>
              <strong>Your version</strong>
              <pre>{JSON.stringify(conflict.localValue, null, 2)}</pre>
            </div>
          </div>

          <div className="choice">
            <Radio
              checked={resolutions[conflict.field] === 'server'}
              onChange={() => setResolution(conflict.field, 'server')}
            />
            <div>
              <strong>Team version</strong>
              <pre>{JSON.stringify(conflict.serverValue, null, 2)}</pre>
            </div>
          </div>
        </ConflictItem>
      ))}

      <Button onClick={() => onResolve(resolutions)}>
        Resolve All
      </Button>
    </Modal>
  );
}
```

---

## 9. Implementation Phases

### Phase 2.1: User Accounts (Month 4)
- [ ] Authentication system (email + OAuth)
- [ ] User profiles
- [ ] Cloud project storage
- [ ] Manual sync (save to cloud button)

### Phase 2.2: Team Features (Month 5)
- [ ] Create teams
- [ ] Invite members
- [ ] Share projects with team
- [ ] Role-based permissions
- [ ] View-only mode

### Phase 2.3: Collaboration (Month 6)
- [ ] Auto-sync every 30s
- [ ] Conflict detection
- [ ] Conflict resolution UI
- [ ] Activity feed
- [ ] Basic commenting

### Phase 3.1: Real-Time Collaboration (Month 7-9)
- [ ] WebSocket implementation
- [ ] Live cursors
- [ ] Real-time updates
- [ ] Presence indicators
- [ ] Live commenting

### Phase 3.2: Advanced Features (Month 10-12)
- [ ] Version history timeline
- [ ] Branching (like Git)
- [ ] Design proposals
- [ ] Approval workflows
- [ ] Audit logs

---

## 10. Cost Considerations

### Infrastructure Costs (Estimated Monthly)

**Free Tier (Up to 5 teams):**
- Frontend: Vercel/Netlify Free - $0
- Backend: Railway/Fly.io Free tier - $0
- Database: Supabase Free tier - $0
- Storage: Cloudflare R2 Free tier - $0
- **Total: $0/month**

**Paid Tier (Up to 100 teams):**
- Frontend: Vercel Pro - $20
- Backend: Railway Pro - $20
- Database: Supabase Pro - $25
- Redis: Upstash - $10
- Storage: Cloudflare R2 - $5
- **Total: ~$80/month**

**Enterprise (Unlimited):**
- Custom infrastructure
- Self-hosted option available
- **Total: ~$200-500/month**

---

## 11. Self-Hosting Option

For privacy-conscious organizations:

```yaml
# docker-compose.yml
version: '3.8'
services:
  frontend:
    image: design-system-framework/frontend
    ports:
      - "3000:3000"

  backend:
    image: design-system-framework/backend
    environment:
      DATABASE_URL: postgres://...
      REDIS_URL: redis://...
    ports:
      - "4000:4000"

  database:
    image: postgres:15
    volumes:
      - pgdata:/var/lib/postgresql/data

  redis:
    image: redis:7
    volumes:
      - redisdata:/data
```

Run with: `docker-compose up -d`

---

## 12. Migration Path

### From Solo to Team

```typescript
// Convert solo project to team project
async function convertToTeamProject(projectId: string, teamId: string) {
  // 1. Upload local project to cloud
  const localProject = await db.projects.get(projectId);

  // 2. Create on server
  const serverProject = await api.createProject({
    ...localProject,
    teamId,
    ownerId: currentUser.id,
  });

  // 3. Link local project to server
  await db.projects.update(projectId, {
    teamId,
    serverId: serverProject.id,
    syncStatus: 'synced',
    lastSyncedAt: new Date(),
  });

  // 4. Enable auto-sync
  syncService.startAutoSync(projectId);
}
```

---

## Document History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0.0 | 2025-11-05 | Claude | Initial collaboration architecture |

---

**Next Steps:**
1. Validate architecture with stakeholders
2. Create backend API specification
3. Design database schema
4. Build MVP of sync service
5. Test with small team
