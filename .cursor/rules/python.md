# LLM Rule: Python Development Rules

## 🐍 Core Guidelines
- **English only** - all code, comments, documentation
- **PEP 8 compliant** - use Black formatter (88 chars max)
- **Type hints** - for all functions and classes
- **DRY & KISS** - modular, reusable, scalable code
- **No hardcoded values** - use variables/config
- **Virtual environments** - venv or Docker for dependencies

## 📝 Naming Conventions
- `UPPER_CASE` - constants, environment variables
- `snake_case` - variables, functions, files, directories
- `PascalCase` - classes
- `_private` - private methods/attributes

## 🔧 Project Essentials
- `requirements.txt` or `pyproject.toml`
- Tests in `tests/` directory with `pytest`
- Docstrings for public functions/classes
- Mock external dependencies in tests
- Pin dependency versions
- >80% test coverage

## 📊 Status Emojis
- ✅ Success/Tests passing
- ❌ Failures/Errors
- ⚠️ Warnings
- 🔄 Refactoring
- 🧪 Testing
- 🔍 Debugging

## 💡 Code Example
```python
def calculate_price(items: list[dict], tax: float = 0.08) -> float:
    """Calculate total price with tax."""
    if tax < 0:
        raise ValueError("Tax cannot be negative")
    return sum(item["price"] for item in items) * (1 + tax)
```

## 🔒 Security
- Environment variables for secrets
- Validate all inputs
- Parameterized database queries