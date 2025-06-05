# Contributing to Forbid Files Action

Thank you for your interest in contributing to the Forbid Files Action! This document provides guidelines and information for contributors.

## How to Contribute

### Reporting Issues

1. **Check existing issues** first to avoid duplicates
2. **Use clear, descriptive titles** for new issues
3. **Provide detailed information** including:
   - Steps to reproduce the problem
   - Expected vs actual behavior
   - Your environment (OS, GitHub Actions runner, etc.)
   - Example workflow configuration
   - Any error messages or logs

### Suggesting Enhancements

1. **Check existing issues** and discussions first
2. **Clearly describe the enhancement** and its use case
3. **Explain why this enhancement would be useful** to other users
4. **Consider backward compatibility** implications

### Pull Requests

1. **Fork the repository** and create your branch from `main`
2. **Make your changes** following the coding standards below
3. **Test your changes** thoroughly
4. **Update documentation** if needed
5. **Write clear commit messages**
6. **Submit a pull request** with a clear description

## Development Setup

### Prerequisites

- Basic understanding of GitHub Actions
- Familiarity with shell scripting (bash)
- Git for version control

### Testing Your Changes

1. **Local Testing**: Test the shell script logic locally when possible
2. **GitHub Actions Testing**: Use the test workflow in `.github/workflows/test.yml`
3. **Manual Testing**: Create test repositories to validate functionality
4. **Edge Cases**: Test with various file patterns and configurations
5. **Directory Skipping**: Test that skip-directories properly excludes specified directories
   - Create test files in both skipped and non-skipped directories
   - Verify files in skipped directories are ignored
   - Verify files in allowed directories are still detected

### Testing Checklist

- [ ] Basic file detection works
- [ ] Glob patterns work correctly
- [ ] Case sensitivity options work
- [ ] Fail-fast behavior works as expected
- [ ] Working directory option works
- [ ] Skip directories feature works correctly
- [ ] Outputs are generated correctly
- [ ] Error messages are clear and helpful
- [ ] No false positives or negatives
- [ ] Directory exclusions don't affect files in allowed directories

## Coding Standards

### Shell Script Guidelines

- Use `bash` for shell scripts
- Use `set -e` for error handling
- Quote variables to prevent word splitting
- Use meaningful variable names
- Add comments for complex logic
- Follow consistent indentation (2 spaces)

### Action Definition

- Keep `action.yml` well-documented
- Use clear input/output descriptions
- Provide sensible defaults
- Maintain backward compatibility when possible

### Documentation

- Update README.md for new features
- Include usage examples
- Document breaking changes
- Keep examples working and up-to-date

## Commit Message Guidelines

Use clear, descriptive commit messages:

```
feat: add support for regex patterns
fix: handle empty file lists correctly  
docs: update README with new examples
test: add test case for case sensitivity
```

Prefixes:
- `feat:` - New features
- `fix:` - Bug fixes
- `docs:` - Documentation changes
- `test:` - Test-related changes
- `refactor:` - Code refactoring
- `style:` - Code style changes

## Release Process

### Versioning

We follow [Semantic Versioning](https://semver.org/):
- **MAJOR** version for incompatible API changes
- **MINOR** version for backward-compatible functionality additions
- **PATCH** version for backward-compatible bug fixes

### Creating a Release

1. Update version in documentation
2. Update CHANGELOG.md
3. Create a git tag
4. Create a GitHub release
5. Update major version tag (e.g., `v1` for `v1.2.3`)

## Project Structure

```
forbid-files/
├── action.yml              # Main action definition
├── README.md              # User documentation
├── CONTRIBUTING.md        # This file
├── LICENSE               # License file
├── .github/
│   └── workflows/
│       └── test.yml      # Test workflow
└── examples/             # Example workflows
    ├── check-env-files.yml
    ├── check-build-artifacts.yml
    ├── check-os-files.yml
    └── comprehensive-check.yml
```

## Getting Help

- **Issues**: Use GitHub issues for bugs and feature requests
- **Discussions**: Use GitHub discussions for questions and ideas
- **Documentation**: Check the README.md for usage information

## Code of Conduct

### Our Pledge

We are committed to making participation in this project a harassment-free experience for everyone, regardless of age, body size, disability, ethnicity, sex characteristics, gender identity and expression, level of experience, education, socio-economic status, nationality, personal appearance, race, religion, or sexual identity and orientation.

### Our Standards