# Contributing to Design System Framework

Thank you for your interest in contributing to the Design System Framework! We welcome contributions from the community and are grateful for any help you can provide.

## Table of Contents
- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [How Can I Contribute?](#how-can-i-contribute)
- [Development Workflow](#development-workflow)
- [Code Guidelines](#code-guidelines)
- [Commit Guidelines](#commit-guidelines)
- [Pull Request Process](#pull-request-process)
- [Community](#community)

---

## Code of Conduct

This project and everyone participating in it is governed by our Code of Conduct. By participating, you are expected to uphold this code. Please report unacceptable behavior to [conduct@uxvision.pro](mailto:conduct@uxvision.pro).

### Our Standards

**Examples of behavior that contributes to a positive environment:**
- Using welcoming and inclusive language
- Being respectful of differing viewpoints and experiences
- Gracefully accepting constructive criticism
- Focusing on what is best for the community
- Showing empathy towards other community members

**Examples of unacceptable behavior:**
- The use of sexualized language or imagery and unwelcome sexual attention
- Trolling, insulting/derogatory comments, and personal or political attacks
- Public or private harassment
- Publishing others' private information without explicit permission
- Other conduct which could reasonably be considered inappropriate

---

## Getting Started

### Prerequisites

Before you begin, ensure you have the following installed:
- **Node.js** 20.x or higher (LTS recommended)
- **npm** 10.x or **pnpm** 8.x (pnpm preferred)
- **Git** 2.x or higher
- A code editor (VS Code recommended)

### Development Setup

1. **Fork the repository** on GitHub
2. **Clone your fork** locally:
   ```bash
   git clone https://github.com/YOUR_USERNAME/design-system-framework.git
   cd design-system-framework
   ```

3. **Add upstream remote:**
   ```bash
   git remote add upstream https://github.com/antonpme/design-system-framework.git
   ```

4. **Install dependencies:**
   ```bash
   npm install
   # or
   pnpm install
   ```

5. **Start the development server:**
   ```bash
   npm run dev
   ```

6. **Open your browser** to `http://localhost:5173`

For detailed setup instructions, see [docs/setup.md](./docs/setup.md).

---

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check the existing issues to avoid duplicates. When you create a bug report, include as many details as possible:

**Use this template:**

```markdown
**Describe the bug**
A clear and concise description of what the bug is.

**To Reproduce**
Steps to reproduce the behavior:
1. Go to '...'
2. Click on '...'
3. Scroll down to '...'
4. See error

**Expected behavior**
A clear description of what you expected to happen.

**Screenshots**
If applicable, add screenshots to help explain your problem.

**Environment:**
 - OS: [e.g. macOS 13.0]
 - Browser: [e.g. Chrome 120]
 - Version: [e.g. 1.0.0]

**Additional context**
Add any other context about the problem here.
```

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion, include:

- **Clear title** and description
- **Use case:** Why is this enhancement needed?
- **Proposed solution:** How should it work?
- **Alternatives considered:** Other approaches you've thought about
- **Additional context:** Mockups, examples, etc.

### Contributing Code

We welcome code contributions! Here's how:

1. **Find or create an issue** for the feature/bug
2. **Comment on the issue** to let others know you're working on it
3. **Fork and create a branch** from `develop`
4. **Make your changes** following our code guidelines
5. **Write tests** for your changes
6. **Ensure all tests pass** and code is linted
7. **Submit a pull request**

### Contributing Content

We need help creating guidelines, templates, and case studies!

**Types of content:**
- **Guidelines:** Best practices, standards, principles
- **Templates:** Reusable documents and files
- **Patterns:** Common design solutions
- **Case Studies:** Real-world examples

**Content contribution process:**
1. Create content in `content/` directory
2. Follow the content template structure
3. Include frontmatter metadata
4. Add images to appropriate folders
5. Submit PR with content

**Content template:**

```markdown
---
title: "Your Title Here"
slug: "your-slug"
category: "your-category"
difficulty: "beginner|intermediate|advanced"
platforms: ["web", "mobile"]
datePublished: "2025-11-05"
author: "Your Name"
tags: ["tag1", "tag2"]
---

# Your Title

Brief introduction...

## Section 1

Content here...

## Examples

Provide examples...

## Best Practices

- Practice 1
- Practice 2

## Related Resources

- [Link 1](#)
- [Link 2](#)
```

### Contributing Documentation

Documentation improvements are always welcome:

- Fix typos, grammar, or formatting
- Clarify confusing sections
- Add missing documentation
- Create tutorials or guides
- Improve code examples

---

## Development Workflow

### Branching Strategy

We use Git Flow:

- **`main`** - Production-ready code
- **`develop`** - Integration branch for features
- **`feature/*`** - Feature branches
- **`fix/*`** - Bug fix branches
- **`docs/*`** - Documentation updates

### Creating a Branch

```bash
# Update your local develop branch
git checkout develop
git pull upstream develop

# Create a feature branch
git checkout -b feature/your-feature-name

# Or for bug fixes
git checkout -b fix/bug-description
```

### Making Changes

1. **Make your changes** in your branch
2. **Test your changes** thoroughly
3. **Run linting:**
   ```bash
   npm run lint
   ```
4. **Run tests:**
   ```bash
   npm run test
   ```
5. **Build to verify:**
   ```bash
   npm run build
   ```

### Keeping Your Branch Updated

```bash
# Fetch upstream changes
git fetch upstream

# Rebase your branch on develop
git checkout feature/your-feature-name
git rebase upstream/develop

# If conflicts, resolve them and continue
git rebase --continue

# Force push to your fork (if needed)
git push origin feature/your-feature-name --force-with-lease
```

---

## Code Guidelines

### TypeScript

- **Use TypeScript** for all new code
- **Enable strict mode** (already configured)
- **Define types** explicitly; avoid `any`
- **Use interfaces** for object shapes
- **Export types** from dedicated `.types.ts` files

```typescript
// Good
interface ButtonProps {
  variant: 'primary' | 'secondary';
  onClick: () => void;
  children: React.ReactNode;
}

export const Button: React.FC<ButtonProps> = ({ variant, onClick, children }) => {
  // ...
};

// Bad
export const Button = (props: any) => {
  // ...
};
```

### React Components

- **Use functional components** with hooks
- **Prefer named exports** over default exports
- **Keep components small** (< 200 lines)
- **Extract logic** to custom hooks
- **Use TypeScript** for props

```typescript
// Good
export const MyComponent: React.FC<MyComponentProps> = ({ title, onAction }) => {
  const [state, setState] = useState('');

  return (
    <div>
      <h1>{title}</h1>
      <button onClick={onAction}>Action</button>
    </div>
  );
};

// Bad
export default function MyComponent(props) {
  return <div>...</div>;
}
```

### File Organization

```
Component/
├── Component.tsx          # Component implementation
├── Component.types.ts     # TypeScript types
├── Component.module.css   # Styles (if needed)
├── Component.test.tsx     # Tests
└── index.ts               # Barrel export
```

### Naming Conventions

- **Components:** PascalCase (`Button`, `FormField`)
- **Files:** Match component name (`Button.tsx`)
- **Hooks:** camelCase with `use` prefix (`useAuth`, `useWizard`)
- **Constants:** UPPER_SNAKE_CASE (`MAX_WIDTH`, `API_URL`)
- **Functions:** camelCase (`handleClick`, `formatDate`)
- **Types/Interfaces:** PascalCase (`UserData`, `ApiResponse`)

### Styling

- **Use Tailwind CSS** for utility classes
- **Use CSS Modules** for component-specific styles
- **Follow BEM** for custom CSS classes
- **Use semantic color names** from design tokens
- **Ensure responsive** design

```tsx
// Good
<button className="px-4 py-2 bg-primary-600 text-white rounded-lg hover:bg-primary-700">
  Click me
</button>

// Also good (with CSS Modules)
<button className={styles.button}>Click me</button>
```

### Testing

- **Write tests** for all new features
- **Aim for 80%+ coverage** minimum
- **Test behavior,** not implementation
- **Use Testing Library** best practices

```typescript
// Good
describe('Button', () => {
  it('calls onClick when clicked', () => {
    const handleClick = vi.fn();
    const { getByText } = render(<Button onClick={handleClick}>Click</Button>);

    fireEvent.click(getByText('Click'));

    expect(handleClick).toHaveBeenCalledOnce();
  });
});
```

### Accessibility

- **Use semantic HTML**
- **Add ARIA labels** where needed
- **Ensure keyboard navigation**
- **Test with screen readers**
- **Maintain color contrast** (WCAG AA minimum)

```tsx
// Good
<button
  aria-label="Close dialog"
  onClick={onClose}
>
  <XIcon />
</button>

// Bad
<div onClick={onClose}>
  <XIcon />
</div>
```

---

## Commit Guidelines

We follow [Conventional Commits](https://www.conventionalcommits.org/) specification.

### Commit Message Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

- **feat:** New feature
- **fix:** Bug fix
- **docs:** Documentation changes
- **style:** Code style changes (formatting, semicolons, etc.)
- **refactor:** Code refactoring
- **test:** Adding or updating tests
- **chore:** Maintenance tasks
- **perf:** Performance improvements

### Examples

```bash
# Feature
git commit -m "feat(wizard): add color palette generator"

# Bug fix
git commit -m "fix(export): resolve CSS export formatting issue"

# Documentation
git commit -m "docs(contributing): update setup instructions"

# Breaking change
git commit -m "feat(api)!: change export format structure

BREAKING CHANGE: Export format now uses nested structure"
```

### Commit Best Practices

- **One logical change** per commit
- **Write clear messages** that explain "why", not "what"
- **Keep first line** under 72 characters
- **Add body** for complex changes
- **Reference issues** in footer (`Fixes #123`)

---

## Pull Request Process

### Before Submitting

- [ ] Code follows style guidelines
- [ ] Tests pass (`npm run test`)
- [ ] Linting passes (`npm run lint`)
- [ ] Build succeeds (`npm run build`)
- [ ] Documentation updated (if needed)
- [ ] Self-review completed
- [ ] Branch is up-to-date with `develop`

### PR Template

When creating a PR, fill out the template:

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Refactoring
- [ ] Performance improvement

## Related Issue
Fixes #(issue number)

## How Has This Been Tested?
Description of testing

## Screenshots (if applicable)
Add screenshots

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex code
- [ ] Documentation updated
- [ ] No new warnings
- [ ] Tests added/updated
- [ ] All tests pass
```

### PR Review Process

1. **Automated checks** must pass (CI/CD)
2. **Code review** by maintainer(s)
3. **Requested changes** addressed
4. **Final approval** from maintainer
5. **Squash and merge** to develop

### Review Timeline

- Small PRs (< 100 lines): 1-2 days
- Medium PRs (100-500 lines): 3-5 days
- Large PRs (> 500 lines): 1-2 weeks

**Tip:** Smaller PRs get reviewed faster!

---

## Community

### Communication Channels

- **GitHub Discussions:** General questions and ideas
- **GitHub Issues:** Bug reports and feature requests
- **Discord:** Real-time chat (coming soon)
- **Twitter:** [@uxvisionpro](https://twitter.com/uxvisionpro)

### Getting Help

- Check existing documentation
- Search closed issues
- Ask in GitHub Discussions
- Reach out to maintainers

### Recognition

We value all contributions! Contributors will be:
- Listed in our README
- Mentioned in release notes
- Featured in monthly highlights (with permission)

---

## Development Resources

### Useful Documentation

- [React Documentation](https://react.dev)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [Tailwind CSS](https://tailwindcss.com/docs)
- [Vitest](https://vitest.dev/)
- [Testing Library](https://testing-library.com/)

### Tools We Use

- **VS Code** - Recommended editor
- **Prettier** - Code formatting
- **ESLint** - Code linting
- **Vitest** - Unit testing
- **Playwright** - E2E testing

### Recommended VS Code Extensions

```json
{
  "recommendations": [
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "bradlc.vscode-tailwindcss",
    "ms-playwright.playwright"
  ]
}
```

---

## Questions?

If you have questions about contributing, please:

1. Check this document thoroughly
2. Search existing issues and discussions
3. Create a new discussion if needed

---

## License

By contributing to Design System Framework, you agree that your contributions will be licensed under the Apache License 2.0.

---

**Thank you for contributing to Design System Framework! 🎉**

Every contribution, no matter how small, helps make this project better for everyone.
