# Contributing to Differential ☉

Thank you for considering contributing to Differential! This document provides guidelines and instructions for contributing.

## 🎯 Code of Conduct

By participating in this project, you agree to maintain a respectful and inclusive environment for everyone.

## 🚀 Getting Started

### Prerequisites

- [Bun](https://bun.sh) >= 1.0
- [Rust](https://www.rust-lang.org/) >= 1.70
- [Node.js](https://nodejs.org/) >= 18
- Basic knowledge of TypeScript and React
- Familiarity with Rust is helpful but not required

### Setting Up Development Environment

1. **Fork the repository**

   Click the "Fork" button at the top right of the repository page.

2. **Clone your fork**

   ```bash
   git clone https://github.com/YOUR_USERNAME/differential.git
   cd differential
   ```

3. **Install dependencies**

   ```bash
   bun install
   ```

4. **Run the development server**

   ```bash
   bun run tauri dev
   ```

## 📝 Development Workflow

### Branch Naming Convention

- `feature/` - New features (e.g., `feature/add-git-integration`)
- `fix/` - Bug fixes (e.g., `fix/scrolling-sync-issue`)
- `docs/` - Documentation updates (e.g., `docs/update-api-reference`)
- `refactor/` - Code refactoring (e.g., `refactor/diff-engine`)
- `test/` - Adding tests (e.g., `test/add-unit-tests`)

### Making Changes

1. **Create a new branch**

   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes**

   - Write clean, readable code
   - Follow existing code style
   - Add comments for complex logic
   - Update documentation if needed

3. **Test your changes**

   ```bash
   # Run the app in dev mode
   bun run tauri dev

   # Run tests (when available)
   bun test

   # Check TypeScript types
   bun run tsc --noEmit

   # Format code
   bun run format
   ```

4. **Commit your changes**

   Write clear, descriptive commit messages:

   ```bash
   git commit -m "feat: add syntax highlighting toggle"
   git commit -m "fix: resolve memory leak in diff engine"
   git commit -m "docs: update installation instructions"
   ```

   Follow [Conventional Commits](https://www.conventionalcommits.org/):
   - `feat:` - New features
   - `fix:` - Bug fixes
   - `docs:` - Documentation changes
   - `style:` - Code style changes (formatting, etc.)
   - `refactor:` - Code refactoring
   - `test:` - Adding tests
   - `chore:` - Maintenance tasks

5. **Push to your fork**

   ```bash
   git push origin feature/your-feature-name
   ```

6. **Open a Pull Request**

   - Go to the original repository
   - Click "New Pull Request"
   - Select your branch
   - Fill out the PR template with details

## 🏗️ Project Structure

```text
differential/
├── src/                    # React frontend (TypeScript)
│   ├── components/         # React components
│   ├── hooks/              # Custom React hooks
│   ├── types/              # TypeScript type definitions
│   ├── utils/              # Utility functions
│   └── App.tsx             # Main component
├── src-tauri/              # Rust backend
│   ├── src/
│   │   ├── commands/       # Tauri command handlers
│   │   ├── diff/           # Diff engine logic
│   │   ├── lib.rs          # Library entry point
│   │   └── main.rs         # Application entry point
│   └── Cargo.toml          # Rust dependencies
├── docs/                   # Documentation
└── .github/                # GitHub workflows and templates
```

## 🎨 Code Style Guidelines

### TypeScript/React

- Use functional components with hooks
- Prefer `const` over `let`
- Use meaningful variable names
- Add JSDoc comments for complex functions
- Keep components small and focused
- Use TypeScript strict mode

```typescript
// Good
const DiffViewer: React.FC<DiffViewerProps> = ({ leftFile, rightFile }) => {
  const [isLoading, setIsLoading] = useState(false);
  
  // Component logic...
};

// Avoid
function DiffViewer(props: any) {
  var loading = false;
  // ...
}
```

### Rust

- Follow [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/)
- Use `rustfmt` for formatting
- Use `clippy` for linting
- Add documentation comments for public APIs
- Handle errors explicitly (no unwrap in production)

```rust
// Good
/// Computes the diff between two text strings
pub fn compute_diff(left: &str, right: &str) -> Result<DiffResult, DiffError> {
    // Implementation...
}

// Avoid
pub fn compute_diff(left: &str, right: &str) -> DiffResult {
    // .unwrap() everywhere...
}
```

## 🧪 Testing

- Write unit tests for new functionality
- Test edge cases and error conditions
- Ensure tests pass before submitting PR

```typescript
// Frontend tests (future)
describe('DiffEngine', () => {
  it('should compute diff correctly', () => {
    // Test implementation
  });
});
```

```rust
// Rust tests
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_compute_diff() {
        let result = compute_diff("hello", "hallo");
        assert!(result.is_ok());
    }
}
```

## 📚 Documentation

- Update README.md if you add new features
- Add inline comments for complex logic
- Update API documentation for public functions
- Create examples for new functionality

## 🐛 Reporting Bugs

When reporting bugs, please include:

1. **Description** - Clear description of the issue
2. **Steps to Reproduce** - Exact steps to reproduce the bug
3. **Expected Behavior** - What should happen
4. **Actual Behavior** - What actually happens
5. **Environment** - OS, Rust version, Bun version
6. **Screenshots** - If applicable
7. **Error Logs** - Console output or error messages

## 💡 Suggesting Features

Feature requests are welcome! Please include:

1. **Use Case** - Why is this feature needed?
2. **Proposed Solution** - How should it work?
3. **Alternatives** - Other solutions you've considered
4. **Examples** - Mock-ups or examples from other tools

## ✅ Pull Request Checklist

Before submitting a PR, ensure:

- [ ] Code follows the project's style guidelines
- [ ] All tests pass
- [ ] New code has appropriate tests
- [ ] Documentation is updated
- [ ] Commit messages follow conventions
- [ ] No linting errors
- [ ] PR description clearly explains changes

## 🤝 Community

- Be respectful and inclusive
- Help others learn and grow
- Give constructive feedback
- Celebrate contributions of all sizes

## 📬 Questions?

If you have questions, feel free to:

- Open an issue for discussion
- Reach out to the maintainers
- Check existing issues and PRs

Thank you for contributing to Differential! 🎉
