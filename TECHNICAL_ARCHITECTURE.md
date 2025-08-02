# Sine Technical Architecture Summary

## Core System Overview

**Sine** is a userscript-based mod manager that integrates deeply with Firefox-based browsers, particularly Zen. It operates through fx-autoconfig to inject functionality directly into the browser's settings interface.

## Key Technical Components

### 1. Entry Point: sine.uc.mjs
- **Type**: UserScript compatible with fx-autoconfig
- **Function**: Bootstrap loader for entire Sine ecosystem
- **Responsibilities**:
  - Module loading and dependency management
  - CSS/JS mod compilation and injection
  - Browser window lifecycle management
  - Update system coordination

### 2. Engine Architecture (engine/ directory)
```
engine/
├── XULManager.js       → DOM manipulation utilities
├── injectAPI.js        → Web integration actors
├── cmdPalette.js       → Developer tools
├── marked.js           → Markdown rendering
├── prefs.js            → Configuration management
├── css/                → UI styling modules
└── SineActors/         → Browser-web communication
```

### 3. Build System
- **Python Scripts**: Automated packaging, import, and distribution
- **Makefile**: Developer-friendly command shortcuts
- **C# Installers**: Cross-platform installation automation
- **Engine Packaging**: Automatic zip creation for updates

## Architecture Patterns

### Module System
- **ES6 Imports**: Modern JavaScript module syntax
- **Lazy Loading**: Modules loaded as needed
- **Dependency Injection**: Clean separation of concerns

### State Management
- **Firefox Preferences**: Persistent configuration storage
- **JSON Files**: Mod registry and metadata
- **File System**: CSS/JS asset management

### Update Mechanism
```javascript
// Automatic version detection
if (new Date(remoteVersion.updatedAt) > new Date(localVersion.updatedAt)) {
    await downloadAndInstall(remoteVersion);
}
```

## Integration Points

### Browser Integration
- **XUL Injection**: Direct DOM manipulation in Firefox UI
- **CSS Loading**: Dynamic stylesheet management
- **Preference System**: Native Firefox configuration APIs

### Web Integration  
- **JSWindowActors**: Secure browser-web communication
- **Marketplace API**: Remote mod discovery and installation
- **GitHub Integration**: Direct repository monitoring

### File System Integration
- **Profile Management**: Automatic browser profile detection
- **Asset Compilation**: CSS import resolution and bundling
- **Security Sandboxing**: Safe mod execution environment

## Security Model

### Safe Defaults
- External sources disabled by default
- JavaScript execution requires explicit consent
- Sandboxed mod execution environment

### Permission System
- Granular control over mod types
- User confirmation for sensitive operations
- Automatic security updates

## Performance Considerations

### Efficient Loading
- Lazy module initialization
- Concurrent network requests
- Minimal startup overhead

### Resource Management
- CSS compilation caching
- Asset deduplication
- Memory-efficient DOM manipulation

## Cross-Platform Support

### Browser Detection
```javascript
// Automatic browser identification
const browserType = detectBrowser(); // zen, firefox, floorp, etc.
const os = detectOS();               // win, mac, linux
```

### Platform Adaptation
- OS-specific file path handling
- Browser-specific API usage
- Platform-aware UI elements

This architecture enables Sine to provide a seamless, powerful modding experience while maintaining security, performance, and compatibility across the Firefox ecosystem.