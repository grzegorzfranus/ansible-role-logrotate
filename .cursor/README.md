# Technology-Specific Cursor Rules

This directory contains organized development rules and examples for different technologies and frameworks.

## 📁 Directory Structure

```
.cursor/
├── README.md               # This documentation
├── rules/                 # Technology-specific rule sets
│   ├── ansible.md         # Ansible playbooks, roles, and automation
│   ├── ansible-inventory.md # Ansible inventory management rules
│   ├── bash.md            # Bash scripting and best practices
│   ├── docker.md          # Docker containers and orchestration
│   └── python.md          # Python development guidelines
└── examples/              # Code examples and templates
    └── ansible-role/      # Example Ansible role structure
```

## 📋 Available Rule Sets

- **`rules/ansible.md`** - Ansible playbooks, roles, and automation
- **`rules/ansible-inventory.md`** - Ansible inventory management and organization rules
- **`rules/bash.md`** - Bash scripting, automation, and best practices
- **`rules/docker.md`** - Docker containers, images, and orchestration  
- **`rules/python.md`** - Python development, testing, and best practices

## 🔄 How to Use These Rules

### Method 1: Reference in Chat/Composer
```
@.cursor/rules/bash.md Help me create a backup script with error handling
@.cursor/rules/ansible.md Please help me create an Ansible playbook for nginx installation
```

### Method 2: Include in Prompts
```
Using the guidelines from .cursor/rules/bash.md, create a script that:
- Validates input parameters
- Includes proper error handling
- Uses status emojis for output
```

### Method 3: Copy to .cursorrules (Temporary)
When working extensively on a specific technology:
1. Copy content from specific rule file in `rules/` directory
2. Temporarily add to `.cursorrules`
3. Remove when switching to different technology

## 📚 Examples Directory

The `examples/` directory contains:
- **Code templates** - Ready-to-use code structures
- **Best practice implementations** - Real-world examples following the rules
- **Project scaffolds** - Complete project structures for different technologies

### Available Examples
- `examples/ansible-role/` - Complete Ansible role structure with best practices

## 🎯 Benefits

✅ **Organized** - Rules and examples separated into logical directories
✅ **Focused** - Context-specific guidelines per technology
✅ **Reusable** - Easy to reference rules and copy examples
✅ **Maintainable** - Update rules and examples independently
✅ **Shareable** - Team members get consistent guidelines and templates

## 📝 Adding New Content

### Adding New Rule Sets
To add rules for a new technology:
1. Create `rules/technology-name.md`
2. Follow the same structure as existing rule files
3. Include status emojis and code examples
4. Update this README with the new rule set

### Adding New Examples
To add examples for a technology:
1. Create `examples/technology-name/` directory
2. Add well-documented code examples
3. Follow the rules from corresponding `rules/` file
4. Update this README with the new examples

## 🔧 Integration with Main Rules

These specific rules work alongside the main `.cursorrules` file:
- **`.cursorrules`** - Core development rules (always active)
- **`.cursor/rules/*.md`** - Technology-specific rules (use when needed)
- **`.cursor/examples/`** - Practical implementations and templates 