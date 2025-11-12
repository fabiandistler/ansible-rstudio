# Ansible RStudio Server Setup

Automated installation and configuration of R and RStudio Server on localhost using Ansible.

## Features

- ✅ Installs R with comprehensive development dependencies (40+ packages)
- ✅ Deploys RStudio Server with optimized configuration
- ✅ Configures custom IDE preferences and keybindings
- ✅ Automatic backup of existing configurations (timestamped)
- ✅ Pre-flight validation (user exists, roles installed)
- ✅ Error handling and service verification with retries
- ✅ Task tagging for selective execution
- ✅ Pinned role versions for reproducibility
- ✅ Comprehensive documentation with package explanations

## Quick Start

1. **Clone the repository**

   ```bash
   git clone <repository-url>
   cd ansible-rstudio
   ```


2. **Install required Ansible roles (as user, not sudo)**

   ```bash
   # Install roles as your user (NOT with sudo)
   ansible-galaxy install -r requirements.yml
   ```

3. **Run the playbook with sudo privileges**

   ```bash
   # Use default user (current user) with sudo prompt
   ansible-playbook -i inventory.ini playbook.yml --ask-become-pass
   
   # Or specify target user with sudo prompt
   ansible-playbook -i inventory.ini playbook.yml -e rstudio_user=username --ask-become-pass
   ```

4. **Access RStudio Server**
   - Open <http://localhost:8787/> in your browser
   - Login with your Linux user credentials

## Configuration

### User Settings

The playbook uses `ansible_user` by default, with fallback to `fd`. Override with:


```bash
ansible-playbook -i inventory.ini playbook.yml -e rstudio_user=your_username
```

### Custom Preferences

Modify the `preferences` variable in `playbook.yml` to customize:

- Editor theme and settings
- Pane layout
- Code completion options
- Display preferences

### Keybindings

Custom keyboard shortcuts are configured in the `keybindings` variable:

- `Ctrl+-`: Format code with styler
- `Ctrl++`: Run Golem dev mode
- `Ctrl+T`: Test active file

### Selective Execution with Tags

Run specific parts of the playbook using tags:

```bash
# Run only pre-flight checks
ansible-playbook -i inventory.ini playbook.yml --tags preflight

# Configure only preferences (skip keybindings)
ansible-playbook -i inventory.ini playbook.yml --tags preferences

# Configure only keybindings (skip preferences)
ansible-playbook -i inventory.ini playbook.yml --tags keybindings

# Skip service verification
ansible-playbook -i inventory.ini playbook.yml --skip-tags verify
```

Available tags:
- `preflight` - Pre-flight validation checks
- `config` - All configuration tasks
- `preferences` - RStudio IDE preferences
- `keybindings` - Custom keyboard shortcuts
- `service` - Service management tasks
- `verify` - Service verification

## File Structure

```
├── playbook.yml          # Main Ansible playbook with enhanced validation
├── requirements.yml      # External role dependencies (version pinned)
├── inventory.ini         # Localhost inventory
├── .ansible-lint         # Linting configuration
├── .gitignore            # Git ignore patterns
├── LICENSE               # MIT License
├── README.md             # This file
└── CLAUDE.md             # AI assistant instructions
```

## Troubleshooting

### Permission Issues

**Common Issue: Roles installed with sudo**

If you installed roles with `sudo ansible-galaxy install`, they are installed for root only. Fix this:

```bash
# Remove roles installed as root
sudo rm -rf /root/.ansible/roles/r /root/.ansible/roles/rstudio-server

# Install roles as your user (without sudo)
ansible-galaxy install -r requirements.yml

# Run playbook with sudo prompt
ansible-playbook -i inventory.ini playbook.yml --ask-become-pass
```

**User verification:**

```bash
# Check user exists
id username


# Check roles are installed for your user
ls ~/.ansible/roles/
```

### Service Issues

If RStudio Server fails to start:

```bash
# Check service status
sudo systemctl status rstudio-server

# View logs
sudo journalctl -u rstudio-server -f
```

### Configuration Backups

Existing configurations are automatically backed up with timestamps:

- `~/.config/rstudio/rstudio-prefs.json.backup.<timestamp>`
- `~/.config/rstudio/keybindings/addins.json.backup.<timestamp>`

Backups are only created if the files already exist.

## Development

### Linting

Use ansible-lint to check playbook quality:

```bash
ansible-lint playbook.yml
```

### Testing

Test the playbook in check mode:

```bash
ansible-playbook -i inventory.ini playbook.yml --check
```

## Requirements

- Ansible 2.9+
- Ubuntu/Debian Linux (tested)
- Sudo privileges
- Internet connection for downloading roles and packages


## Important Notes

**Role Installation:** Always install Ansible roles as your user (without sudo):

```bash
ansible-galaxy install -r requirements.yml  # ✅ Correct
sudo ansible-galaxy install -r requirements.yml  # ❌ Wrong - installs for root only
```

**Playbook Execution:** Use `--ask-become-pass` to provide sudo when needed:

```bash
ansible-playbook -i inventory.ini playbook.yml --ask-become-pass
```

## External Dependencies

This playbook uses community roles with pinned versions:

- [Oefenweb.ansible-r](https://github.com/Oefenweb/ansible-r) v3.1.11 - R installation
- [Oefenweb.ansible-rstudio-server](https://github.com/Oefenweb/ansible-rstudio-server) v5.2.0 - RStudio Server

Version pinning ensures reproducible deployments. To update to newer versions, modify `requirements.yml`.

## License

MIT License - see LICENSE file for details.
