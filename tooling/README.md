# Tooling Folder

Local development and testing — **tests are not shipped to GitHub**.

## Structure

```
tooling/
├── tests/              # Unit and integration tests (local only, not on GitHub)
│   ├── test_pipeline.py
│   ├── test_golden.py
│   └── __init__.py
└── README.md           # This file
```

## Usage

**Run tests locally:**
```bash
python -m pytest tooling/tests/ -v
```

## Note

Feature files (ai_helpers, observability, visualization, sdk_integration, security, team_features, etc.) are kept on GitHub as part of the production codebase. Only test files are excluded from GitHub via `.gitignore`.
