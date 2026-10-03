# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an Ansible playbook for installing and configuring R and RStudio Server on localhost. The playbook uses external roles from GitHub (with pinned versions for reproducibility) to handle the core installation, then applies custom configuration for RStudio preferences and keybindings. Includes pre-flight validation, comprehensive error handling, and task tagging for selective execution.

## Key Commands

### Setup and Deployment
```bash
# Install required Ansible roles
ansible-galaxy install -r requirements.yml

# Run the complete playbook (uses dynamic user detection)
ansible-playbook -i inventory.ini playbook.yml

# Run with specific user override
ansible-playbook -i inventory.ini playbook.yml -e rstudio_user=username

# Test without making changes
ansible-playbook -i inventory.ini playbook.yml --check

# Lint the playbook
ansible-lint playbook.yml

# Run with tags (selective execution)
ansible-playbook -i inventory.ini playbook.yml --tags preflight
ansible-playbook -i inventory.ini playbook.yml --tags config
ansible-playbook -i inventory.ini playbook.yml --tags preferences
ansible-playbook -i inventory.ini playbook.yml --tags keybindings
```

### Access
- RStudio Server runs on <http://localhost:8787/>
- Login with existing Linux user credentials

## Architecture

The playbook consists of two main components:

1. **External Roles** (defined in `requirements.yml` with version pinning):
   - `r` (v3.1.11): Installs R and development dependencies from Oefenweb/ansible-r
   - `rstudio-server` (v5.2.0): Installs RStudio Server from Oefenweb/ansible-rstudio-server

2. **Pre-flight Validation** (in `pre_tasks` section):
   - Verifies target user exists using getent (runs BEFORE roles)
   - Checks that required Ansible roles are installed
   - Provides helpful error messages with actionable guidance
   - Prevents cryptic role loading errors

3. **Custom Configuration Tasks** (in `tasks` section):
   - Creates RStudio user preferences (`rstudio-prefs.json`) with backup
   - Sets up custom keybindings (`addins.json`) with backup
   - Manages RStudio Server service with retries

## Configuration

### Key Variables
- `rstudio_user`: Target user for RStudio (default: `ansible_user` or fallback to "fd")
- `r_install_dev`: Enables building R from source (default: true)
- `r_install`: List of 40+ R development packages (documented with comments)
- `preferences`: RStudio IDE preferences (theme, editor settings, panes)
- `keybindings`: Custom keyboard shortcuts for RStudio addins

### Features
- **Version pinning**: Role versions locked for reproducible deployments
- **Pre-flight validation**: Verifies user exists and roles are installed
- **Automatic user detection**: Uses current user by default
- **Configuration backup**: Existing configs backed up with timestamps
- **Error handling**: Service verification with retries and proper error reporting
- **Idempotency**: Only restarts services when configurations change
- **Task tagging**: Selective execution with `--tags` flag
- **Comprehensive documentation**: Package list with explanatory comments
- **Linting support**: Includes `.ansible-lint` configuration

### File Structure
- `playbook.yml`: Main playbook with roles, validation, and configuration tasks
- `requirements.yml`: External role dependencies (version pinned)
- `inventory.ini`: Localhost inventory configuration
- `.ansible-lint`: Linting configuration
- `.gitignore`: Git exclusions for cache and temporary files
- `LICENSE`: MIT License
- User config files created at: `/home/{user}/.config/rstudio/`

### Task Tags
Use tags for selective execution:
- `preflight`: Pre-flight validation checks
- `config`: All configuration tasks
- `preferences`: RStudio IDE preferences only
- `keybindings`: Custom keyboard shortcuts only
- `service`: Service management
- `verify`: Service verification

## Common Issues

If you encounter "role not found" errors, ensure roles are installed first with `ansible-galaxy install -r requirements.yml` before running the playbook.