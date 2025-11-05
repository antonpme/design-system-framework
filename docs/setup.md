# Development Setup Guide
## Design System Framework

This guide will help you set up your development environment for contributing to the Design System Framework.

---

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Installation](#installation)
3. [Configuration](#configuration)
4. [Running the Project](#running-the-project)
5. [Development Tools](#development-tools)
6. [Troubleshooting](#troubleshooting)
7. [Next Steps](#next-steps)

---

## Prerequisites

### Required Software

Before you begin, ensure you have the following installed on your system:

#### 1. Node.js (v20+ LTS)

**Check if installed:**
```bash
node --version  # Should be v20.0.0 or higher
```

**Installation:**
- **macOS:** `brew install node@20`
- **Windows:** Download from [nodejs.org](https://nodejs.org/)
- **Linux:**
  ```bash
  curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
  sudo apt-get install -y nodejs
  ```

**Recommended:** Use [nvm](https://github.com/nvm-sh/nvm) for Node version management:
```bash
# Install nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# Install and use Node 20
nvm install 20
nvm use 20
```

#### 2. Package Manager

**npm** (comes with Node.js):
```bash
npm --version  # Should be v10.0.0 or higher
```

**pnpm** (recommended for faster installs):
```bash
# Install pnpm globally
npm install -g pnpm

# Verify installation
pnpm --version
```

#### 3. Git

**Check if installed:**
```bash
git --version  # Should be v2.0.0 or higher
```

**Installation:**
- **macOS:** `brew install git` or comes with Xcode
- **Windows:** Download from [git-scm.com](https://git-scm.com/)
- **Linux:** `sudo apt-get install git`

**Configure Git:**
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### Optional but Recommended

#### VS Code
Download from [code.visualstudio.com](https://code.visualstudio.com/)

#### Browser DevTools
- Chrome DevTools
- React Developer Tools extension
- Redux DevTools extension (for state debugging)

---

## Installation

### 1. Fork and Clone

**Fork the repository:**
1. Go to [https://github.com/antonpme/design-system-framework](https://github.com/antonpme/design-system-framework)
2. Click the "Fork" button in the top right
3. Select your GitHub account

**Clone your fork:**
```bash
# Clone via HTTPS
git clone https://github.com/YOUR_USERNAME/design-system-framework.git

# OR clone via SSH (if you have SSH keys set up)
git clone git@github.com:YOUR_USERNAME/design-system-framework.git

# Navigate into the directory
cd design-system-framework
```

### 2. Add Upstream Remote

This allows you to sync with the main repository:

```bash
git remote add upstream https://github.com/antonpme/design-system-framework.git

# Verify remotes
git remote -v
# Should show:
# origin    https://github.com/YOUR_USERNAME/design-system-framework.git (fetch)
# origin    https://github.com/YOUR_USERNAME/design-system-framework.git (push)
# upstream  https://github.com/antonpme/design-system-framework.git (fetch)
# upstream  https://github.com/antonpme/design-system-framework.git (push)
```

### 3. Install Dependencies

**Using npm:**
```bash
npm install
```

**Using pnpm (recommended):**
```bash
pnpm install
```

This will install all required dependencies defined in `package.json`.

**Expected output:**
- 200+ packages installed
- Installation should complete in 1-3 minutes
- No errors (warnings are okay)

---

## Configuration

### 1. Environment Variables

Create a `.env` file in the project root:

```bash
cp .env.example .env
```

Edit `.env` with your configuration:

```bash
# .env
# Application
VITE_APP_NAME="Design System Framework"
VITE_APP_VERSION="1.0.0"

# API (optional, for future use)
# VITE_API_URL=http://localhost:3000

# Analytics (optional)
# VITE_ANALYTICS_ID=your-analytics-id

# Development
VITE_DEV_MODE=true
```

**Note:** `.env` files are git-ignored and never committed.

### 2. VS Code Setup (Recommended)

**Install recommended extensions:**

When you open the project in VS Code, you'll be prompted to install recommended extensions. Alternatively, install manually:

1. **ESLint** (`dbaeumer.vscode-eslint`)
2. **Prettier** (`esbenp.prettier-vscode`)
3. **Tailwind CSS IntelliSense** (`bradlc.vscode-tailwindcss`)
4. **TypeScript Vue Plugin** (if using Vue)

**VS Code settings:**

The project includes `.vscode/settings.json` with:
- Auto-format on save
- ESLint auto-fix
- Correct file associations

### 3. Git Hooks

We use Husky for git hooks (pre-commit, pre-push):

```bash
# Hooks are installed automatically with npm install
# If needed, manually install:
npx husky install
```

**Pre-commit hook:**
- Runs Prettier (formatting)
- Runs ESLint (linting)
- Runs type checking

**Pre-push hook:**
- Runs tests
- Runs build verification

---

## Running the Project

### Development Server

**Start the development server:**

```bash
npm run dev
# or
pnpm dev
```

**Expected output:**
```
  VITE v5.0.0  ready in 1234 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h to show help
```

**Open your browser:**
Navigate to `http://localhost:5173`

**Features:**
- ⚡️ Hot Module Replacement (HMR) - instant updates
- 🔥 Fast refresh - state preserved during edits
- 📝 TypeScript checking
- 🎨 Tailwind CSS compilation

### Building for Production

**Create a production build:**

```bash
npm run build
# or
pnpm build
```

**Output:**
- Built files in `dist/` directory
- Optimized bundles
- Source maps generated

**Preview the production build:**

```bash
npm run preview
# or
pnpm preview
```

Opens at `http://localhost:4173`

### Running Tests

**Run all tests:**
```bash
npm run test
# or
pnpm test
```

**Run tests in watch mode:**
```bash
npm run test:watch
```

**Run tests with coverage:**
```bash
npm run test:coverage
```

**Run E2E tests:**
```bash
npm run test:e2e
```

### Linting and Formatting

**Lint code:**
```bash
npm run lint
```

**Lint and auto-fix:**
```bash
npm run lint:fix
```

**Format code:**
```bash
npm run format
```

**Type checking:**
```bash
npm run type-check
```

---

## Development Tools

### Available npm Scripts

| Command | Description |
|---------|-------------|
| `npm run dev` | Start development server |
| `npm run build` | Build for production |
| `npm run preview` | Preview production build |
| `npm run test` | Run tests |
| `npm run test:watch` | Run tests in watch mode |
| `npm run test:coverage` | Run tests with coverage report |
| `npm run test:e2e` | Run E2E tests with Playwright |
| `npm run lint` | Lint code with ESLint |
| `npm run lint:fix` | Lint and auto-fix issues |
| `npm run format` | Format code with Prettier |
| `npm run type-check` | Check TypeScript types |
| `npm run clean` | Clean build artifacts |

### Browser DevTools

**React Developer Tools:**
1. Install from Chrome Web Store
2. Open DevTools (F12)
3. Find "Components" and "Profiler" tabs

**Key features:**
- Inspect component tree
- View props and state
- Profile performance
- Track component re-renders

### Debugging

**VS Code Debugging:**

Press F5 or use the Debug panel. The launch configuration is in `.vscode/launch.json`:

```json
{
  "type": "chrome",
  "request": "launch",
  "name": "Launch Chrome against localhost",
  "url": "http://localhost:5173",
  "webRoot": "${workspaceFolder}/src"
}
```

**Console debugging:**
```typescript
// Use debug utility
import { debug } from '@/utils/debug';

debug.log('Component mounted', { props, state });
```

---

## Troubleshooting

### Common Issues

#### Issue: Port 5173 already in use

**Solution:**
```bash
# Kill the process using the port
# macOS/Linux
lsof -ti:5173 | xargs kill -9

# Windows
netstat -ano | findstr :5173
taskkill /PID <PID> /F

# Or change the port in vite.config.ts
```

#### Issue: Module not found errors

**Solution:**
```bash
# Clear node_modules and reinstall
rm -rf node_modules package-lock.json
npm install

# Or with pnpm
rm -rf node_modules pnpm-lock.yaml
pnpm install
```

#### Issue: TypeScript errors

**Solution:**
```bash
# Restart TypeScript server in VS Code
# Command Palette (Cmd+Shift+P) > "TypeScript: Restart TS Server"

# Or rebuild TypeScript
npm run type-check
```

#### Issue: Tests failing

**Solution:**
```bash
# Clear test cache
npm run test -- --clearCache

# Update snapshots
npm run test -- -u
```

#### Issue: Git hooks not running

**Solution:**
```bash
# Reinstall husky
npx husky install

# Make hooks executable
chmod +x .husky/*
```

#### Issue: Slow npm install

**Solution:**
```bash
# Use pnpm for faster installs
npm install -g pnpm
pnpm install

# Or use npm ci for clean install
npm ci
```

### Getting Help

If you encounter issues not covered here:

1. **Check existing issues:** [GitHub Issues](https://github.com/antonpme/design-system-framework/issues)
2. **Search documentation:** Check other docs in `/docs`
3. **Ask in discussions:** [GitHub Discussions](https://github.com/antonpme/design-system-framework/discussions)
4. **Create an issue:** Provide details about your environment and error

---

## Project Structure

```
design-system-framework/
├── .github/              # GitHub configuration
│   └── workflows/        # CI/CD workflows
├── .husky/               # Git hooks
├── .vscode/              # VS Code settings
├── docs/                 # Documentation
├── public/               # Static assets
├── src/                  # Source code
│   ├── app/              # Application root
│   ├── modules/          # Feature modules
│   ├── shared/           # Shared components
│   ├── lib/              # Core libraries
│   ├── assets/           # Source assets
│   └── main.tsx          # Entry point
├── tests/                # Test files
├── .env.example          # Environment template
├── .gitignore            # Git ignore rules
├── package.json          # Dependencies and scripts
├── tsconfig.json         # TypeScript config
├── vite.config.ts        # Vite configuration
├── tailwind.config.js    # Tailwind CSS config
├── vitest.config.ts      # Vitest test config
└── README.md             # Project overview
```

---

## Next Steps

Now that your environment is set up:

1. **Read the documentation:**
   - [CONTRIBUTING.md](../CONTRIBUTING.md) - Contribution guidelines
   - [docs/architecture.md](./architecture.md) - System architecture
   - [docs/prd.md](./prd.md) - Product requirements

2. **Explore the codebase:**
   - Browse the `src/` directory
   - Look at existing components
   - Review test examples

3. **Find an issue to work on:**
   - Check [good first issues](https://github.com/antonpme/design-system-framework/labels/good%20first%20issue)
   - Comment on an issue to claim it
   - Ask questions if needed

4. **Make your first contribution:**
   - Create a feature branch
   - Make your changes
   - Write tests
   - Submit a pull request

5. **Join the community:**
   - Star the repository
   - Watch for updates
   - Engage in discussions

---

## Quick Reference

### Essential Commands

```bash
# Setup
git clone https://github.com/YOUR_USERNAME/design-system-framework.git
cd design-system-framework
npm install

# Development
npm run dev          # Start dev server
npm run test:watch   # Run tests in watch mode
npm run lint:fix     # Fix linting issues

# Before committing
npm run lint         # Check linting
npm run type-check   # Check types
npm run test         # Run tests
npm run build        # Verify build

# Git workflow
git checkout -b feature/my-feature
# ... make changes ...
git add .
git commit -m "feat: add new feature"
git push origin feature/my-feature
```

### Keyboard Shortcuts (VS Code)

| Shortcut | Action |
|----------|--------|
| `Cmd/Ctrl + P` | Quick open file |
| `Cmd/Ctrl + Shift + P` | Command palette |
| `Cmd/Ctrl + B` | Toggle sidebar |
| `Cmd/Ctrl + J` | Toggle terminal |
| `F5` | Start debugging |
| `Cmd/Ctrl + Shift + F` | Search in files |

---

## System Requirements

### Minimum Requirements
- **CPU:** 2 cores
- **RAM:** 4 GB
- **Disk:** 2 GB free space
- **OS:** macOS 10.15+, Windows 10+, Ubuntu 20.04+

### Recommended Requirements
- **CPU:** 4+ cores
- **RAM:** 8+ GB
- **Disk:** 5+ GB free space (SSD)
- **OS:** macOS 12+, Windows 11, Ubuntu 22.04+

---

**You're all set! Happy coding! 🚀**

If you have any questions, don't hesitate to ask in [GitHub Discussions](https://github.com/antonpme/design-system-framework/discussions).
