# Byobu Configuration Guide

This comprehensive guide covers all aspects of configuring and customizing Byobu to suit your needs.

## Table of Contents

1. [Configuration Overview](#configuration-overview)
2. [Configuration Files](#configuration-files)
3. [Interactive Configuration](#interactive-configuration)
4. [Backend Configuration](#backend-configuration)
5. [Status Bar Configuration](#status-bar-configuration)
6. [Profile System](#profile-system)
7. [Color Schemes](#color-schemes)
8. [Keybindings](#keybindings)
9. [Session Management](#session-management)
10. [Environment Variables](#environment-variables)
11. [Comprehensive byoburc Configuration](#comprehensive-byoburc-configuration)
12. [Advanced Configuration](#advanced-configuration)
13. [Performance Optimization](#performance-optimization)
14. [Troubleshooting Configuration](#troubleshooting-configuration)

## Configuration Overview

Byobu uses a hierarchical configuration system that allows for:
- System-wide defaults
- User-specific overrides
- Profile-based configurations
- Backend-specific settings
- Runtime customizations

### Configuration Hierarchy

1. **System defaults** (`/usr/share/byobu/`)
2. **User configuration** (`~/.byobu/`)
3. **Runtime settings** (environment variables)
4. **Command-line options**

## Configuration Files

### Main Configuration Directory

All user configuration is stored in `~/.byobu/`:

```
~/.byobu/
├── .byoburc              # Main configuration file
├── backend               # Backend selection
├── color.tmux           # Tmux color configuration
├── datetime.tmux        # Date/time format configuration
├── keybindings.tmux     # Tmux keybindings
├── profile.tmux         # Tmux profile settings
├── statusrc             # Status bar configuration
├── .tmux.conf           # User tmux configuration
├── .screenrc            # User screen configuration
├── windows.tmux         # Custom window layouts
└── bin/                 # Custom status modules
```

### Core Configuration Files

#### ~/.byoburc
Main configuration file for environment variables and global settings. This file is sourced early in the byobu startup process and by most byobu utilities.

**Loading Order:**
1. Sourced by `byobu.in` during startup
2. Loaded before backend selection and profile initialization
3. Available to all byobu scripts and status modules

**Basic Configuration:**
```bash
# Backend selection
export BYOBU_BACKEND=tmux

# Installation prefix (auto-generated)
export BYOBU_PREFIX='/home/user/byobu'

# Python interpreter
export BYOBU_PYTHON='/usr/bin/env python3'

# Custom settings
export BYOBU_CHARMAP=UTF-8
export BYOBU_TERM=screen-256color
```

**For comprehensive byoburc documentation with all environment variables and advanced examples, see [Comprehensive byoburc Configuration](#comprehensive-byoburc-configuration).**

#### ~/.byobu/backend
Simple backend selection:
```bash
BYOBU_BACKEND=tmux
```

#### ~/.byobu/statusrc
Status bar configuration:
```bash
# Status modules for left side
BYOBU_STATUS_LEFT="logo distro release"

# Status modules for right side  
BYOBU_STATUS_RIGHT="network disk memory load_average time date"

# Status refresh frequency
BYOBU_STATUS_REFRESH=5
```

## Interactive Configuration

### byobu-config

The primary configuration tool:
```bash
byobu-config
```

This launches an interactive menu with options for:

1. **Help** - View help information
2. **Toggle status notifications** - Enable/disable status modules
3. **Change escape sequence** - Modify command prefix key
4. **Create/manage a shared session** - Session sharing setup
5. **Enable/disable automatic updates** - Update management
6. **Change background color** - Color scheme selection
7. **Change foreground color** - Text color selection
8. **Toggle UTF-8 support** - Character encoding
9. **Exit** - Save and exit

### Navigation

- Use arrow keys or vim-style navigation (j/k)
- Enter to select options
- Tab to navigate between elements
- Escape or 'q' to exit

## Backend Configuration

### Selecting Backend

Choose between tmux and screen:

```bash
# Interactive selection
byobu-select-backend

# Direct selection
echo "BYOBU_BACKEND=tmux" >> ~/.byoburc
# or
echo "BYOBU_BACKEND=screen" >> ~/.byoburc
```

### Tmux Configuration

#### ~/.byobu/profile.tmux
Tmux-specific profile settings:
```bash
# Set default shell
set-option -g default-shell /bin/bash

# Set terminal type
set-option -g default-terminal "screen-256color"

# Enable mouse support
set-option -g mouse on

# Set history limit
set-option -g history-limit 10000
```

#### ~/.byobu/keybindings.tmux
Custom tmux keybindings:
```bash
# Custom prefix key
set-option -g prefix C-a
unbind-key C-b
bind-key C-a send-prefix

# Window navigation
bind-key -n F2 new-window
bind-key -n F3 previous-window
bind-key -n F4 next-window

# Pane management
bind-key | split-window -h
bind-key - split-window -v
```

### Screen Configuration

#### ~/.byobu/.screenrc
Screen-specific configuration:
```bash
# Set default shell
shell /bin/bash

# Set terminal type
term screen-256color

# Set scrollback buffer
defscrollback 10000

# Enable visual bell
vbell on

# Set caption (status line)
caption always "%{= kw}%-w%{= BW}%n %t%{-}%+w %-= @%H - %LD %d %LM - %c"
```

## Status Bar Configuration

### Status Module Selection

#### Interactive Method
```bash
byobu-config
# Select "Toggle status notifications"
```

#### Manual Method
Edit `~/.byobu/statusrc`:
```bash
# Left side modules
BYOBU_STATUS_LEFT="logo distro release #arch"

# Right side modules
BYOBU_STATUS_RIGHT="network #disk_io custom #entropy raid reboot_required updates_available #apport #services #mail users uptime #ec2_cost #rcs_cost #fan_speed #cpu_temp battery wifi_quality #processes load_average cpu_count memory #swap #disk #time_utc date time"

# Refresh interval (seconds)
BYOBU_STATUS_REFRESH=5
```

### Available Status Modules

**System Information:**
- `logo` - Distribution logo
- `distro` - Distribution name
- `release` - OS release
- `arch` - System architecture
- `hostname` - System hostname

**Hardware:**
- `cpu_count` - CPU core count
- `cpu_freq` - CPU frequency
- `cpu_temp` - CPU temperature
- `memory` - Memory usage
- `swap` - Swap usage
- `battery` - Battery status
- `fan_speed` - Fan speed

**Network:**
- `network` - Network status
- `ip_address` - IP address
- `wifi_quality` - WiFi signal

**Storage:**
- `disk` - Disk usage
- `disk_io` - Disk I/O
- `raid` - RAID status

**System Status:**
- `load_average` - System load
- `uptime` - System uptime
- `processes` - Process count
- `users` - Logged users
- `updates_available` - Available updates
- `reboot_required` - Reboot needed

**Time/Date:**
- `date` - Current date
- `time` - Current time
- `time_utc` - UTC time

### Custom Status Modules

Create custom modules in `~/.byobu/bin/`:

```bash
#!/bin/sh -e
# ~/.byobu/bin/my_status

__my_status() {
    echo "Custom: $(uptime | cut -d',' -f1)"
}

__my_status "$@"
```

Make executable and add to statusrc:
```bash
chmod +x ~/.byobu/bin/my_status
echo "my_status" >> ~/.byobu/statusrc
```

## Profile System

### Available Profiles

Byobu includes several built-in profiles:

- `tmuxrc` - Tmux configuration profile
- `screenrc` - Screen configuration profile
- `bashrc` - Bash shell customizations
- `common` - Shared settings
- `byoburc` - Byobu-specific settings

### Profile Selection

```bash
byobu-select-profile
```

### Custom Profiles

Create custom profiles by copying and modifying existing ones:

```bash
# Copy existing profile
cp /usr/share/byobu/profiles/tmuxrc ~/.byobu/my-profile.tmux

# Edit custom profile
vim ~/.byobu/my-profile.tmux

# Use custom profile
echo "source-file ~/.byobu/my-profile.tmux" >> ~/.byobu/.tmux.conf
```

## Color Schemes

### Color Configuration

#### Tmux Colors
Edit `~/.byobu/color.tmux`:
```bash
# Status bar colors
set-option -g status-style bg=black,fg=white

# Window status colors
set-window-option -g window-status-style bg=black,fg=cyan
set-window-option -g window-status-current-style bg=red,fg=white

# Pane border colors
set-option -g pane-border-style fg=green
set-option -g pane-active-border-style fg=red
```

#### Screen Colors
Edit `~/.byobu/.screenrc`:
```bash
# Status line colors
caption always "%{= kw}%-w%{= BW}%n %t%{-}%+w %-= @%H - %LD %d %LM - %c"

# Color definitions
# k=black, r=red, g=green, y=yellow, b=blue, m=magenta, c=cyan, w=white
# Uppercase = bright colors
```

### Predefined Color Schemes

Byobu includes several color schemes accessible through `byobu-config`:

1. **Default** - Standard black/white theme
2. **Dark** - Dark background theme
3. **Light** - Light background theme
4. **Solarized** - Solarized color scheme
5. **Custom** - User-defined colors

### Custom Color Schemes

Create custom color schemes:

```bash
# ~/.byobu/colors/my-theme.tmux
set-option -g status-style bg=colour235,fg=colour250
set-window-option -g window-status-style bg=colour235,fg=colour244
set-window-option -g window-status-current-style bg=colour166,fg=colour235
```

Load custom theme:
```bash
echo "source-file ~/.byobu/colors/my-theme.tmux" >> ~/.byobu/color.tmux
```

## Keybindings

### Default Keybindings

**Common (Tmux/Screen):**
- `Ctrl-a` - Command prefix
- `Ctrl-a c` - Create new window
- `Ctrl-a n` - Next window
- `Ctrl-a p` - Previous window
- `Ctrl-a d` - Detach session
- `Ctrl-a ?` - Help

**Tmux-specific:**
- `Ctrl-a |` - Split horizontally
- `Ctrl-a -` - Split vertically
- `Ctrl-a o` - Switch panes

### Custom Keybindings

#### Tmux Keybindings
Edit `~/.byobu/keybindings.tmux`:
```bash
# Change prefix key
set-option -g prefix C-b
unbind-key C-a

# Custom window management
bind-key -n F1 select-window -t 1
bind-key -n F2 select-window -t 2
bind-key -n F3 select-window -t 3

# Custom pane management
bind-key h select-pane -L
bind-key j select-pane -D
bind-key k select-pane -U
bind-key l select-pane -R

# Reload configuration
bind-key r source-file ~/.tmux.conf \; display-message "Config reloaded!"
```

#### Screen Keybindings
Edit `~/.byobu/.screenrc`:
```bash
# Change escape key
escape ^Bb

# Custom bindings
bind j focus down
bind k focus up
bind h focus left
bind l focus right

# Function key bindings
bindkey -k F1 select 1
bindkey -k F2 select 2
bindkey -k F3 select 3
```

## Session Management

### Window Layouts

#### Custom Window Sets
Create `~/.byobu/windows.tmux`:
```bash
new-window -n "editor" vim
new-window -n "server" ssh user@server
new-window -n "logs" tail -f /var/log/syslog
new-window -n "monitor" htop
```

#### Layout Management
```bash
# Save current layout
byobu-layout save my-layout

# Load saved layout
byobu-layout load my-layout

# List available layouts
byobu-layout list
```

### Session Scripts

Create session startup scripts:

```bash
#!/bin/bash
# ~/.byobu/sessions/development

# Start development session
byobu new-session -d -s development

# Create windows
byobu new-window -t development:1 -n 'editor' 'vim'
byobu new-window -t development:2 -n 'server' 'cd ~/project && npm start'
byobu new-window -t development:3 -n 'git' 'cd ~/project && git status'

# Attach to session
byobu attach-session -t development
```

## Environment Variables

### Core Variables

```bash
# Installation and runtime
export BYOBU_PREFIX="/usr/local"
export BYOBU_BACKEND="tmux"
export BYOBU_PYTHON="/usr/bin/python3"

# Terminal settings
export BYOBU_TERM="screen-256color"
export BYOBU_CHARMAP="UTF-8"

# Directories
export BYOBU_CONFIG_DIR="$HOME/.byobu"
export BYOBU_RUN_DIR="/tmp/byobu-$USER"

# Status settings
export BYOBU_STATUS_REFRESH="5"
export BYOBU_NO_TITLE="1"  # Disable window title updates
```

### Customization Variables

```bash
# Custom window name
export BYOBU_WINDOW_NAME="shell"

# Custom title format
export BYOBU_ALT_TITLE='${USER}@${HOSTNAME} - Custom Title'

# Disable specific features
export BYOBU_DISABLE_AUTOLAUNCH="1"
export BYOBU_QUIET="1"
```

## Comprehensive byoburc Configuration

The `~/.byoburc` file is the primary configuration file for byobu environment variables and global settings. It's sourced early in the startup process and provides the foundation for all byobu functionality.

### Core Environment Variables

#### Backend and Installation Settings
```bash
# Backend selection (tmux or screen)
export BYOBU_BACKEND=tmux

# Installation prefix (usually auto-detected)
export BYOBU_PREFIX="/usr/local"

# Python interpreter for configuration tools
export BYOBU_PYTHON="/usr/bin/env python3"
```

#### Terminal and Display Settings
```bash
# Terminal type for proper color and feature support
export BYOBU_TERM="screen-256color"

# Character encoding
export BYOBU_CHARMAP="UTF-8"

# Default window name
export BYOBU_WINDOW_NAME="shell"

# Date and time formats
export BYOBU_DATE="%Y-%m-%d"
export BYOBU_TIME="%H:%M:%S"
```

#### Directory Configuration
```bash
# Configuration directory (usually ~/.byobu)
export BYOBU_CONFIG_DIR="$HOME/.byobu"

# Runtime cache directory (usually /dev/shm/byobu-$USER-*)
export BYOBU_RUN_DIR="/tmp/byobu-$USER"
```

#### Status Bar Configuration
```bash
# Left side status modules
export BYOBU_STATUS_LEFT="logo distro release"

# Right side status modules
export BYOBU_STATUS_RIGHT="network disk memory load_average time date"

# Status refresh interval in seconds
export BYOBU_STATUS_REFRESH="5"
```

#### Feature Control Variables
```bash
# Enable debug output
export BYOBU_DEBUG=1

# Suppress informational messages
export BYOBU_QUIET=1

# Disable automatic launching
export BYOBU_DISABLE_AUTOLAUNCH=1

# Disable window title updates
export BYOBU_NO_TITLE=1

# Custom window title format
export BYOBU_ALT_TITLE='${USER}@${HOSTNAME} - Custom'
```

#### Cross-Platform Compatibility
```bash
# macOS-specific settings
export BYOBU_SED="gsed"
export BYOBU_READLINK="greadlink"

# Pager and editor preferences
export BYOBU_PAGER="less"
export BYOBU_EDITOR="vim"

# Resource limits support
export BYOBU_ULIMIT="ulimit"
```

### Complete Example Configuration

```bash
#!/bin/bash
# ~/.byoburc - Comprehensive byobu configuration

# Core backend and installation
export BYOBU_BACKEND=tmux
export BYOBU_PREFIX="/usr/local"
export BYOBU_PYTHON="/usr/bin/env python3"

# Terminal settings
export BYOBU_TERM="tmux-256color"
export BYOBU_CHARMAP="UTF-8"

# Custom date/time formats
export BYOBU_DATE="%a %Y-%m-%d"
export BYOBU_TIME="%H:%M:%S %Z"

# Status bar configuration
export BYOBU_STATUS_LEFT="logo distro release arch"
export BYOBU_STATUS_RIGHT="network battery memory load_average time date"
export BYOBU_STATUS_REFRESH=3

# Performance optimization
export BYOBU_QUIET=1
export BYOBU_NO_TITLE=1

# Conditional configuration based on environment
if [ -n "$SSH_CONNECTION" ]; then
    # SSH session - minimal status bar
    export BYOBU_STATUS_RIGHT="load_average time"
fi

# Host-specific configuration
case "$(hostname)" in
    "server"*)
        export BYOBU_STATUS_RIGHT="network load_average memory time"
        export BYOBU_STATUS_REFRESH=10
        ;;
    "laptop"*)
        export BYOBU_STATUS_RIGHT="battery wifi_quality memory time"
        export BYOBU_STATUS_REFRESH=5
        ;;
    "desktop"*)
        export BYOBU_STATUS_RIGHT="cpu_temp fan_speed memory load_average time"
        ;;
esac

# Development environment detection
if [ -d "$HOME/dev" ] || [ -n "$DEVELOPMENT" ]; then
    export BYOBU_WINDOW_NAME="dev"
    export BYOBU_STATUS_LEFT="logo custom"
fi

# Auto-launch configuration
if [ -z "$BYOBU_LAUNCHED" ] && [ -n "$SSH_CONNECTION" ]; then
    export BYOBU_LAUNCHED=1
    exec byobu
fi
```

### Environment Variable Reference

| Variable | Purpose | Default | Example |
|----------|---------|---------|---------|
| `BYOBU_BACKEND` | Backend selection | `tmux` | `screen`, `tmux` |
| `BYOBU_PREFIX` | Installation path | `/usr` | `/usr/local`, `$HOME/byobu` |
| `BYOBU_PYTHON` | Python interpreter | Auto-detected | `/usr/bin/python3` |
| `BYOBU_TERM` | Terminal type | `screen-256color` | `tmux-256color` |
| `BYOBU_CHARMAP` | Character encoding | Auto-detected | `UTF-8` |
| `BYOBU_CONFIG_DIR` | Config directory | `~/.byobu` | `~/.config/byobu` |
| `BYOBU_RUN_DIR` | Runtime cache | `/dev/shm/byobu-$USER-*` | `/tmp/byobu-$USER` |
| `BYOBU_STATUS_LEFT` | Left status modules | `logo distro release` | Custom module list |
| `BYOBU_STATUS_RIGHT` | Right status modules | Default set | Custom module list |
| `BYOBU_STATUS_REFRESH` | Refresh interval | `5` | `1-300` seconds |
| `BYOBU_WINDOW_NAME` | Default window name | `-` | `shell`, `dev` |
| `BYOBU_DATE` | Date format | `%Y-%m-%d` | `%a %b %d` |
| `BYOBU_TIME` | Time format | `%H:%M:%S` | `%I:%M %p` |
| `BYOBU_DEBUG` | Debug mode | Unset | `1` |
| `BYOBU_QUIET` | Suppress messages | Unset | `1` |
| `BYOBU_NO_TITLE` | Disable title updates | Unset | `1` |
| `BYOBU_DISABLE_AUTOLAUNCH` | Disable auto-launch | Unset | `1` |

### Loading Process and Precedence

1. **System defaults** from `/etc/byobu/`
2. **User byoburc** from `~/.byoburc` (highest priority for environment variables)
3. **Backend selection** from `~/.byobu/backend`
4. **Status configuration** from `~/.byobu/statusrc`
5. **Profile settings** from selected profile
6. **Runtime overrides** from command line or environment

### Best Practices

#### Organization
```bash
# Group related settings together
# Core settings
export BYOBU_BACKEND=tmux
export BYOBU_PREFIX="/usr/local"

# Display settings
export BYOBU_TERM="tmux-256color"
export BYOBU_CHARMAP="UTF-8"

# Status bar
export BYOBU_STATUS_LEFT="logo distro"
export BYOBU_STATUS_RIGHT="memory time"
```

#### Comments and Documentation
```bash
# Use comments to explain complex logic
if [ "$(uname)" = "Darwin" ]; then
    # macOS requires different sed command
    export BYOBU_SED="gsed"
fi
```

#### Error Handling
```bash
# Check for required commands
if command -v python3 >/dev/null 2>&1; then
    export BYOBU_PYTHON="python3"
elif command -v python >/dev/null 2>&1; then
    export BYOBU_PYTHON="python"
fi
```

### Troubleshooting byoburc

#### Debug Configuration Loading
```bash
# Enable debug mode
export BYOBU_DEBUG=1
byobu

# Check if byoburc is being loaded
echo "Byoburc loaded" >> ~/.byoburc
```

#### Common Issues

1. **Syntax errors**: Use `bash -n ~/.byoburc` to check syntax
2. **Variable conflicts**: Check for conflicting environment variables
3. **Path issues**: Verify `BYOBU_PREFIX` points to correct installation
4. **Permission problems**: Ensure `~/.byoburc` is readable

#### Backup and Recovery
```bash
# Backup current configuration
cp ~/.byoburc ~/.byoburc.backup

# Reset to defaults (byobu will recreate)
mv ~/.byoburc ~/.byoburc.old

# Restore from backup
cp ~/.byoburc.backup ~/.byoburc
```

## Advanced Configuration

### Multi-Backend Setup

Run different configurations for tmux and screen:

```bash
# ~/.byobu/backend-tmux
BYOBU_BACKEND=tmux
source ~/.byobu/tmux-specific-config

# ~/.byobu/backend-screen  
BYOBU_BACKEND=screen
source ~/.byobu/screen-specific-config
```

### Conditional Configuration

```bash
# ~/.byoburc
if [ "$BYOBU_BACKEND" = "tmux" ]; then
    export BYOBU_TERM="tmux-256color"
else
    export BYOBU_TERM="screen-256color"
fi

# Host-specific configuration
case "$(hostname)" in
    "server1")
        export BYOBU_STATUS_RIGHT="load_average memory time"
        ;;
    "laptop")
        export BYOBU_STATUS_RIGHT="battery wifi_quality time"
        ;;
esac
```

### Integration with Shell

#### Bash Integration
Add to `~/.bashrc`:
```bash
# Auto-launch byobu
if [ -z "$BYOBU_LAUNCHED" ]; then
    export BYOBU_LAUNCHED=1
    exec byobu
fi

# Custom prompt in byobu
if [ -n "$BYOBU_BACKEND" ]; then
    PS1="\[\e[38;5;170m\]\u\[\e[00m\]@\[\e[38;5;98m\]\h\[\e[00m\]:\[\e[38;5;63m\]\w\[\e[00m\]\$ "
fi
```

#### Zsh Integration
Add to `~/.zshrc`:
```bash
# Byobu integration
if [[ -z "$BYOBU_LAUNCHED" && -n "$SSH_CONNECTION" ]]; then
    export BYOBU_LAUNCHED=1
    exec byobu
fi
```

### Performance Optimization

#### Status Module Caching
```bash
# ~/.byobu/statusrc
# Reduce refresh frequency for expensive modules
BYOBU_STATUS_REFRESH=10

# Disable unused modules
BYOBU_STATUS_RIGHT="memory load_average time"
```

#### Resource Limits
```bash
# ~/.byoburc
# Optimize for low-resource systems
export BYOBU_TERM="screen"  # Use basic terminal
export BYOBU_STATUS_REFRESH=30  # Slower refresh
```

## Troubleshooting Configuration

### Debugging Configuration

```bash
# Enable debug mode
export BYOBU_DEBUG=1
byobu

# Check configuration loading
byobu-config --help

# Validate configuration files
sh -n ~/.byobu/.tmux.conf
```

### Common Configuration Issues

1. **Colors not working:**
   ```bash
   # Check terminal capabilities
   echo $TERM
   tput colors
   
   # Set appropriate terminal type
   export BYOBU_TERM="screen-256color"
   ```

2. **Keybindings not working:**
   ```bash
   # Check for conflicts
   tmux list-keys | grep "your-key"
   
   # Reload configuration
   tmux source-file ~/.tmux.conf
   ```

3. **Status modules not appearing:**
   ```bash
   # Test module directly
   /usr/lib/byobu/battery
   
   # Check statusrc syntax
   cat ~/.byobu/statusrc
   ```

### Reset Configuration

```bash
# Backup current configuration
mv ~/.byobu ~/.byobu.backup

# Reset to defaults
byobu-config

# Restore specific settings
cp ~/.byobu.backup/statusrc ~/.byobu/
```

---

*For more information, see the main [WIKI.md](WIKI.md) and [STATUS_MODULES.md](STATUS_MODULES.md) documentation.*
