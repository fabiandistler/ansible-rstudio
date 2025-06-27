# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an Ansible playbook for installing and configuring R and RStudio Server on localhost. The playbook uses external roles from GitHub to handle the core installation, then applies custom configuration for RStudio preferences and keybindings.

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
```

### Access
- RStudio Server runs on <http://localhost:8787/>
- Login with existing Linux user credentials

## Architecture

The playbook consists of two main components:

1. **External Roles** (defined in `requirements.yml`):
   - `r`: Installs R and development dependencies from Oefenweb/ansible-r
   - `rstudio-server`: Installs RStudio Server from Oefenweb/ansible-rstudio-server

2. **Custom Configuration Tasks** (in `playbook.yml`):
   - Creates RStudio user preferences (`rstudio-prefs.json`)
   - Sets up custom keybindings (`addins.json`)
   - Manages RStudio Server service

## Configuration

### Key Variables
- `rstudio_user`: Target user for RStudio (default: `ansible_user` or fallback to "fd")
- `r_install_dev`: Enables building R from source (default: true)
- `r_install`: List of R development packages and dependencies
- `preferences`: RStudio IDE preferences (theme, editor settings, panes)
- `keybindings`: Custom keyboard shortcuts for RStudio addins

### New Features
- **Automatic user detection**: Uses current user by default
- **Configuration backup**: Existing configs are backed up with timestamps
- **Error handling**: Service verification with retries and proper error reporting
- **Idempotency**: Only restarts services when configurations change
- **Linting support**: Includes `.ansible-lint` configuration

### File Structure
- `playbook.yml`: Main playbook with roles and custom tasks
- `requirements.yml`: External role dependencies
- `inventory.ini`: Localhost inventory configuration
- User config files created at: `/home/{user}/.config/rstudio/`

## Common Issues

If you encounter "role not found" errors, ensure roles are installed first with `ansible-galaxy install -r requirements.yml` before running the playbook.