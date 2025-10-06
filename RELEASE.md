# Release Notes

## Version 0.1.0 (Initial Release)

### Features
- AI-powered GitHub issue implementation validation for pull requests
- Automatic extraction of issue references from commit messages
- Comparison of issue requirements against PR code changes
- Inline PR comments identifying implementation gaps
- Support for custom validation guidelines
- Integration with Augment Agent framework

### Supported Issue Reference Formats
- `#123` - Simple issue reference
- `fixes #123` - Closing keyword with issue number
- `closes #456` - Alternative closing keyword
- `resolves #789` - Another closing keyword
- Multiple issues per commit message

### Requirements
- GitHub token with `repo` scope
- Augment session authentication
- Node.js environment for template rendering

### Known Limitations
- Requires issues to have structured requirements or acceptance criteria
- Analysis quality depends on issue description clarity
- Subject to GitHub API rate limits
- Cannot validate implementation correctness, only completeness

## Versioning

This project follows semantic versioning (SemVer):
- **Major version**: Breaking changes to action inputs or behavior
- **Minor version**: New features, backward compatible
- **Patch version**: Bug fixes, backward compatible

## Upgrade Guide

### From v0.x to v1.x
(To be documented when v1.0 is released)

## Changelog

### v0.1.0 - 2025-10-06
- Initial release
- Core issue validation functionality
- Template framework implementation
- GitHub Action integration
- Documentation and examples

