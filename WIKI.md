# Byobu Wiki

Welcome to the comprehensive wiki for **Byobu** - a GPLv3 open source text-based window manager and terminal multiplexer that provides elegant enhancements to GNU Screen and Tmux.

## Table of Contents

1. [Overview](#overview)
2. [Installation](#installation)
3. [Quick Start](#quick-start)
4. [Configuration](#configuration)
5. [Status Modules](#status-modules)
6. [Profiles and Customization](#profiles-and-customization)
7. [Commands and Usage](#commands-and-usage)
8. [Development](#development)
9. [Troubleshooting](#troubleshooting)
10. [Additional Resources](#additional-resources)

## Overview

Byobu is a text-based window manager and terminal multiplexer originally designed to provide elegant enhancements to GNU Screen for Ubuntu server distribution. It now includes enhanced profiles, convenient keybindings, configuration utilities, and toggle-able system status notifications for both GNU Screen and the more modern Tmux terminal multiplexer.

### Key Features

- **Dual Backend Support**: Works with both GNU Screen and Tmux
- **Rich Status Bar**: 40+ status modules for system monitoring
- **Customizable Profiles**: Multiple configuration profiles and themes
- **Cross-Platform**: Works on Linux, BSD, and Mac distributions
- **Easy Configuration**: Interactive configuration utility
- **Session Management**: Advanced session and window management
- **Keybinding Support**: Convenient and customizable keybindings

### Architecture

Byobu acts as a wrapper around either GNU Screen or Tmux, providing:
- Enhanced status bar with system information
- Improved configuration management
- Profile-based customization
- Session management utilities
- Cross-platform compatibility layer

## Installation

### Prerequisites

Before installing Byobu, ensure you have the following dependencies:

- **tmux >= 1.5** and **screen**
- **python-newt** (optional, for configuration utility)
- **gsed** (if your sed implementation doesn't support -i)

### Building from Source

1. **Clone the repository:**
   ```bash
   git clone https://github.com/RyoNakagami/byobu.git
   cd byobu
   ```

2. **Configure the build:**
   ```bash
   ./configure --prefix="$HOME/byobu"
   ```

   **Optional**: Use python from your environment:
   ```bash
   echo "export BYOBU_PYTHON='/usr/bin/env python'" >> $HOME/.bashrc
   ```

3. **Build:**
   ```bash
   make
   ```

4. **Install:**
   ```bash
   make install
   ```

5. **Update your environment:**
   ```bash
   echo "export PATH=$HOME/byobu/bin:$PATH" >> $HOME/.bashrc
   . $HOME/.bashrc
   ```

6. **Run Byobu:**
   ```bash
   byobu
   ```

### Alternative Installation from Git

If you want to pull from upstream git:
```bash
git clone git://github.com/dustinkirkland/byobu.git byobu-src
cd byobu-src
./debian/rules autoconf
```

## Quick Start

### First Launch

Simply run:
```bash
byobu
```

This will:
- Start a new session using the default backend (tmux or screen)
- Display the status bar with system information
- Create a shell window ready for use

### Basic Navigation

**Tmux Backend (default):**
- `Ctrl-a` + `c` - Create new window
- `Ctrl-a` + `n` - Next window
- `Ctrl-a` + `p` - Previous window
- `Ctrl-a` + `d` - Detach session

**Screen Backend:**
- Same keybindings as tmux
- Use `byobu-select-backend` to switch between backends

### Configuration

Run the interactive configuration utility:
```bash
byobu-config
```

This opens a menu-driven interface to:
- Toggle status notifications
- Change color schemes
- Modify keybindings
- Select backend (tmux/screen)

## Configuration

### Configuration Files

Byobu stores configuration in `~/.byobu/`:

- `~/.byoburc` - Main configuration file
- `~/.byobu/backend` - Backend selection (tmux/screen)
- `~/.byobu/color.tmux` - Color configuration for tmux
- `~/.byobu/profile.tmux` - Profile settings for tmux
- `~/.byobu/keybindings.tmux` - Custom keybindings for tmux
- `~/.byobu/.tmux.conf` - User tmux configuration
- `~/.byobu/statusrc` - Status bar configuration

### Backend Selection

Choose between tmux and screen:
```bash
byobu-select-backend
```

Or set directly:
```bash
echo "BYOBU_BACKEND=tmux" >> ~/.byoburc
# or
echo "BYOBU_BACKEND=screen" >> ~/.byoburc
```

### Environment Variables

Key environment variables:

- `BYOBU_PREFIX` - Installation prefix
- `BYOBU_BACKEND` - Backend selection (tmux/screen)
- `BYOBU_TERM` - Terminal type
- `BYOBU_PYTHON` - Python interpreter path
- `BYOBU_CHARMAP` - Character map encoding

## Status Modules

Byobu includes 40+ status modules that display system information in the status bar.

### Available Status Modules

**System Information:**
- `arch` - System architecture
- `distro` - Linux distribution
- `hostname` - System hostname
- `release` - OS release information
- `whoami` - Current username

**Hardware Monitoring:**
- `battery` - Battery status and charge level
- `cpu_count` - Number of CPU cores
- `cpu_freq` - CPU frequency
- `cpu_temp` - CPU temperature
- `fan_speed` - Fan speed
- `memory` - Memory usage
- `swap` - Swap usage

**Network:**
- `ip_address` - IP address information
- `network` - Network interface status
- `wifi_quality` - WiFi signal quality

**Storage:**
- `disk` - Disk usage
- `disk_io` - Disk I/O statistics
- `raid` - RAID status

**System Status:**
- `load_average` - System load average
- `processes` - Running processes count
- `uptime` - System uptime
- `users` - Logged in users
- `entropy` - Available entropy
- `updates_available` - Available package updates
- `reboot_required` - Reboot required status

**Applications:**
- `mail` - Mail status
- `services` - Service status
- `trash` - Trash status

**Time and Date:**
- `date` - Current date
- `time` - Current time
- `time_binary` - Binary clock
- `time_utc` - UTC time

### Enabling/Disabling Status Modules

Use the interactive configuration:
```bash
byobu-config
```

Or manually edit status configuration files in `~/.byobu/`.

### Custom Status Modules

Create custom status modules by:

1. Creating a script in `~/.byobu/bin/`
2. Making it executable
3. Adding it to your status configuration

Example custom module:
```bash
#!/bin/bash
# ~/.byobu/bin/custom_status
echo "Custom: $(date +%s)"
```

## Profiles and Customization

### Available Profiles

Byobu includes several built-in profiles in `/usr/share/byobu/profiles/`:

- `tmuxrc` - Tmux configuration profile
- `screenrc` - Screen configuration profile  
- `bashrc` - Bash shell customizations
- `byoburc` - Byobu-specific settings
- `common` - Common settings shared between backends

### Color Schemes

Byobu supports multiple color schemes and themes. The `bashrc` profile includes special color configurations:

**Wolfi Colors:**
- Pink (170), Purple (98), Blue (63)
- Customizable prompt with git integration
- Runtime and status information

### Profile Selection

Select a profile:
```bash
byobu-select-profile
```

### Custom Profiles

Create custom profiles by:

1. Copying an existing profile
2. Modifying settings
3. Selecting the custom profile

## Commands and Usage

### Core Commands

**Main Commands:**
- `byobu` - Start byobu session
- `byobu-config` - Interactive configuration
- `byobu-screen` - Force screen backend
- `byobu-tmux` - Force tmux backend

**Session Management:**
- `byobu-select-session` - Select/attach to session
- `byobu-layout` - Manage window layouts
- `byobu-export` - Export session configuration

**Configuration Commands:**
- `byobu-enable` - Enable byobu
- `byobu-disable` - Disable byobu
- `byobu-select-backend` - Choose backend
- `byobu-select-profile` - Choose profile

**Status Commands:**
- `byobu-status` - Display status information
- `byobu-status-detail` - Detailed status information
- `byobu-ugraph` - Unicode graph utility
- `byobu-ulevel` - Unicode level utility

**Utility Commands:**
- `byobu-janitor` - Clean up byobu processes
- `byobu-launcher` - Desktop launcher
- `byobu-quiet` - Quiet mode
- `byobu-reconnect-sockets` - Reconnect sockets

### Advanced Usage

**Custom Window Sets:**
Create custom window configurations in `~/.byobu/windows.tmux`:
```
new-window -n "editor" vim
new-window -n "server" 
new-window -n "logs" tail -f /var/log/syslog
```

**Session Scripting:**
Automate session creation with custom scripts and layouts.

**Multi-Backend Usage:**
Run both tmux and screen sessions simultaneously with different configurations.

## Development

### Project Structure

```
byobu/
├── usr/
│   ├── bin/           # Executable scripts
│   ├── lib/byobu/     # Status modules and libraries
│   └── share/byobu/   # Profiles, keybindings, resources
├── etc/byobu/         # System configuration
├── debian/            # Debian packaging
└── po/                # Internationalization
```

### Build System

Byobu uses GNU Autotools for building:

- `configure.ac` - Autoconf configuration
- `Makefile.am` - Automake configuration
- `autogen.sh` - Generate build scripts

### Key Components

**Core Libraries:**
- `usr/lib/byobu/include/common` - Common functions
- `usr/lib/byobu/include/colors` - Color definitions
- `usr/lib/byobu/include/constants` - Constants
- `usr/lib/byobu/include/shutil` - Shell utilities

**Status Module System:**
Each status module in `usr/lib/byobu/` is a standalone script that:
- Implements `__module_name()` function
- Implements `__module_name_detail()` function
- Uses common color and formatting functions
- Supports caching for performance

**Configuration System:**
- Python-based configuration utility
- Shell-based profile system
- Backend-specific configurations

### Contributing

1. **Fork the Repository:**
   ```bash
   git clone https://github.com/RyoNakagami/byobu.git
   ```

2. **Development Setup:**
   ```bash
   ./configure --prefix="$HOME/byobu-dev"
   make
   make install
   ```

3. **Coding Standards:**
   - Use tabs, not spaces
   - Follow existing shell script patterns
   - Test on multiple platforms
   - Check syntax with `sh -n`

4. **Testing:**
   ```bash
   # Check shell syntax
   sh -n `find . -type f -exec grep -l "^#!/bin/sh" '{}' \;`
   
   # Test status modules
   ./usr/lib/byobu/battery
   ./usr/lib/byobu/cpu_count
   ```

5. **Submit Changes:**
   - Commit changes locally
   - Create pull request on GitHub
   - Include tests and documentation

### Architecture Details

**Backend Abstraction:**
Byobu abstracts tmux and screen differences through:
- Common configuration interface
- Unified keybinding system
- Backend-specific profile loading

**Status Module Architecture:**
- Modular design with independent scripts
- Common library functions for colors/formatting
- Caching system for performance
- Cross-platform compatibility

**Configuration Management:**
- Hierarchical configuration system
- User overrides system defaults
- Profile-based customization
- Environment variable integration

## Troubleshooting

### Common Issues

**Permission Issues:**
```bash
# Ensure you own your $HOME directory
ls -la $HOME
# If not, byobu will show an error about ownership
```

**Backend Issues:**
```bash
# Check available backends
which tmux screen

# Select backend explicitly
byobu-select-backend
```

**Status Module Issues:**
```bash
# Test individual status modules
/usr/lib/byobu/battery
/usr/lib/byobu/memory

# Check status configuration
cat ~/.byobu/statusrc
```

**Build Issues:**
```bash
# Ensure dependencies are installed
# Check autotools are available
autoconf --version
automake --version
```

### Debug Mode

Enable debug output:
```bash
export BYOBU_DEBUG=1
byobu
```

### Log Files

Check log files in:
- `~/.byobu/`
- `/tmp/byobu-*`

## Additional Resources

### Documentation Files

- [STATUS_MODULES.md](STATUS_MODULES.md) - Detailed status module guide
- [CONFIGURATION.md](CONFIGURATION.md) - Comprehensive configuration guide  
- [DEVELOPMENT.md](DEVELOPMENT.md) - Developer setup and contribution guide

### External Links

- [Official Byobu Website](http://byobu.org)
- [Upstream Repository](https://github.com/dustinkirkland/byobu)
- [GNU Screen Manual](https://www.gnu.org/software/screen/manual/)
- [Tmux Manual](https://github.com/tmux/tmux/wiki)

### Community

- GitHub Issues for bug reports and feature requests
- Contribute via pull requests
- Follow coding standards and testing guidelines

---

*This wiki covers the RyoNakagami/byobu fork. For upstream byobu documentation, see the official website.*
