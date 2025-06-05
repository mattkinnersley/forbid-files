# Forbid Files Action

A GitHub composite action that checks for forbidden files in your repository and fails the workflow if any are found. This is useful for preventing sensitive files, build artifacts, or other unwanted files from being committed to your repository.

## Features

- ✅ Check for multiple forbidden files at once
- ✅ Support for glob patterns and exact file names
- ✅ Case-sensitive or case-insensitive matching
- ✅ Fail-fast or collect all violations
- ✅ Custom working directory support
- ✅ Detailed output with file locations
- ✅ Zero dependencies - uses only shell commands

## Usage

### Basic Example

```yaml
name: Check for forbidden files
on: [push, pull_request]

jobs:
  check-files:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Check for forbidden files
        uses: mattkinnersley/forbid-files@1.0.0
        with:
          files: |
            .env
            *.log
            node_modules
            .DS_Store
```

### Advanced Example

```yaml
name: Comprehensive file checking
on: [push, pull_request]

jobs:
  check-files:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Check for forbidden files
        uses: mattkinnersley/forbid-files@1.0.0
        with:
          files: |
            .env
            .env.local
            *.log
            *.tmp
            .DS_Store
            Thumbs.db
            node_modules
            __pycache__
            *.pyc
            .vscode/settings.json
          fail-fast: false
          ignore-case: true
          working-directory: ./src
          skip-directories: |
            .git
            node_modules
            vendor
            .cache
```

### Multiple Files (Comma Separated)

```yaml
- name: Check for forbidden files
  uses: mattkinnersley/forbid-files@1.0.0
  with:
    files: ".env, *.log, .DS_Store, node_modules"
    fail-fast: true
    skip-directories: ".git, node_modules, vendor"
```

## Inputs

| Input               | Description                                              | Required | Default |
| ------------------- | -------------------------------------------------------- | -------- | ------- |
| `files`             | List of files to forbid (newline or comma-separated)     | ✅ Yes   | -       |
| `fail-fast`         | Stop on first forbidden file found                       | ❌ No    | `true`  |
| `ignore-case`       | Ignore case when matching file names                     | ❌ No    | `false` |
| `working-directory` | Directory to search in                                   | ❌ No    | `.`     |
| `skip-directories`  | List of directories to skip (newline or comma-separated) | ❌ No    | -       |

### File Patterns

The action supports various file patterns:

- **Exact names**: `filename.txt`, `.env`, `config.json`
- **Wildcards**: `*.log`, `temp*`, `*backup*`
- **Extensions**: `*.tmp`, `*.bak`, `*.old`

### Directory Skipping

You can skip entire directories during the search:

- **Common directories**: `.git`, `node_modules`, `vendor`, `.cache`
- **Build directories**: `dist`, `build`, `out`, `target`
- **IDE directories**: `.vscode`, `.idea`, `.vs`

## Outputs

| Output                  | Description                                               |
| ----------------------- | --------------------------------------------------------- |
| `forbidden-files-found` | Newline-separated list of forbidden files that were found |
| `files-count`           | Number of forbidden files found                           |

### Using Outputs

```yaml
- name: Check for forbidden files
  id: check-files
  uses: mattkinnersley/forbid-files@1.0.0
  with:
    files: ".env, *.log"
    fail-fast: false
    skip-directories: "node_modules, .git"

- name: Report findings
  if: steps.check-files.outputs.files-count > 0
  run: |
    echo "Found ${{ steps.check-files.outputs.files-count }} forbidden files:"
    echo "${{ steps.check-files.outputs.forbidden-files-found }}"
```

## Common Use Cases

### Prevent Environment Files

```yaml
- uses: mattkinnersley/forbid-files@1.0.0
  with:
    files: |
      .env
      .env.local
      .env.development
      .env.production
      config.json
      secrets.yaml
```

### Prevent Build Artifacts

```yaml
- uses: mattkinnersley/forbid-files@1.0.0
  with:
    files: |
      node_modules
      dist
      build
      *.log
      __pycache__
      *.pyc
      *.pyo
      .pytest_cache
    skip-directories: |
      .git
      vendor
      .cache
```

### Prevent OS-Specific Files

```yaml
- uses: mattkinnersley/forbid-files@1.0.0
  with:
    files: |
      .DS_Store
      Thumbs.db
      desktop.ini
      .Spotlight-V100
      .Trashes
    ignore-case: true
```

### Prevent IDE Configuration

```yaml
- uses: mattkinnersley/forbid-files@1.0.0
  with:
    files: |
      .vscode/settings.json
      .idea
      *.swp
      *.swo
      *~
    skip-directories: |
      node_modules
      .git
      dist
```

## Error Output Example

When forbidden files are found, the action will output detailed information:

```
🔍 Checking for forbidden files in: /github/workspace
📋 Files to check for:
  - .env
  - *.log
  - node_modules

⏭️  Directories to skip:
  - .git
  - node_modules
  - vendor

🔎 Checking for: .env
❌ Found forbidden file: .env
   📍 Location: ./.env

🔎 Checking for: *.log
❌ Found forbidden file: *.log
   📍 Location: ./debug.log
   📍 Location: ./error.log

❌ Found 3 forbidden file(s):
   ./.env
   ./debug.log
   ./error.log

💡 Please remove these files and try again.
```

## Best Practices

1. **Use in CI/CD Pipelines**: Combine with other linting actions for comprehensive code quality checks
2. **Fail Fast for Critical Files**: Use `fail-fast: true` for sensitive files like `.env`
3. **Collect All Violations**: Use `fail-fast: false` when you want to see all issues at once
4. **Case Sensitivity**: Use `ignore-case: true` for OS-specific files that might have different casing
5. **Specific Directories**: Use `working-directory` to limit scope when needed
6. **Skip Directories**: Use `skip-directories` to exclude large directories like `node_modules`, `.git`, or `vendor` for better performance

## Local Development and Pre-commit Hooks

**Important Note**: This GitHub Action runs on GitHub's servers and cannot be used directly in local pre-commit hooks. For local development, you have several options:

### Option 1: Shell Script for Pre-commit

Create a `.pre-commit-config.yaml` file:

```yaml
repos:
  - repo: local
    hooks:
      - id: forbid-files
        name: Check for forbidden files
        entry: bash
        language: system
        args:
          - -c
          - |
            forbidden_files=(.env *.log .DS_Store node_modules)
            for pattern in "${forbidden_files[@]}"; do
              if find . -name "$pattern" -not -path "./.git/*" | grep -q .; then
                echo "❌ Forbidden file found: $pattern"
                exit 1
              fi
            done
            echo "✅ No forbidden files found"
        pass_filenames: false
```

### Option 2: Custom Shell Script

Create `scripts/check-forbidden-files.sh`:

```bash
#!/bin/bash
set -e

# List of forbidden files/patterns
forbidden_files=(
  ".env"
  "*.log"
  ".DS_Store"
  "node_modules"
  "*.tmp"
)

# Directories to skip
skip_dirs=(
  ".git"
  ".github"
)

echo "🔍 Checking for forbidden files..."

for pattern in "${forbidden_files[@]}"; do
  # Build find command with exclusions
  find_cmd="find . -name '$pattern' -type f"
  for skip_dir in "${skip_dirs[@]}"; do
    find_cmd="$find_cmd -not -path './$skip_dir/*'"
  done
  
  if eval "$find_cmd" | grep -q .; then
    echo "❌ Found forbidden file pattern: $pattern"
    eval "$find_cmd"
    exit 1
  fi
done

echo "✅ No forbidden files found!"
```

Then add to `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: local
    hooks:
      - id: forbid-files
        name: Check for forbidden files
        entry: ./scripts/check-forbidden-files.sh
        language: system
        pass_filenames: false
```

### Option 3: Use Both Approaches

- **Local Development**: Use pre-commit hooks with shell scripts for fast feedback
- **CI/CD**: Use this GitHub Action for comprehensive server-side validation

This dual approach ensures developers catch issues early while maintaining robust server-side checks.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with different file patterns
5. Submit a pull request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Changelog

### v1.0.0

- Initial release
- Support for multiple file patterns
- Configurable fail-fast behavior
- Case-sensitive/insensitive matching
- Custom working directory support
