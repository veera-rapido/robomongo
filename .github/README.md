# GitHub Actions Workflows

This directory contains GitHub Actions workflows for automated building, testing, and releasing of Robo 3T.

## Workflows

### 1. CI Workflow (`ci.yml`)

**Triggers:**
- Push to `master`, `main`, or `develop` branches
- Pull requests to `master`, `main`, or `develop` branches

**Jobs:**
- **lint-and-test**: Basic code quality checks
  - Checks for trailing whitespace and tabs
  - Validates JSON configuration files
  - Verifies documentation files exist
- **build-check**: Basic build system validation
  - Checks CMake configuration
- **security-scan**: Security and code quality scanning
  - Uses GitHub Super Linter for multiple languages

### 2. macOS Build Workflow (`build-macos.yml`)

**Triggers:**
- Push to `master`, `main`, or `develop` branches
- Pull requests to `master`, `main`, or `develop` branches
- Manual workflow dispatch with build type selection

**Features:**
- Builds on macOS 12 (Monterey)
- Supports both release and debug builds
- Caches dependencies (Qt, OpenSSL, Robo Shell) for faster builds
- Creates DMG packages
- Tests the new `--config-file` feature
- Uploads build artifacts

**Dependencies:**
- Qt 5.12.8
- OpenSSL 1.1.1f
- MongoDB Robo Shell v4.2
- CMake, Python 3.9, Scons

### 3. Release Workflow (`release.yml`)

**Triggers:**
- GitHub release published
- Manual workflow dispatch with tag input

**Features:**
- Builds for both macOS and Linux
- Creates release packages (DMG for macOS, tar.gz for Linux)
- Automatically uploads packages to GitHub releases
- Matrix build strategy for multiple platforms

## Usage

### Running Workflows Manually

#### macOS Build
1. Go to the "Actions" tab in the GitHub repository
2. Select "Build macOS Binary"
3. Click "Run workflow"
4. Choose build type (release or debug)
5. Click "Run workflow"

#### Release
1. Go to the "Actions" tab in the GitHub repository
2. Select "Release"
3. Click "Run workflow"
4. Enter the release tag (e.g., "v1.4.4")
5. Click "Run workflow"

### Automatic Triggers

- **CI**: Runs automatically on every push and pull request
- **macOS Build**: Runs automatically on pushes to main branches
- **Release**: Runs automatically when a GitHub release is published

## Artifacts

### Build Artifacts
- **robo3t-macos-release**: Complete macOS release build
- **robo3t-macos-debug**: Complete macOS debug build
- **robo3t-macos-release-dmg**: DMG package for distribution

### Release Assets
- **macOS**: `.dmg` installer package
- **Linux**: `.tar.gz` archive

## Caching Strategy

To improve build times, the workflows cache:
- **Qt SDK**: Cached by version and platform
- **OpenSSL**: Cached by version and platform
- **Robo Shell**: Cached by version and platform

Caches are automatically invalidated when dependencies change.

## Build Requirements

### macOS
- macOS 12 (Monterey) runner
- Xcode command line tools
- Qt 5.12.8 (clang_64)
- OpenSSL 1.1.1f
- Python 3.8 with Scons (for compatibility with legacy dependencies)

### Linux
- Ubuntu 20.04 runner
- GCC build tools
- Qt 5.12.8 (gcc_64)
- OpenSSL 1.0.2o
- Python 3.8 with Scons (for compatibility with legacy dependencies)

## Troubleshooting

### Common Issues

1. **Cache Issues**: If builds fail due to corrupted cache, manually clear the cache in the Actions tab
2. **Dependency Failures**: Check if external download URLs are still valid
3. **Qt Installation**: The Qt installation step may occasionally fail; retry the workflow
4. **OpenSSL Build**: OpenSSL compilation can be sensitive to system changes
5. **Python Dependency Issues**: The workflows include automatic fallbacks for problematic Python packages like `zope.interface==4.6.0`

### Dependency Compatibility

The workflows include several mechanisms to handle dependency compatibility issues:

- **Python Version**: Uses Python 3.8 for better compatibility with older packages
- **Setuptools Version**: Pins to `setuptools<60` to maintain compatibility with legacy packages
- **Automatic Patching**: `scripts/fix-roboshell-deps.py` automatically updates problematic version pins
- **Fallback Requirements**: `scripts/roboshell-requirements.txt` provides compatible alternatives
- **Graceful Degradation**: If original requirements fail, workflows fall back to essential packages only

### Debugging

- Check the workflow logs in the Actions tab
- Look for specific error messages in failed steps
- Verify that all required secrets and environment variables are set

## Security

- No secrets are required for basic building
- `GITHUB_TOKEN` is automatically provided for release uploads
- All dependencies are downloaded from official sources
- Build artifacts are scanned for security issues

## Contributing

When modifying workflows:
1. Test changes in a fork first
2. Use workflow dispatch for testing
3. Update this documentation
4. Consider backward compatibility
5. Update cache keys if dependencies change
