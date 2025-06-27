# Ansible RStudio Server Setup

Automated installation and configuration of R and RStudio Server on localhost using Ansible.

## Features

- ✅ Installs R with development dependencies
- ✅ Deploys RStudio Server with optimized configuration
- ✅ Configures custom IDE preferences and keybindings
- ✅ Automatic backup of existing configurations
- ✅ Error handling and service verification
- ✅ Supports user customization via variables

## Quick Start

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd ansible-rstudio
   ```

2. **Install required Ansible roles**
   ```bash
   ansible-galaxy install -r requirements.yml
   ```

3. **Run the playbook**
   ```bash
   # Use default user (current user)
   ansible-playbook -i inventory.ini playbook.yml
   
   # Or specify target user
   ansible-playbook -i inventory.ini playbook.yml -e rstudio_user=username
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

### R Packages
R package installation is disabled by default due to potential freezing issues. To enable:
```yaml
r_packages:
  - name: devtools
  - name: tidyverse
  # Add more packages as needed
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

## File Structure

```
├── playbook.yml          # Main Ansible playbook
├── requirements.yml      # External role dependencies
├── inventory.ini         # Localhost inventory
├── .ansible-lint        # Linting configuration
├── README.md            # This file
└── CLAUDE.md            # AI assistant instructions
```

## Troubleshooting

### Permission Issues
Ensure the target user exists and you have sudo privileges:
```bash
# Check user exists
id username

# Run with explicit sudo
ansible-playbook -i inventory.ini playbook.yml --ask-become-pass
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
Existing configurations are automatically backed up to:
- `~/.config/rstudio/rstudio-prefs.json.backup.<timestamp>`

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

## External Dependencies

This playbook uses community roles:
- [Oefenweb.ansible-r](https://github.com/Oefenweb/ansible-r) - R installation
- [Oefenweb.ansible-rstudio-server](https://github.com/Oefenweb/ansible-rstudio-server) - RStudio Server

## License

MIT License - see LICENSE file for details.
