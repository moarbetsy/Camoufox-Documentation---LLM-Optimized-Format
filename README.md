Generated: 2025-11-03T02:54:30.038Z

# Camoufox Documentation - LLM Optimized Format

## Overview & Introduction

An open-source anti-detect browser with robust fingerprint injection built on Firefox.

## Key Features
- Invisible to anti-bot systems (71.5% CreepJS score)
- Fingerprint injection without JS injection at C++ level
- Human-like mouse movement
- Comprehensive anti-graphical fingerprinting
- Memory efficient (200MB usage)
- Always updated with latest Firefox

## Design Philosophy
- **Crowdblending**: Mimics real-world traffic distribution
- **Stealth**: No JavaScript injection, operates at C++ level
- **Undetectable**: All Playwright Page Agent JS is sandboxed

## Why Firefox over Chromium?
1. Chrome closed-source features make patching difficult
2. CDP more widely known = more common bot detection target
3. Juggler operates at lower level than CDP
4. More fingerprinting resistance research available

---

### Feature List
ID: features

# Camoufox Features

## Fingerprint Spoofing
- Navigator properties (device, browser, locale)
- Screen size, resolution, viewport
- WebGL parameters, extensions, context attributes
- Canvas fingerprint via anti-aliasing manipulation
- AudioContext (sample rate, latency, channels)
- Device voices & playback rates
- Media devices (microphones, webcams, speakers)
- Network headers (Accept-Languages, User-Agent)
- WebRTC IP at protocol level
- Geolocation, timezone, locale
- Battery API

## Stealth Features
- Main world execution leak prevention
- Frame execution context protection
- navigator.webdriver detection fix
- Headless detection fixes
- Non-default screen/window sizes
- Re-enabled fission & PDF.js

## Human-like Behavior
- Natural cursor movement algorithm
- Anti-font fingerprinting with metrics randomization
- System-appropriate fonts (Windows, Mac, Linux)

## Quality of Life
- Playwright support with custom implementation
- uBlock Origin with privacy filters
- No CSS animations for speed
- Debloated (200MB usage vs standard Firefox)
- PyPI package for auto fingerprint injection
- Remote server hosting capability

---


## Installation & Setup

### Python Installation
ID: install_python

# Installation

## Install Package
```bash
pip install -U camoufox[geoip]
```

The `geoip` parameter downloads dataset for:
- Longitude, latitude determination
- Timezone calculation
- Country & locale matching
- Based on proxy IP address

## Download Browser
```bash
camoufox fetch
```

## Uninstall
```bash
camoufox remove
```

## CLI Commands
- `camoufox fetch` - Download latest version
- `camoufox path` - Show executable path
- `camoufox remove` - Remove all files
- `camoufox server` - Launch Playwright server
- `camoufox test` - Open inspector
- `camoufox version` - Show version

---


## Usage & API

### Basic Usage
ID: basic_usage

# Basic Usage

## Sync API
```python
from camoufox.sync_api import Camoufox

with Camoufox() as browser:
    page = browser.new_page()
    page.goto("https://example.com")
```

## Async API
```python
from camoufox.async_api import Camoufox

async with Camoufox() as browser:
    page = await browser.new_page()
    await page.goto("https://example.com")
```

## With GeoIP & Proxy
```python
with Camoufox(
    geoip=True,
    proxy={
        'server': 'http://example.com:8080',
        'username': 'username',
        'password': 'password'
    }
) as browser:
    page = browser.new_page()
    page.goto("https://www.browserscan.net")
```

---

### Key Parameters
ID: parameters

# Configuration Parameters

## Device Rotation
- **os**: str | list - Operating system ("windows", "macos", "linux")
- **fonts**: list[str] - Custom fonts to load
- **screen**: Screen - Screen dimension constraints
- **webgl_config**: tuple - (vendor, renderer) pair

## Features
- **humanize**: bool | float - Human-like cursor (True or max seconds)
- **headless**: bool | "virtual" - Headless mode (use "virtual" for Xvfb)
- **addons**: list[str] - Paths to extracted Firefox addons
- **window**: tuple - (width, height) in pixels
- **main_world_eval**: bool - Enable DOM modification (detectable!)

## Location & Language
- **geoip**: str | bool - IP address or True for auto-detection
- **locale**: str | list - Locale(s) like "en-US" or ["en-US", "fr-FR"]

## Toggles
- **block_images**: bool - Block image requests
- **block_webrtc**: bool - Disable WebRTC
- **block_webgl**: bool - Disable WebGL
- **disable_coop**: bool - Disable Cross-Origin-Opener-Policy
- **enable_cache**: bool - Enable page/request caching

## Advanced
- **config**: dict - Override individual fingerprint properties
- **persistent_context**: bool - Create persistent context
- **exclude_addons**: list[DefaultAddons] - Exclude default addons

---


## Fingerprint Configuration

### Navigator Properties
ID: navigator

# Navigator Spoofing

Properties are spoofed at C++ level, undetectable via JavaScript.

## Available Properties
- navigator.userAgent
- navigator.doNotTrack
- navigator.appCodeName / appName / appVersion
- navigator.oscpu
- navigator.language / languages
- navigator.platform
- navigator.hardwareConcurrency
- navigator.product / productSub
- navigator.maxTouchPoints
- navigator.cookieEnabled
- navigator.globalPrivacyControl
- navigator.buildID
- navigator.onLine

## Usage
```python
with Camoufox(config={
    "navigator.userAgent": "Mozilla/5.0...",
    "navigator.platform": "Win32",
    "navigator.hardwareConcurrency": 8
}) as browser:
    ...
```

## Notes
- navigator.webdriver always set to false
- navigator.language falls back to locale settings
- Changing Firefox version may flag some test sites

---

### Canvas Fingerprinting
ID: canvas

# Canvas Anti-Fingerprinting

## How It Works
Camoufox uses **patched Skia** with modified subpixel rendering logic, not noise injection.

### Traditional Approach (Other Tools)
- Add random noise to canvas
- Easily detectable by drawing known shapes

### Camoufox Approach
- Modifies anti-aliasing at Skia level
- Changes appear as natural rendering differences
- Impossible to distinguish from different hardware

## Properties
- **canvas:aaOffset** (int) - Offset pixel transparency for anti-aliasing
- **canvas:aaCapOffset** (bool) - Clamp offset to 0-255 range (default: wrap)

## Status
⚠️ **Closed Source** - Available only in GitHub releases to prevent reverse engineering

## Testing
- Canvas integrity tests: camoufox.com/tests/canvas
- Shape drawing tests: camoufox.com/tests/shapetests

---

### WebGL Fingerprinting
ID: webgl

# WebGL Spoofing

## Properties
All properties must match real device combinations to avoid detection.

### Core Properties
- **webGl:renderer** - GPU renderer name
- **webGl:vendor** - GPU vendor name
- **webGl:supportedExtensions** - Array of WebGL extensions
- **webGl:contextAttributes** - Context attribute dictionary
- **webGl:parameters** - GL enum parameters (keys must be GL enums)
- **webGl:shaderPrecisionFormats** - Shader precision formats

### WebGL2 Equivalents
- webGl2:renderer, webGl2:vendor, webGl2:supportedExtensions
- webGl2:contextAttributes, webGl2:parameters
- webGl2:shaderPrecisionFormats

### Block Options
- **webGl:parameters:blockIfNotDefined** - Only allow defined parameters
- **webGl:shaderPrecisionFormats:blockIfNotDefined** - Only allow defined formats

## Usage
```python
with Camoufox(
    webgl_config=("Apple", "Apple M1, or similar"),
    os="macos"
) as browser:
    ...
```

## Warning
⚠️ Never randomly assign WebGL values! WAFs compare against known device datasets.

## Testing
- Demo site: camoufox.com/tests/webgl
- Research: camoufox.com/webgl-research

---

### Screen & Window
ID: screen_window

# Screen & Window Spoofing

## Screen Properties
- screen.availHeight / availWidth
- screen.availTop / availLeft
- screen.height / width
- screen.colorDepth / pixelDepth
- screen.pageXOffset / pageYOffset

## Window Properties
- window.scrollMinX / scrollMinY
- window.scrollMaxX / scrollMaxY
- window.outerHeight / outerWidth
- window.innerHeight / innerWidth
- window.screenX / screenY
- window.history.length
- window.devicePixelRatio

## Document Properties
- document.body.clientWidth / clientHeight
- document.body.clientTop / clientLeft

## Usage
```python
with Camoufox(config={
    "screen.width": 1920,
    "screen.height": 1080,
    "window.innerWidth": 1280,
    "window.innerHeight": 720
}) as browser:
    ...
```

## Notes
- Setting outer viewport may cause cosmetic defects
- Camoufox prevents manual window resizing when set
- Better to set constraints via screen parameter

---

### Geolocation & Locale
ID: geolocation

# Geolocation & Intl Spoofing

## Properties
- **geolocation:latitude** / **longitude** (double) - GPS coordinates
- **geolocation:accuracy** (double) - Auto-calculated from decimal precision
- **timezone** (str) - TZ identifier (e.g., "America/Chicago")
- **locale:language** (str) - Language code (e.g., "en")
- **locale:region** (str) - Region code (e.g., "US")
- **locale:script** (str) - Script (e.g., "Latn") - auto-set if omitted
- **locale:all** (str) - Comma-separated locale list

## GeoIP Integration
```python
# Auto-detect from proxy IP
with Camoufox(
    geoip=True,
    proxy={'server': 'http://proxy.com:8080'}
) as browser:
    ...

# Use specific IP
with Camoufox(geoip="203.0.113.0") as browser:
    ...
```

## Features
- Auto-accepts location permission prompts
- Calculates browser language from speaker distribution
- Matches timezone to coordinates
- Spoofs Intl API responses

## Validation
- timezone must be valid TZ identifier
- locale must be valid ISO codes

---

### WebRTC & Headers
ID: webrtc_headers

# WebRTC & HTTP Headers

## WebRTC IP Spoofing
Spoofs at **protocol level** by modifying ICE candidates and SDP.

### Properties
- **webrtc:ipv4** (str) - IPv4 address
- **webrtc:ipv6** (str) - IPv6 address

### Usage
```python
with Camoufox(config={
    'webrtc:ipv4': '123.45.67.89',
    'webrtc:ipv6': 'e791:d37a:88f6:48d1:2cad:2667:4582:1d6d'
}) as browser:
    ...
```

### Disable WebRTC
Set preference: `media.peerconnection.enabled = false`

## HTTP Headers
- **headers.User-Agent** - Browser/system info (falls back to navigator.userAgent)
- **headers.Accept-Language** - Acceptable languages
- **headers.Accept-Encoding** - Acceptable encodings

## Testing
- Browserleaks WebRTC test
- CreepJS WebRTC test
- BrowserScan WebRTC test

---

### Fonts & Media
ID: fonts_media

# Fonts & Media Devices

## Font Spoofing
### Built-in Fonts
- Windows 11 22H2 fonts
- macOS Sonoma fonts
- Linux TOR bundle fonts

### Auto-Selection
Camoufox automatically loads fonts matching the User-Agent OS.

### Custom Fonts
```python
custom_fonts = ["Arial", "Helvetica", "Times New Roman"]
with Camoufox(fonts=custom_fonts) as browser:
    ...
```

### Anti-Fingerprinting
- Randomly offsets letter spacing by 0-0.1px
- Prevents font metrics fingerprinting
- Properties: fonts, fonts:spacing_seed

## Media Devices
### Properties
- **mediaDevices:enabled** (bool) - Enable spoofing
- **mediaDevices:micros** (uint) - Microphone count (default: 3)
- **mediaDevices:webcams** (uint) - Webcam count (default: 1)
- **mediaDevices:speakers** (uint) - Speaker count (default: 1)

## AudioContext
- **AudioContext:sampleRate** (uint)
- **AudioContext:outputLatency** (double)
- **AudioContext:maxChannelCount** (uint)

## Voices
- **voices** (array) - Voice objects with keys: isLocalService, isDefault, voiceUri, name, lang
- **voices:blockIfNotDefined** (bool)
- **voices:fakeCompletion** (bool)
- **voices:fakeCompletion:charsPerSecond** (double)

---


## Development & Building

### Build System Overview
ID: build_system

# Build System

## Prerequisites
⚠️ **Linux only** - WSL will not work!

## Build Process
1. **Clone repository**
```bash
git clone --depth 1 https://github.com/daijro/camoufox
cd camoufox
```

2. **Setup directory**
```bash
make dir
```

3. **Bootstrap** (one-time)
```bash
make bootstrap
```

4. **Build & package**
```bash
python3 multibuild.py --target linux windows macos --arch x86_64 arm64 i686
```

## Build Targets
- **Platforms**: linux, windows, macos
- **Architectures**: x86_64, arm64, i686

## CLI Options
- `--bootstrap` - Bootstrap build system
- `--clean` - Clean build directory
- `--target` - Target platforms
- `--arch` - Target architectures

## Components
- Firefox source code
- Fingerprint masking patches
- uBlock Origin & Better Private Browsing
- Debloat/optimizations
- Platform-specific fonts
- Patched Juggler (Playwright integration)

---

### Building with Docker
ID: docker_build

# Docker Build

Build on non-Linux systems using Docker.

## Create Image
```bash
docker build -t camoufox-builder .
```

## Build for Target
```bash
docker run -v "$(pwd)/dist:/app/dist" \
  camoufox-builder \
  --target <os> --arch <arch>
```

## Use Local .mozbuild
```bash
docker run \
  -v "$HOME/.mozbuild":/root/.mozbuild:rw,z \
  -v "$(pwd)/dist:/app/dist" \
  camoufox-builder \
  --target <os> --arch <arch>
```

## Output
Build artifacts appear in `dist/` folder.

## Example
```bash
docker run -v "$(pwd)/dist:/app/dist" \
  camoufox-builder \
  --target windows macos linux \
  --arch x86_64 arm64 i686
```

---

### Development Tools
ID: dev_tools

# Development Tools

## Developer UI
```bash
make edits
```

Opens UI for managing patches:
- Edit existing patches
- Create new patches
- Remove patches
- Reset workspace

## Creating Patches
1. Click "Reset workspace" in developer UI
2. Make changes in `camoufox-*/` folder
3. Test with `make build` and `make run`
4. Click "Write workspace to patch"

## Editing Patches
1. Click "Edit a patch"
2. Select patch to edit
3. Workspace resets to patch state
4. Make changes
5. "Write workspace to patch" to overwrite

## Useful Commands
- `make build` - Build source code
- `make run` - Run built browser
- `make ff-dbg` - Setup vanilla Firefox
- `make edit-cfg` - Edit camoufox.cfg

---

### Leak Debugging Workflow
ID: debugging_workflow

# Debugging Detection Issues

## Flowchart Process

### 1. Baseline Test
**Q: Does website flag in official Firefox?**
- YES → Likely bad IP/rate-limiting
- NO → Continue to step 2

### 2. Clean Headless Build
Build clean Firefox: `make ff-dbg`, `make build`
**Q: Does website flag in headless mode?**
- YES → Investigate fingerprinting (Path 1)
- NO → Investigate patches (Path 2)

## Path 1: Fingerprinting Investigation
**Q: Flags in headful AND headless?**
- YES → Bad IP/rate-limiting
- NO → Enable `privacy.resistFingerprinting`
  - Fixed? → Fine-tune FPP settings
  - Still flags? → Deobfuscate website JS

## Path 2: Patch Investigation
1. Apply config.patch, rebuild
   - Still flags? → Test Playwright patch
   - Fixed? → Omit camoufox.cfg options

2. Apply Playwright bootstrap patch
   - Still flags? → Apply Camoufox patches one by one
   - Fixed? → Debug Juggler

## Last Resort
Deobfuscate website JavaScript to identify exact detection method.

## Key Commands
- `make ff-dbg` - Vanilla Firefox setup
- `make build` - Build source
- `make run` - Run browser
- `make run args="--headless https://test.com"` - Headless test
- `make edits` - Developer UI
- `make edit-cfg` - Edit config

---


## Testing & Performance

### Stealth Test Results
ID: stealth_performance

# Stealth Performance

All Playwright Page Agent JS is sandboxed and isolated.

## Test Results
✅ **CreepJS**: 71.5% - Spoofs all OS predictions
✅ **Rebrowser Bot Detector**: All tests pass
✅ **BrowserScan**: 100% - Full geolocation/locale spoofing
✅ **reCaptcha Score**: 0.9 across multiple test sites
✅ **DataDome**: All test sites pass including bot bounty
✅ **Imperva**: ticketmaster.es passes
✅ **Cloudflare**: Turnstile & Interstitial pass
✅ **WebRTC IP**: Correct spoofing on all test sites
✅ **Font Fingerprinting**: Rotates all metrics
✅ **Incolumitas**: 0.8-1.0 score
✅ **SannySoft**: Pass
✅ **Fingerprint.com**: Pass
✅ **IpHey**: Pass
✅ **Bet365**: Pass

## Testing Sites
- nopecha.com
- recaptcha-demo.appspot.com
- berstend.github.io
- hermes.com
- browserleaks.com
- browserscan.net

## Limitations
⚠️ **No Chromium fingerprint injection** - Some WAFs test for Spidermonkey engine behavior

---
