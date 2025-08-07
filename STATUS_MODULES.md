# Byobu Status Modules Guide

This guide provides comprehensive documentation for all status modules available in Byobu.

## Overview

Byobu's status bar system is built around modular, independent scripts that display system information. Each module is a standalone executable that can be run independently or as part of the status bar.

## Status Module Architecture

### Module Structure

Each status module follows a consistent pattern:

```bash
#!/bin/sh -e
# Module header with copyright and license

__module_name_detail() {
    # Detailed information function
    # Called when user requests detailed view
}

__module_name() {
    # Main display function
    # Returns formatted status information
}
```

### Common Functions

Status modules use shared functions from `/usr/lib/byobu/include/`:

- `color` - Color formatting
- `$BYOBU_TEST` - Cross-platform command testing
- `$BYOBU_SED` - Cross-platform sed command
- Caching functions for performance

## System Information Modules

### arch
**Purpose:** Display system architecture  
**Output:** System architecture (e.g., x86_64, arm64)  
**Usage:** Shows the processor architecture of the current system

### distro  
**Purpose:** Display Linux distribution information  
**Output:** Distribution name and version  
**Usage:** Identifies the Linux distribution (Ubuntu, Debian, CentOS, etc.)

### hostname
**Purpose:** Display system hostname  
**Output:** System hostname  
**Usage:** Shows the network name of the current system

### release
**Purpose:** Display OS release information  
**Output:** Operating system release details  
**Usage:** Shows detailed OS version information

### whoami
**Purpose:** Display current username  
**Output:** Current user's username  
**Usage:** Shows who is currently logged in

## Hardware Monitoring Modules

### battery
**Purpose:** Monitor battery status and charge level  
**Output:** Battery percentage and charging status  
**Features:**
- Cross-platform support (Linux, macOS, Android Termux)
- Color-coded based on charge level:
  - Red: < 33%
  - Yellow: 33-67%  
  - Green: > 67%
- Charging indicators: `+` (charging), `-` (discharging), `=` (full)

**Example Output:** `85%+` (85% charged, charging)

**Implementation Details:**
- Linux: Uses `/sys/class/power_supply/*` and `/proc/acpi/battery/*`
- macOS: Uses `ioreg` command
- Android: Uses `termux-battery-status`

### cpu_count
**Purpose:** Display number of CPU cores  
**Output:** Number of processors (e.g., `4x` for 4 cores)  
**Usage:** Shows available CPU cores, omits display for single-core systems

**Implementation:** Uses `getconf _NPROCESSORS_ONLN` or `/proc/cpuinfo`

### cpu_freq
**Purpose:** Display CPU frequency  
**Output:** Current CPU frequency  
**Usage:** Shows processor speed information

### cpu_temp
**Purpose:** Display CPU temperature  
**Output:** CPU temperature in Celsius/Fahrenheit  
**Usage:** Monitor processor thermal status

### fan_speed
**Purpose:** Display system fan speed  
**Output:** Fan RPM information  
**Usage:** Monitor cooling system status

### memory
**Purpose:** Display memory usage  
**Output:** Used/total memory information  
**Usage:** Monitor RAM utilization

### swap
**Purpose:** Display swap usage  
**Output:** Swap space utilization  
**Usage:** Monitor virtual memory usage

## Network Modules

### ip_address
**Purpose:** Display IP address information  
**Output:** Current IP address(es)  
**Usage:** Shows network connectivity status

**Features:**
- Multiple interface support
- IPv4 and IPv6 support
- Public/private IP detection

### network
**Purpose:** Display network interface status  
**Output:** Network interface information  
**Usage:** Monitor network connectivity

### wifi_quality
**Purpose:** Display WiFi signal quality  
**Output:** WiFi signal strength and quality  
**Usage:** Monitor wireless connection status

## Storage Modules

### disk
**Purpose:** Display disk usage  
**Output:** Disk space utilization  
**Usage:** Monitor filesystem usage

### disk_io
**Purpose:** Display disk I/O statistics  
**Output:** Read/write activity  
**Usage:** Monitor storage performance

### raid
**Purpose:** Display RAID status  
**Output:** RAID array health information  
**Usage:** Monitor storage array status

## System Status Modules

### load_average
**Purpose:** Display system load average  
**Output:** 1, 5, and 15-minute load averages  
**Usage:** Monitor system performance

### processes
**Purpose:** Display running processes count  
**Output:** Number of active processes  
**Usage:** Monitor system activity

### uptime
**Purpose:** Display system uptime  
**Output:** Time since last boot  
**Usage:** Monitor system stability

### users
**Purpose:** Display logged-in users  
**Output:** Number of active users  
**Usage:** Monitor user activity

### entropy
**Purpose:** Display available entropy  
**Output:** System entropy pool status  
**Usage:** Monitor cryptographic randomness

### updates_available
**Purpose:** Display available package updates  
**Output:** Number of pending updates  
**Usage:** Monitor system maintenance needs

### reboot_required
**Purpose:** Display reboot requirement status  
**Output:** Indicator if reboot is needed  
**Usage:** Monitor system maintenance needs

## Application Modules

### mail
**Purpose:** Display mail status  
**Output:** Unread mail count  
**Usage:** Monitor email notifications

### services
**Purpose:** Display service status  
**Output:** System service information  
**Usage:** Monitor daemon status

### trash
**Purpose:** Display trash status  
**Output:** Items in trash  
**Usage:** Monitor desktop cleanup needs

## Time and Date Modules

### date
**Purpose:** Display current date  
**Output:** Formatted date string  
**Usage:** Show current date in status bar

### time
**Purpose:** Display current time  
**Output:** Formatted time string  
**Usage:** Show current time in status bar

### time_binary
**Purpose:** Display binary clock  
**Output:** Time in binary format  
**Usage:** Novelty time display

### time_utc
**Purpose:** Display UTC time  
**Output:** UTC time string  
**Usage:** Show universal time

## Utility Modules

### custom
**Purpose:** Display custom user information  
**Output:** User-defined content  
**Usage:** Extensibility for custom status

### logo
**Purpose:** Display distribution logo  
**Output:** ASCII art or text logo  
**Usage:** Branding and identification

### menu
**Purpose:** Provide interactive menu  
**Output:** Menu interface  
**Usage:** Quick access to functions

### notify_osd
**Purpose:** Desktop notifications  
**Output:** Notification integration  
**Usage:** Desktop environment integration

### session
**Purpose:** Display session information  
**Output:** Current session details  
**Usage:** Session management

## Configuration

### Enabling/Disabling Modules

Use the interactive configuration:
```bash
byobu-config
```

### Manual Configuration

Edit status configuration files:
- `~/.byobu/statusrc` - Main status configuration
- `~/.byobu/status` - Status module settings

### Status Bar Layout

Configure which modules appear and their order:
```bash
# Example statusrc configuration
BYOBU_STATUS_LEFT="logo distro release"
BYOBU_STATUS_RIGHT="network disk memory load_average time date"
```

## Creating Custom Status Modules

### Basic Custom Module

Create a script in `~/.byobu/bin/`:

```bash
#!/bin/sh -e
# ~/.byobu/bin/my_custom_module

__my_custom_module_detail() {
    echo "Detailed information about my custom module"
}

__my_custom_module() {
    # Your custom logic here
    echo "Custom: $(date +%s)"
}

# Call the main function
__my_custom_module "$@"
```

### Advanced Custom Module

```bash
#!/bin/sh -e
# ~/.byobu/bin/advanced_custom

. "${BYOBU_PREFIX}/lib/byobu/include/common"

__advanced_custom_detail() {
    echo "Advanced Custom Module Details:"
    echo "- Feature 1: Active"
    echo "- Feature 2: Inactive"
    echo "- Status: OK"
}

__advanced_custom() {
    local status color_code
    
    # Your logic here
    status="OK"
    
    # Use color coding
    case "$status" in
        "OK") color_code="G k" ;;      # Green
        "WARNING") color_code="Y k" ;;  # Yellow  
        "ERROR") color_code="R w" ;;    # Red
    esac
    
    color $color_code
    printf "Custom: %s" "$status"
    color --
}

__advanced_custom "$@"
```

### Integration

1. Make the script executable:
   ```bash
   chmod +x ~/.byobu/bin/my_custom_module
   ```

2. Add to status configuration:
   ```bash
   echo "my_custom_module" >> ~/.byobu/status
   ```

3. Restart byobu to see changes

## Performance Considerations

### Caching

Status modules support caching to improve performance:

```bash
# Check if cached result is still valid
if [ -f "$BYOBU_RUN_DIR/status.$BYOBU_BACKEND/my_module" ]; then
    # Use cached result if recent enough
    if [ $(stat -c %Y "$cache_file") -gt $(($(date +%s) - 60)) ]; then
        cat "$cache_file"
        return
    fi
fi

# Generate new result and cache it
result=$(expensive_operation)
echo "$result" > "$BYOBU_RUN_DIR/status.$BYOBU_BACKEND/my_module"
echo "$result"
```

### Optimization Tips

1. **Minimize external command calls**
2. **Use caching for expensive operations**
3. **Test cross-platform compatibility**
4. **Handle errors gracefully**
5. **Keep output concise**

## Troubleshooting

### Testing Individual Modules

Run modules directly:
```bash
/usr/lib/byobu/battery
/usr/lib/byobu/memory
~/.byobu/bin/my_custom_module
```

### Debug Mode

Enable debug output:
```bash
export BYOBU_DEBUG=1
byobu-status
```

### Common Issues

1. **Module not appearing:** Check permissions and syntax
2. **Performance issues:** Implement caching
3. **Cross-platform issues:** Test on different systems
4. **Color issues:** Verify color code usage

## Reference

### Color Codes

Common color combinations:
- `G k` - Green text on black background
- `R w` - Red text on white background  
- `Y k` - Yellow text on black background
- `b G w` - Bold green text on white background

### Environment Variables

Key variables available to modules:
- `$BYOBU_PREFIX` - Installation prefix
- `$BYOBU_BACKEND` - Current backend (tmux/screen)
- `$BYOBU_RUN_DIR` - Runtime directory
- `$BYOBU_CONFIG_DIR` - Configuration directory

---

*For more information, see the main [WIKI.md](WIKI.md) and [DEVELOPMENT.md](DEVELOPMENT.md) documentation.*
