# Project Summary: Augment Review PR Issue Implementation

## Overview

This project provides an AI-powered GitHub Action that validates whether pull requests fully implement the requirements from referenced GitHub issues. It uses the Augment Agent framework to analyze commit messages, fetch issue details, and compare code changes against issue requirements.

## Project Structure

```
issue-review-pr/
├── action.yml                          # GitHub Action composite workflow definition
├── package.json                        # Node.js package configuration
├── LICENSE                             # MIT License
├── README.md                           # Main documentation
├── RELEASE.md                          # Release notes and versioning
├── PROJECT_SUMMARY.md                  # This file
├── .gitignore                          # Git ignore patterns
├── .prettierrc.json                    # Prettier configuration
├── .prettierignore                     # Prettier ignore patterns
│
├── templates/                          # Nunjucks template framework
│   ├── prompt.njk                      # Main template orchestrator
│   ├── capabilities/
│   │   └── base.njk                    # AI capabilities description
│   ├── context/
│   │   └── pr_or_issue.njk             # PR context provider
│   ├── formatting/
│   │   └── base.njk                    # Output formatting rules
│   ├── guidelines/
│   │   └── base.njk                    # Issue validation guidelines
│   ├── instructions/
│   │   └── base.njk                    # AI workflow instructions
│   ├── limitations/
│   │   └── base.njk                    # AI limitations description
│   └── request/
│       └── base.njk                    # Issue validation task definition
│
└── example-workflows/                  # Example GitHub workflow files
    ├── README.md                       # Workflow documentation
    ├── basic-issue-validation.yml      # Simple validation workflow
    ├── custom-guidelines-validation.yml # Custom guidelines example
    └── on-demand-validation.yml        # Label-triggered validation
```

## Key Features

### 1. Issue Reference Extraction
- Automatically extracts GitHub issue numbers from commit messages
- Supports multiple reference formats: `#123`, `fixes #456`, `closes #789`, etc.
- Handles multiple issues per commit and across all commits in a PR

### 2. Issue Analysis
- Fetches issue details via GitHub API
- Extracts requirements, acceptance criteria, and expected behavior
- Analyzes issue descriptions, comments, and checklists
- Handles missing or inaccessible issues gracefully

### 3. Implementation Validation
- Compares PR code changes against issue requirements
- Identifies missing or incomplete implementations
- Maps code changes to issue requirements
- Focuses on high-confidence gaps

### 4. Intelligent Feedback
- Posts GitHub review comments with specific, actionable feedback
- Anchors comments to relevant code lines
- References both issue requirements and code changes
- Provides constructive, helpful tone

### 5. Customization
- Supports custom validation guidelines
- Configurable AI model selection
- Optional rules and MCP config files
- Flexible workflow triggers

## Template Framework

The project uses a modular Nunjucks template system following the established pattern from `review-pr` and `review-pr-test-coverage`:

### Template Components

1. **prompt.njk**: Main orchestrator that includes all other templates
2. **context/pr_or_issue.njk**: Provides PR metadata, changed files, and diff
3. **instructions/base.njk**: Defines the AI workflow steps
4. **request/base.njk**: Specifies the validation task and constraints
5. **guidelines/base.njk**: Detailed validation guidelines and focus areas
6. **formatting/base.njk**: Output format requirements and examples
7. **capabilities/base.njk**: Describes what the AI can do
8. **limitations/base.njk**: Describes what the AI cannot do

### Template Design Principles

- **Modular**: Each template has a specific purpose
- **Reusable**: Templates can be included and composed
- **Extensible**: Custom guidelines can be injected
- **Consistent**: Follows the same pattern as other Augment actions

## Workflow Process

1. **Trigger**: GitHub Action runs on PR events (opened, synchronize, reopened)
2. **Checkout**: Checks out the PR head to access the codebase
3. **Context Preparation**: Prepares custom guidelines if provided
4. **Agent Execution**: Runs Augment Agent with the template directory
5. **Issue Extraction**: AI extracts issue references from commit messages
6. **Issue Fetching**: AI fetches issue details via GitHub API
7. **Requirement Analysis**: AI analyzes issue requirements and acceptance criteria
8. **Code Analysis**: AI reviews PR code changes
9. **Validation**: AI compares requirements against implementation
10. **Feedback**: AI posts GitHub review with validation results

## Validation Focus Areas

The action validates across multiple dimensions:

- **Functional Completeness**: Core functionality requested in issues
- **Acceptance Criteria**: Explicit acceptance criteria from issues
- **Edge Cases**: Specific edge cases mentioned in issues
- **Technical Requirements**: APIs, data models, integrations specified
- **Non-Functional Requirements**: Performance, security, accessibility
- **Documentation**: Documentation updates requested in issues
- **Testing**: Testing requirements mentioned in issues

## Configuration

### Required Inputs
- `augment_session_auth`: Augment API credentials (stored as secret)
- `github_token`: GitHub token with repo and PR permissions
- `pull_number`: PR number being validated
- `repo_name`: Repository full name (owner/repo)

### Optional Inputs
- `custom_guidelines`: Project-specific validation guidelines
- `model`: AI model to use (e.g., sonnet4)
- `rules`: JSON array of rule file paths
- `mcp_configs`: JSON array of MCP config file paths

### Required Permissions
```yaml
permissions:
  contents: read        # Read repository files
  pull-requests: write  # Post review comments
  issues: read          # Fetch issue details
```

## Example Workflows

### Basic Validation
Runs on all PR events, validates issue implementations automatically.

### Custom Guidelines
Demonstrates adding project-specific validation requirements like:
- Acceptance criteria checkbox validation
- Security requirement verification
- Performance requirement checks
- Documentation requirement validation

### On-Demand Validation
Triggered by adding a `validate_issues` label, useful for:
- Selective validation
- Re-validation after addressing feedback
- Manual control over when validation runs

## Comparison to Related Projects

### vs. review-pr
- **review-pr**: General code review (quality, bugs, best practices)
- **issue-review-pr**: Validates implementation against issue requirements

### vs. review-pr-test-coverage
- **review-pr-test-coverage**: Suggests test cases for code changes
- **issue-review-pr**: Validates code implements issue requirements

### vs. describe-pr
- **describe-pr**: Generates PR descriptions from code changes
- **issue-review-pr**: Validates code changes against issue requirements

All four projects share the same template framework architecture.

## Best Practices

1. **Write Clear Issues**: Include explicit requirements and acceptance criteria
2. **Reference Issues in Commits**: Use standard patterns like `fixes #123`
3. **Use Custom Guidelines**: Add project-specific validation requirements
4. **Review AI Feedback**: Treat suggestions as recommendations, not requirements
5. **Iterate**: Use feedback reactions to improve future validations
6. **Combine with Other Actions**: Use alongside code review and test coverage actions

## Limitations

- Cannot execute code or verify functional correctness
- Analysis quality depends on issue description clarity
- Subject to GitHub API rate limits
- Cannot access external systems or databases
- Validates requirement coverage, not code quality
- May miss implicit or unstated requirements

## Future Enhancements

Potential improvements for future versions:

1. **Issue Template Recognition**: Better parsing of structured issue templates
2. **Multi-Repository Support**: Validate against issues in other repositories
3. **Historical Analysis**: Learn from past PRs and issues
4. **Integration with Project Boards**: Validate against project board requirements
5. **Custom Issue Parsers**: Support for different issue formats and tools
6. **Metrics and Reporting**: Track validation coverage and gap trends

## Development

### Prerequisites
- Node.js for package management
- Prettier for code formatting
- Access to Augment Agent framework

### Local Testing
1. Set up Augment session authentication
2. Configure GitHub token with required permissions
3. Test templates with sample PR data
4. Validate workflow files with `actionlint`

### Contributing
Contributions welcome! Please:
- Follow existing template patterns
- Add tests for new features
- Update documentation
- Follow commit message conventions

## License

MIT License - see LICENSE file for details.

## Support

- GitHub Issues: https://github.com/augmentcode/issue-review-pr/issues
- Documentation: https://docs.augmentcode.com
- Related Projects:
  - review-pr: https://github.com/augmentcode/review-pr
  - describe-pr: https://github.com/augmentcode/describe-pr
  - review-pr-test-coverage: https://github.com/augmentcode/review-pr-test-coverage

