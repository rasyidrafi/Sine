# Sine Repository Complete Explanation

## Overview

**Sine** is a community-driven mod/theme manager specifically designed for Firefox-based browsers, with special emphasis on the **Zen Browser**. It serves as a powerful, user-friendly alternative to manual installation of browser modifications and themes.

## What is Sine?

Sine is a third-party plugin that transforms how users manage browser customizations. Instead of manually installing themes and mods, Sine provides:

- **Built-in Marketplace**: Browse and install mods directly from the browser settings
- **Automatic Updates**: Keep mods up-to-date without manual intervention  
- **Easy Management**: Enable/disable mods with a simple toggle
- **Developer-Friendly**: Easy publishing system for mod creators
- **Cross-Browser Support**: Works with multiple Firefox-based browsers

## Repository Structure

```
Sine/
├── sine.uc.mjs              # Main entry point userscript
├── engine/                  # Core functionality modules
│   ├── XULManager.js       # DOM manipulation utilities
│   ├── injectAPI.js        # API injection for web integration
│   ├── cmdPalette.js       # Developer command palette
│   ├── marked.js           # Markdown parsing for READMEs
│   ├── prefs.js            # Preference management
│   ├── css/                # Styling modules
│   └── SineActors/         # Browser actor modules
├── data/                   # Configuration files
│   └── engine.json         # Version and update metadata
├── deployment/             # Built packages and installers
│   ├── auto-installers/   # Platform-specific installers
│   └── engine.zip         # Packaged engine for distribution
├── scripts/                # Build and deployment automation
│   ├── package_engine.py  # Package engine into zip
│   ├── import_engine.py   # Import to browser profile
│   ├── update.py          # Update version metadata
│   └── distribute.py      # Distribution helper
├── makefile               # Build automation commands
└── README.md             # User documentation
```

## Technical Architecture

### Core Components

#### 1. **sine.uc.mjs** - Main Entry Point
- **File Type**: UserScript for fx-autoconfig
- **Purpose**: Primary loader that bootstraps the entire Sine system
- **Key Features**:
  - Imports and initializes all engine modules
  - Manages CSS/JS mod loading and injection
  - Handles browser window lifecycle
  - Provides mod installation and update functionality

#### 2. **Engine Modules**

**XULManager.js**
- Simplifies DOM manipulation in Firefox XUL environment
- Provides utility functions for creating UI elements
- Used throughout Sine for interface generation

**injectAPI.js**
- Registers browser actors for web integration
- Enables communication between Sine and web pages
- Specifically targets Zen browser marketplace integration

**cmdPalette.js**
- Developer command palette (Ctrl+Shift+Y)
- Provides debugging and development tools
- Optional feature for advanced users

**prefs.js**
- Sets up default preferences for Sine
- Manages configuration persistence
- Handles browser-specific settings

### Mod Management System

#### CSS Theme Handling
```javascript
// Sine automatically processes CSS imports and dependencies
const stylesheetData = await this.rebuildStylesheets();

// Applies styles to all browser windows including PiP
windowUtils.loadSheet(cssURI, windowUtils.USER_SHEET);

// Handles @import resolution and relative paths
await this.processCSS(currentPath, cssContent, originalURL, themeFolder);
```

#### JavaScript Mod Support
- Secure JS mod execution with explicit user consent
- Automatic file management in browser profile JS directory
- Enable/disable functionality with proper cleanup
- Restart notification for JS changes (security requirement)

#### Preference System
```javascript
// Dynamic preference UI generation from JSON
const prefEl = this.parsePrefs(pref);

// Conditional visibility based on other settings
if (pref.hasOwnProperty("conditions")) this.injectDynamicCSS(pref);

// Real-time updates with restart notifications when needed
UC_API.Prefs.set(pref.property, value);
```

#### Actor System for Web Integration
- **SineModsMarketplaceParent.sys.mjs**: Server-side actor for marketplace
- **SineModsMarketplaceChild.sys.mjs**: Client-side actor for web pages
- Enables direct installation from Zen browser website
- Secure communication channel between web and extension

## Installation Process

### Automatic Installation (Recommended)
1. Download platform-specific installer from releases
2. Run installer (handles fx-autoconfig and Sine setup)
3. Clear browser startup cache
4. Restart browser

### Manual Installation
1. Install fx-autoconfig in browser profile
2. Copy Sine files to appropriate directories
3. Configure userChrome.css imports

## Key Features Deep Dive

### 1. Built-in Marketplace Integration
- **Curated Store**: Quality-controlled mod repository
- **One-Click Installation**: Direct installation from settings page
- **Search & Filter**: Find mods by name, category, or compatibility
- **Mod Previews**: Screenshots and detailed descriptions
- **Rating System**: Community feedback and popularity metrics

### 2. Automatic Update System
```javascript
// Engine auto-update check
if (new Date(engine.updatedAt) > new Date(updatedAt)) {
    await this.updateEngine();
    this.showToast("Engine updated to v" + engine.version);
}

// Mod auto-update detection
if (new Date(currModData.updatedAt) < new Date(newThemeData.updatedAt)) {
    console.log("Auto-updating: " + currModData.name);
    await this.syncModData(currThemeData, newThemeData, currModData);
}
```

### 3. Developer-Friendly Publishing
- **Simple Metadata**: Just add `theme.json` to your repository
- **Automatic Detection**: Updates pulled directly from GitHub
- **No Manual Submissions**: Direct repository linking
- **Version Control**: Semantic versioning with automatic detection

### 4. Cross-Browser Compatibility
```javascript
// Browser detection system
get fork() {
    let fork = "firefox";
    if (UC_API.Prefs.get("zen.browser.is-cool").exists()) fork = "zen";
    else if (UC_API.Prefs.get("enable.floorp.update").exists()) fork = "floorp";
    else if (UC_API.Prefs.get("mullvadbrowser.migration.version").exists()) fork = "mullvad";
    // ... additional browser detection
    return fork;
}
```

### 5. Advanced Preference Management
- **Conditional UI**: Show/hide preferences based on other settings
- **Type Validation**: String, boolean, number, dropdown support
- **Live Updates**: Real-time style application without restart
- **OS-Specific Options**: Platform-aware preference handling

## Mod File Structure

### Standard Mod Layout
```
my-awesome-theme/
├── theme.json              # Mod metadata and configuration
├── chrome.css              # Main CSS file (or userChrome.css)
├── userContent.css         # Content-specific styles (optional)
├── preferences.json        # User-configurable options (optional)
├── README.md              # Documentation (optional)
├── screenshot.png         # Preview image (optional)
└── assets/                # Additional resources (optional)
    ├── images/
    └── fonts/
```

### theme.json Schema
```json
{
  "name": "My Awesome Theme",
  "id": "my-awesome-theme",
  "version": "1.0.0",
  "description": "A beautiful theme for Zen browser",
  "author": "Your Name",
  "homepage": "https://github.com/username/my-awesome-theme",
  "style": "chrome.css",
  "preferences": "preferences.json",
  "readme": "README.md",
  "image": "screenshot.png",
  "os": ["win", "mac", "linux"],
  "fork": ["zen", "firefox"]
}
```

### Preference Configuration
```json
[
  {
    "type": "checkbox",
    "property": "my-theme.enable-feature",
    "label": "Enable awesome feature",
    "defaultValue": true
  },
  {
    "type": "dropdown",
    "property": "my-theme.color-scheme",
    "label": "Color scheme",
    "options": [
      {"value": "dark", "label": "Dark"},
      {"value": "light", "label": "Light"}
    ]
  }
]
```

## File System Organization

### Browser Profile Structure
```
profile/
├── chrome/
│   ├── JS/
│   │   ├── sine.uc.mjs         # Main Sine entry point
│   │   └── engine/             # Sine engine modules
│   ├── sine-mods/              # Installed mods directory
│   │   ├── mods.json          # Mod registry
│   │   ├── chrome.css         # Compiled CSS imports
│   │   ├── content.css        # Compiled content CSS
│   │   └── [mod-id]/          # Individual mod folders
│   │       ├── chrome.css
│   │       ├── preferences.json
│   │       └── readme.md
│   └── userChrome.css          # Firefox CSS import
└── prefs.js                   # Firefox preferences
```

### Sine Internal Structure
```
chrome/JS/sine-mods/
├── mods.json                  # Master mod registry
├── chrome.css                 # Aggregated userChrome imports
├── content.css                # Aggregated userContent imports
└── [individual-mods]/
    ├── awesome-theme-123/
    │   ├── chrome.css
    │   ├── preferences.json
    │   └── readme.md
    └── another-mod-456/
        ├── userChrome.css
        └── assets/
```

### For End Users
1. **Browse Marketplace**: Access via browser settings → Sine Mods
2. **Install Mods**: One-click installation from curated store
3. **Manage Installed**: Toggle mods on/off, configure preferences
4. **Auto-Updates**: Mods update automatically in background

### For Mod Developers
1. **Create Mod**: Develop CSS/JS modifications
2. **Add Metadata**: Include theme.json with mod information
3. **Publish**: Submit to marketplace or share GitHub repo
4. **Updates**: Push updates to repository (auto-detected by Sine)

## Supported Browsers

Sine includes detection for multiple Firefox-based browsers:

- **Zen Browser** (primary target)
- **Firefox** (base support)
- **Floorp** 
- **Mullvad Browser**
- **Waterfox**
- **LibreWolf**

Each browser has specific optimizations and compatibility handling.

## Build System

### Development Commands
```bash
# Package engine for distribution
make run package

# Import to browser profile for testing  
make run import

# Update version metadata
make run update

# Build installers for all platforms
make run distribute
```

### Python Scripts

**package_engine.py**
- Creates `engine.zip` containing all engine modules
- Packages `sine.uc.mjs` and entire `engine/` folder
- Used for distribution and auto-updates

**import_engine.py**  
- Copies Sine files to browser profile for testing
- Reads `PROFILE_PATH` from `.env` file
- Installs to `PROFILE_PATH/chrome/JS/` directory
- Enables local development and testing

**update.py**
- Updates timestamps in `engine.json`
- Optionally updates version numbers
- Critical for auto-update system functionality

**distribute.py**
- Reorganizes installer files for distribution
- Renames platform-specific installers
- Handles cross-platform deployment automation

### Auto-Installers (.NET)

The `deployment/auto-installers/` directory contains C# projects that create platform-specific installers:

**Key Components:**
- **Program.cs**: Main installer logic
- **SineInstaller.csproj**: .NET project configuration  
- **compiler.sh/.ps1**: Cross-platform build scripts
- **app.manifest**: Windows UAC elevation requirements

**Installer Functionality:**
1. Downloads and installs fx-autoconfig
2. Copies Sine files to browser profile
3. Configures userChrome.css imports
4. Handles platform-specific file paths
5. Provides user-friendly installation experience

## Security Considerations

### Safe Defaults
- External marketplaces disabled by default
- JavaScript mods require explicit user consent
- Sandboxed execution environment

### User Controls
- Granular permissions for different mod types
- Ability to disable all mods instantly
- Import/export for backup and migration

## Marketplace Integration

### Mod Discovery
- Curated marketplace with quality control
- Search and filtering capabilities
- Mod screenshots and descriptions

### Installation Pipeline
```javascript
// Automatic dependency resolution
await this.processCSS(currentPath, cssContent, originalURL, themeFolder);

// Mod validation and installation
await this.syncModData(currThemeData, newThemeData);

// Style rebuilding and application
await this.manager.rebuildMods();
```

## Development Features

### Command Palette
- Developer tools accessible via Ctrl+Shift+Y
- Debugging utilities for mod development
- Real-time testing capabilities

### Hot Reloading
- Automatic style updates without restart
- JavaScript changes require restart (security)
- Live preference updates

## Version Management

### Engine Updates
- Automatic background updates
- Version tracking in engine.json
- Rollback capabilities

### Mod Updates
- GitHub integration for automatic detection
- Semantic versioning support
- Batch update functionality

## Future Roadmap

Based on the codebase structure, Sine appears to be actively developed with:
- Expansion to more Firefox-based browsers
- Enhanced developer tools
- Improved marketplace features
- Better mod discovery and rating systems

## Getting Started for Contributors

### Prerequisites
- **Git**: For repository management
- **Python 3.7+**: For build scripts
- **Make** (optional): For simplified commands
- **.NET SDK** (optional): For building installers
- **Firefox-based browser**: For testing

### Development Setup

1. **Fork and Clone Repository**
```bash
git clone https://github.com/yourusername/Sine.git
cd Sine
```

2. **Install Dependencies**
```bash
# Python dependencies for build scripts
pip install python-dotenv

# Make (platform-specific)
# Windows: choco install make
# macOS: brew install make  
# Linux: sudo apt install make
```

3. **Configure Testing Environment**
```bash
# Create .env file in repository root
echo "PROFILE_PATH=/path/to/your/browser/profile" > .env
```

4. **Test Local Installation**
```bash
# Package and import to browser profile
make run package
make run import

# Or using Python directly
python scripts/package_engine.py
python scripts/import_engine.py
```

### Development Workflow

#### Testing Changes
1. **Modify Engine Files**: Edit files in `engine/` directory
2. **Package Changes**: `make run package`
3. **Import to Browser**: `make run import`
4. **Test in Browser**: Restart browser and test functionality
5. **Iterate**: Repeat process for further changes

#### Adding New Features
1. **Engine Modules**: Add new .js files to `engine/` directory
2. **Import in Main**: Update `sine.uc.mjs` to import new modules
3. **Update Build**: Ensure new files are included in packaging
4. **Test Thoroughly**: Verify compatibility across browsers
5. **Document Changes**: Update README and documentation

#### Creating Installers
```bash
# Build all platform installers
cd deployment/auto-installers
./compiler.sh  # Linux/macOS
# or
./compiler.ps1  # Windows

# Distribute built installers
cd ../../
make run distribute
```

### Code Style Guidelines

#### JavaScript/Module Style
- **Indentation**: 4 spaces (not tabs)
- **Semicolons**: Required for statements
- **Comments**: Use `//` for single line, `/* */` for blocks
- **Function Names**: camelCase convention
- **Module Exports**: Use ES6 export/import syntax

#### File Organization
- **Engine modules**: Place in `engine/` with descriptive names
- **Utilities**: Group related functions in single modules
- **CSS**: Separate styling logic into `engine/css/`
- **Assets**: Store images/resources in `engine/assets/`

### Contribution Guidelines

#### Bug Reports
1. **Check Existing Issues**: Search for similar problems
2. **Provide Details**: Browser version, OS, steps to reproduce
3. **Include Logs**: Browser console errors if applicable
4. **Minimal Example**: Provide simplest case that demonstrates issue

#### Feature Requests  
1. **Describe Use Case**: Explain why feature is needed
2. **Propose Implementation**: Suggest how it might work
3. **Consider Scope**: Ensure it fits Sine's mission
4. **Discussion First**: Use GitHub Discussions for major features

#### Pull Requests
1. **Fork Repository**: Create your own copy for development
2. **Feature Branch**: Create branch for your specific changes
3. **Test Thoroughly**: Verify changes work across browsers
4. **Update Documentation**: Include relevant documentation updates
5. **Clear Commits**: Use descriptive commit messages
6. **PR Description**: Explain what changes do and why

### Testing Your Contributions

#### Manual Testing
- **Install Fresh**: Test on clean browser profile
- **Multiple Browsers**: Verify Zen, Firefox, other supported browsers
- **Different Platforms**: Test Windows, macOS, Linux if possible
- **Edge Cases**: Test with no mods, many mods, update scenarios

#### Debugging Tools
- **Browser Console**: Check for JavaScript errors
- **Command Palette**: Use Ctrl+Shift+Y for debugging features
- **Preference Inspection**: Verify settings are saved correctly
- **Network Tab**: Monitor marketplace and update requests

This repository represents a sophisticated ecosystem for browser customization that makes advanced theming accessible to both developers and end users, with Zen browser as the primary target while maintaining broad Firefox compatibility.