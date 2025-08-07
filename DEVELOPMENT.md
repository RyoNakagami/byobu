# Byobu Development Guide

This guide provides comprehensive information for developers who want to contribute to, modify, or extend Byobu.

## Table of Contents

1. [Development Environment Setup](#development-environment-setup)
2. [Project Architecture](#project-architecture)
3. [Build System](#build-system)
4. [Code Structure](#code-structure)
5. [Status Module Development](#status-module-development)
6. [Testing](#testing)
7. [Contributing](#contributing)
8. [Debugging](#debugging)
9. [Cross-Platform Development](#cross-platform-development)
10. [Release Process](#release-process)

## Development Environment Setup

### Prerequisites

Install required development tools:

```bash
# Ubuntu/Debian
sudo apt-get install autotools-dev autoconf automake build-essential
sudo apt-get install tmux screen python3 python3-newt
sudo apt-get install shellcheck  # For shell script linting

# macOS
brew install autoconf automake tmux screen python3
brew install shellcheck

# CentOS/RHEL
sudo yum install autoconf automake gcc make
sudo yum install tmux screen python3
```

### Development Setup

1. **Clone the repository:**
   ```bash
   git clone https://github.com/RyoNakagami/byobu.git
   cd byobu
   ```

2. **Set up development build:**
   ```bash
   # Generate build scripts
   ./autogen.sh
   
   # Configure for development
   ./configure --prefix="$HOME/byobu-dev" --enable-debug
   
   # Build
   make
   
   # Install to development directory
   make install
   ```

3. **Set up development environment:**
   ```bash
   # Add to PATH
   export PATH="$HOME/byobu-dev/bin:$PATH"
   export BYOBU_PREFIX="$HOME/byobu-dev"
   
   # Create development config
   mkdir -p ~/.byobu-dev
   export BYOBU_CONFIG_DIR="$HOME/.byobu-dev"
   ```

### IDE Setup

#### VS Code Configuration
Create `.vscode/settings.json`:
```json
{
    "shellcheck.enable": true,
    "shellcheck.run": "onType",
    "files.associations": {
        "*.in": "shellscript",
        "Makefile.am": "makefile"
    },
    "editor.tabSize": 4,
    "editor.insertSpaces": false
}
```

#### Vim Configuration
Add to `.vimrc`:
```vim
" Byobu development settings
autocmd FileType sh setlocal noexpandtab tabstop=4 shiftwidth=4
autocmd BufRead,BufNewFile *.in setfiletype sh
```

## Project Architecture

### Directory Structure

```
byobu/
├── usr/
│   ├── bin/                    # Executable scripts (.in templates)
│   │   ├── byobu.in           # Main byobu wrapper
│   │   ├── byobu-config.in    # Configuration utility
│   │   └── ...                # Other utilities
│   ├── lib/byobu/             # Status modules and libraries
│   │   ├── include/           # Common libraries
│   │   │   ├── common         # Core functions
│   │   │   ├── colors         # Color definitions
│   │   │   └── ...            # Other includes
│   │   ├── battery            # Battery status module
│   │   ├── memory             # Memory status module
│   │   └── ...                # Other status modules
│   └── share/byobu/           # Shared resources
│       ├── profiles/          # Configuration profiles
│       ├── keybindings/       # Keybinding definitions
│       ├── status/            # Status configurations
│       └── ...                # Other resources
├── etc/byobu/                 # System configuration
├── debian/                    # Debian packaging
├── po/                        # Internationalization
├── configure.ac               # Autoconf configuration
├── Makefile.am               # Automake configuration
└── autogen.sh                # Build script generator
```

### Core Components

#### 1. Main Wrapper (`usr/bin/byobu.in`)
- Entry point for all byobu functionality
- Backend detection and selection
- Environment setup and validation
- Session management

#### 2. Status System (`usr/lib/byobu/`)
- Modular status modules
- Common library functions
- Cross-platform compatibility layer

#### 3. Configuration System (`usr/bin/byobu-config.in`)
- Interactive configuration interface
- Profile management
- Settings persistence

#### 4. Profile System (`usr/share/byobu/profiles/`)
- Backend-specific configurations
- Customizable templates
- Default settings

## Build System

### Autotools Overview

Byobu uses GNU Autotools for building:

- **autoconf** - Generates configure script from `configure.ac`
- **automake** - Generates Makefile.in from `Makefile.am`
- **make** - Builds and installs the project

### Key Build Files

#### configure.ac
Main autoconf configuration:
```bash
AC_PREREQ([2.61])
AC_INIT([byobu], [5.121], [http://byobu.org])
AC_CONFIG_SRCDIR([usr/bin/byobu.in])
AM_INIT_AUTOMAKE([foreign])

# Output files - templates (.in) become final files
AC_OUTPUT(Makefile \
    usr/bin/byobu \
    usr/bin/byobu-config \
    usr/lib/byobu/include/config.py \
    # ... other files
)
```

#### Makefile.am Files
Define build rules for each directory:
```makefile
# usr/bin/Makefile.am
bin_SCRIPTS = byobu byobu-config byobu-status
EXTRA_DIST = byobu.in byobu-config.in byobu-status.in

# Template substitution happens automatically
```

### Build Process

1. **Template Processing:**
   - `.in` files are processed by configure
   - `@prefix@` → actual prefix path
   - `@VERSION@` → version number
   - Other substitutions as defined

2. **Script Generation:**
   ```bash
   # byobu.in becomes byobu with substitutions
   sed 's|@prefix@|/usr/local|g' usr/bin/byobu.in > usr/bin/byobu
   ```

3. **Installation:**
   ```bash
   # Files copied to final locations
   install -m 755 usr/bin/byobu $(DESTDIR)/usr/local/bin/
   ```

### Custom Build Targets

Add custom targets to `Makefile.am`:
```makefile
# Development targets
dev-install: install
	@echo "Development installation complete"

test-syntax:
	sh -n `find . -name "*.sh" -o -name "*.in"`
	
lint:
	shellcheck usr/bin/*.in usr/lib/byobu/*

.PHONY: dev-install test-syntax lint
```

## Code Structure

### Coding Standards

1. **Shell Scripts:**
   - Use `#!/bin/sh -e` for POSIX compatibility
   - Use tabs, not spaces (4-character tabs)
   - Follow existing naming conventions
   - Include copyright headers

2. **Python Scripts:**
   - Use `#!/usr/bin/env python3`
   - Follow PEP 8 style guide
   - Include docstrings for functions

3. **Naming Conventions:**
   - Functions: `__module_name()` and `__module_name_detail()`
   - Variables: lowercase with underscores
   - Constants: UPPERCASE with underscores

### Common Library Functions

#### usr/lib/byobu/include/common
Core functions used by all modules:

```bash
# Color functions
color() {
    # Set terminal colors
    # Usage: color "G k" (green on black)
}

# Cross-platform command testing
$BYOBU_TEST command >/dev/null 2>&1 && {
    # Command exists, use it
}

# Caching functions
cache_get() {
    # Retrieve cached value
}

cache_set() {
    # Store value in cache
}
```

#### usr/lib/byobu/include/colors
Color definitions and utilities:

```bash
# Color codes
ESC="\033["
CLEAR="${ESC}0m"

# Standard colors
BLACK="0;30"
RED="0;31"
GREEN="0;32"
# ... etc
```

### Error Handling

Standard error handling patterns:

```bash
#!/bin/sh -e

# Function with error handling
__my_function() {
    local result
    
    # Check prerequisites
    [ -r "/proc/meminfo" ] || return 1
    
    # Safe command execution
    if result=$(grep "MemTotal" /proc/meminfo 2>/dev/null); then
        echo "$result"
    else
        # Graceful fallback
        echo "Memory: N/A"
    fi
}
```

## Status Module Development

### Module Template

Create new status modules using this template:

```bash
#!/bin/sh -e
#
#    module_name: brief description
#
#    Copyright (C) YEAR Your Name
#
#    Authors: Your Name <your.email@example.com>
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU General Public License as published by
#    the Free Software Foundation, version 3 of the License.

__module_name_detail() {
    # Detailed information function
    # Called when user requests detailed view
    echo "Detailed information about module_name"
    echo "Additional details here"
}

__module_name() {
    local value color_code
    
    # Your module logic here
    value=$(get_some_value)
    
    # Color coding based on value
    if [ "$value" -gt 80 ]; then
        color_code="R w"  # Red on white (warning)
    elif [ "$value" -gt 50 ]; then
        color_code="Y k"  # Yellow on black (caution)
    else
        color_code="G k"  # Green on black (normal)
    fi
    
    # Output with color
    color $color_code
    printf "%s" "$value"
    color --
}

# Execute main function
__module_name "$@"

# vi: syntax=sh ts=4 noexpandtab
```

### Advanced Module Features

#### Caching Implementation
```bash
__module_name() {
    local cache_file="$BYOBU_RUN_DIR/status.$BYOBU_BACKEND/module_name"
    local cache_ttl=60  # Cache for 60 seconds
    
    # Check cache validity
    if [ -f "$cache_file" ]; then
        local cache_age=$(($(date +%s) - $(stat -c %Y "$cache_file" 2>/dev/null || echo 0)))
        if [ "$cache_age" -lt "$cache_ttl" ]; then
            cat "$cache_file"
            return
        fi
    fi
    
    # Generate new value
    local result=$(expensive_operation)
    
    # Cache result
    echo "$result" > "$cache_file"
    echo "$result"
}
```

#### Cross-Platform Support
```bash
__module_name() {
    local value
    
    # Linux implementation
    if [ -r "/proc/meminfo" ]; then
        value=$(awk '/MemTotal/ {print $2}' /proc/meminfo)
    # macOS implementation
    elif eval $BYOBU_TEST sysctl >/dev/null 2>&1; then
        value=$(sysctl -n hw.memsize)
        value=$((value / 1024))  # Convert to KB
    # FreeBSD implementation
    elif [ -r "/var/run/dmesg.boot" ]; then
        value=$(grep "real memory" /var/run/dmesg.boot | awk '{print $4}')
    else
        # Fallback
        value="N/A"
    fi
    
    echo "$value"
}
```

### Module Integration

1. **Add to build system:**
   ```bash
   # usr/lib/byobu/Makefile.am
   pkglib_SCRIPTS = ... module_name
   ```

2. **Test module:**
   ```bash
   # Test directly
   ./usr/lib/byobu/module_name
   
   # Test detail function
   ./usr/lib/byobu/module_name --detail
   ```

3. **Add to status configuration:**
   ```bash
   echo "module_name" >> ~/.byobu/status
   ```

## Testing

### Test Framework

#### Syntax Testing
```bash
# Test shell syntax
test_syntax() {
    find . -name "*.sh" -o -name "*.in" | while read -r file; do
        if ! sh -n "$file"; then
            echo "Syntax error in $file"
            return 1
        fi
    done
}
```

#### Unit Testing for Status Modules
```bash
# test/test_battery.sh
test_battery_module() {
    local output
    output=$(./usr/lib/byobu/battery)
    
    # Test output format
    if echo "$output" | grep -q "[0-9]\+%[+-=]"; then
        echo "PASS: Battery module output format correct"
    else
        echo "FAIL: Battery module output format incorrect"
        return 1
    fi
}
```

#### Integration Testing
```bash
# test/test_integration.sh
test_byobu_startup() {
    # Test byobu starts without errors
    timeout 10 byobu-export > /dev/null 2>&1
    if [ $? -eq 0 ]; then
        echo "PASS: Byobu starts successfully"
    else
        echo "FAIL: Byobu startup failed"
        return 1
    fi
}
```

### Automated Testing

#### GitHub Actions Workflow
```yaml
# .github/workflows/test.yml
name: Test
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Install dependencies
      run: |
        sudo apt-get update
        sudo apt-get install autotools-dev autoconf automake tmux screen
    - name: Build
      run: |
        ./autogen.sh
        ./configure
        make
    - name: Test syntax
      run: |
        find . -name "*.sh" -o -name "*.in" | xargs -I {} sh -n {}
    - name: Test modules
      run: |
        for module in usr/lib/byobu/*; do
          [ -x "$module" ] && "$module" >/dev/null
        done
```

### Manual Testing

#### Test Checklist
- [ ] Byobu starts without errors
- [ ] Status modules display correctly
- [ ] Configuration utility works
- [ ] Backend switching works
- [ ] Session management works
- [ ] Cross-platform compatibility
- [ ] Performance is acceptable

#### Platform Testing
Test on multiple platforms:
```bash
# Test script for different platforms
test_platform() {
    echo "Testing on $(uname -s) $(uname -r)"
    
    # Test core functionality
    byobu-export >/dev/null 2>&1 || return 1
    
    # Test status modules
    for module in usr/lib/byobu/*; do
        [ -x "$module" ] && "$module" >/dev/null 2>&1
    done
    
    echo "Platform test passed"
}
```

## Contributing

### Contribution Workflow

1. **Fork and Clone:**
   ```bash
   git clone https://github.com/yourusername/byobu.git
   cd byobu
   git remote add upstream https://github.com/RyoNakagami/byobu.git
   ```

2. **Create Feature Branch:**
   ```bash
   git checkout -b feature/my-new-feature
   ```

3. **Make Changes:**
   - Follow coding standards
   - Add tests for new features
   - Update documentation

4. **Test Changes:**
   ```bash
   # Syntax check
   find . -name "*.sh" -o -name "*.in" | xargs -I {} sh -n {}
   
   # Build test
   ./autogen.sh && ./configure && make
   
   # Functional test
   make install && byobu-export
   ```

5. **Commit and Push:**
   ```bash
   git add .
   git commit -m "Add new feature: description"
   git push origin feature/my-new-feature
   ```

6. **Create Pull Request:**
   - Describe changes clearly
   - Reference any related issues
   - Include test results

### Code Review Guidelines

#### For Contributors
- Write clear commit messages
- Keep changes focused and atomic
- Include tests and documentation
- Follow existing code style

#### For Reviewers
- Check code style and standards
- Verify functionality works
- Test on multiple platforms
- Review security implications

### Documentation Updates

When contributing, update relevant documentation:
- README.md for user-facing changes
- WIKI.md for feature additions
- STATUS_MODULES.md for new modules
- DEVELOPMENT.md for development changes

## Debugging

### Debug Mode

Enable debug output:
```bash
export BYOBU_DEBUG=1
byobu
```

### Logging

Add logging to modules:
```bash
__module_name() {
    local debug_file="$BYOBU_RUN_DIR/debug.log"
    
    if [ -n "$BYOBU_DEBUG" ]; then
        echo "$(date): module_name called" >> "$debug_file"
    fi
    
    # Module logic here
}
```

### Common Debugging Techniques

#### Shell Script Debugging
```bash
# Enable shell debugging
set -x

# Trace function calls
PS4='+ ${FUNCNAME[0]}:${LINENO}: '

# Check variable values
echo "DEBUG: variable=$variable" >&2
```

#### Status Module Debugging
```bash
# Test module directly
./usr/lib/byobu/battery

# Test with debug output
BYOBU_DEBUG=1 ./usr/lib/byobu/battery

# Check module dependencies
ldd ./usr/lib/byobu/battery 2>/dev/null || echo "Shell script"
```

### Performance Profiling

#### Timing Analysis
```bash
# Time module execution
time ./usr/lib/byobu/memory

# Profile status bar refresh
time byobu-status
```

#### Memory Usage
```bash
# Monitor memory usage
ps aux | grep byobu

# Check for memory leaks
valgrind --tool=memcheck byobu-status
```

## Cross-Platform Development

### Platform Differences

#### Command Availability
```bash
# Check command existence
if eval $BYOBU_TEST command >/dev/null 2>&1; then
    # Use command
else
    # Fallback implementation
fi
```

#### File System Differences
```bash
# Linux
if [ -r "/proc/meminfo" ]; then
    # Linux implementation
# macOS
elif eval $BYOBU_TEST sysctl >/dev/null 2>&1; then
    # macOS implementation
# FreeBSD
elif [ -r "/var/run/dmesg.boot" ]; then
    # FreeBSD implementation
else
    # Generic fallback
fi
```

### Testing Matrix

Test on multiple platforms:
- Ubuntu/Debian Linux
- CentOS/RHEL Linux
- macOS
- FreeBSD
- Alpine Linux (busybox)

### Compatibility Guidelines

1. **Use POSIX shell features only**
2. **Test command availability before use**
3. **Provide fallbacks for platform-specific features**
4. **Use portable command options**
5. **Handle different file system layouts**

## Release Process

### Version Management

Update version in `configure.ac`:
```bash
AC_INIT([byobu], [5.122], [http://byobu.org])
```

### Release Checklist

1. **Pre-release Testing:**
   - [ ] All tests pass
   - [ ] Cross-platform testing complete
   - [ ] Documentation updated
   - [ ] Changelog updated

2. **Release Preparation:**
   ```bash
   # Update version
   vim configure.ac
   
   # Generate release
   ./autogen.sh
   ./configure
   make dist
   ```

3. **Release Creation:**
   - Tag release in git
   - Create GitHub release
   - Upload distribution tarball
   - Update documentation

4. **Post-release:**
   - Announce release
   - Update package repositories
   - Monitor for issues

### Packaging

#### Debian Packaging
Files in `debian/` directory:
- `control` - Package dependencies
- `rules` - Build rules
- `changelog` - Version history
- `copyright` - License information

#### RPM Packaging
Create `.spec` file for RPM distributions.

---

*This development guide covers the technical aspects of contributing to Byobu. For user documentation, see [WIKI.md](WIKI.md).*
