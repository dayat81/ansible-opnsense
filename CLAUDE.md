# CLAUDE.md
sudo password is admin
opnsense on https://192.168.18.47/ root:opnsense
key=axyedgWJiYaUQ19AOusP4DUBA83rB08WixZpkLnKdkWyK+s9HUl0wW27EascUw0SmRkfoDNZW2uRU64N
secret=mnStlMA6UTZ8GVxnTgZafCefLTLI8THhmP23U0/eQ2eCy0LbOeBMJRpNl7cSBq2qSSK3HER3T8mq+kM2
This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Build
```bash
./scripts/build.sh
```
Builds the Ansible collection tarball using `ansible-galaxy collection build`.

### Linting
```bash
./scripts/lint.sh
```
Runs comprehensive linting including:
- Python linting with pylint
- YAML linting with yamllint  
- Ansible linting with ansible-lint

### Testing
```bash
./scripts/test.sh <firewall> <api_key_file> <collection_path> [venv_path]
```
Runs functional tests against a live OPNSense firewall. Tests all modules in check mode or actual mode as appropriate.

For single module testing:
```bash
./scripts/test_single.sh <module_name> <firewall> <api_key_file> <collection_path> [venv_path]
```

Unit tests can be run with pytest (configured in pytest.ini).

## Architecture Overview

This is an Ansible collection for managing OPNSense firewalls via their REST API.

### Core Structure

- **plugins/modules/**: Ansible modules (the main entry points users interact with)
- **plugins/module_utils/**: Shared utilities and implementation logic
  - **base/**: Core API handling, base classes, and common functionality
  - **main/**: Module-specific implementation classes 
  - **helper/**: Utility functions for API calls, validation, data manipulation
  - **defaults/**: Default parameter definitions for modules
  - **inline_docs/**: Module documentation

### Key Architecture Patterns

1. **Separation of Concerns**: Modules in `plugins/modules/` are thin wrappers that delegate to implementation classes in `plugins/module_utils/main/`

2. **Base Class Pattern**: All module implementations inherit from `Base` class in `plugins/module_utils/base/base.py` which provides:
   - Common CRUD operations
   - API communication patterns
   - Field mapping and translation
   - Diff generation and change detection

3. **API Session Management**: `plugins/module_utils/base/api.py` provides the `Session` class for HTTP API communication using httpx

4. **Module Wrapper Pattern**: All modules use `module_wrapper` from `helper/wrapper.py` for consistent error handling and execution flow

### API Integration

- Uses httpx library for HTTP client functionality
- Session-based API authentication with API key/secret
- Configurable timeouts, retries, and SSL verification
- Comprehensive error handling for API failures

### Module Development

When creating new modules:
1. Add module file to `plugins/modules/`
2. Add implementation class to `plugins/module_utils/main/`
3. Add default parameters to `plugins/module_utils/defaults/`
4. Add inline documentation to `plugins/module_utils/inline_docs/`
5. Add Ansible test playbook to `tests/`

The base class handles most common patterns - focus on defining the API endpoints, field mappings, and module-specific logic.

## Requirements

- Python 3.x with httpx module
- Ansible
- Access to OPNSense firewall with API enabled

## Testing Requirements

Functional tests require:
- Live OPNSense firewall
- API credentials file
- Test environment setup via test preparation scripts