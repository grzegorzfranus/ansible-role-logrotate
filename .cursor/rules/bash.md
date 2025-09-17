# LLM Rule: Bash Development Rules

## 🐚 Core Guidelines
- **English only** - all code, comments, documentation
- **Descriptive names** - scripts and variables (e.g., `backup_files.sh`)
- **Modular design** - functions and reusable components
- **POSIX compliant** - or document non-standard dependencies
- **ShellCheck linting** - always validate scripts
- **Safe defaults** - use `set -euo pipefail`

## 📝 Script Structure
- **Shebang** - `#!/bin/bash` or `#!/usr/bin/env bash`
- **Error handling** - `set -euo pipefail` at script start
- **Functions first** - define functions before main logic
- **Input validation** - validate all parameters with `getopts`
- **Exit codes** - meaningful exit status (0=success, 1-255=error)

## 🔧 Best Practices
- **No hardcoding** - use environment variables or parameters
- **Least privilege** - minimum required permissions
- **Trap cleanup** - `trap 'cleanup' EXIT ERR`
- **Separate outputs** - redirect stdout/stderr appropriately
- **Quote variables** - always use `"$variable"`
- **Check commands** - test exit status of critical operations

## 📊 Status Emojis
- ✅ Success/Completed
- ❌ Script failures
- ⚠️ Warnings/Non-critical errors
- 🔄 Processing/In progress
- 🛠️ Maintenance/Setup
- 🧪 Testing scripts
- 🔍 Debugging

## 💡 Code Example
```bash
#!/bin/bash
set -euo pipefail

# Cleanup function
cleanup() {
    [[ -f "$temp_file" ]] && rm -f "$temp_file"
}
trap cleanup EXIT ERR

# Function with validation
process_file() {
    local file="$1"
    [[ -f "$file" ]] || { echo "❌ File not found: $file" >&2; return 1; }
    echo "✅ Processing: $file"
}

# Main logic
main() {
    local temp_file
    temp_file=$(mktemp)
    
    while getopts "f:h" opt; do
        case "$opt" in
            f) process_file "$OPTARG" ;;
            h) echo "Usage: $0 -f <file>"; exit 0 ;;
            *) echo "❌ Invalid option" >&2; exit 1 ;;
        esac
    done
}

main "$@"
```

## 🔒 Security & Performance
- **Input sanitization** - validate all external data
- **Avoid command injection** - quote variables, use arrays
- **Optimize loops** - minimize external command calls
- **Log appropriately** - separate info/error logs
- **Secure cron jobs** - proper permissions and paths