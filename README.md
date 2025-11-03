# Camoufox Documentation - LLM Optimized Format

Generated: 2025-11-03T02:54:30.038Z

---


## Overview & Introduction

### Introduction to Camoufox
ID: intro

# Camoufox Overview

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


## Legal & Disclaimer

### Liability Disclaimer
ID: disclaimer

# Legal Information

## Disclaimer
Camoufox does NOT provide liability or warranty.

No responsibility accepted for use of this software. Intended for **ethical and research purposes only**.

Users use at their own risk. Developers cannot be held liable for damages. Software must not be used for malicious purposes.

By using Camoufox, you agree to this disclaimer and acknowledge responsible use in compliance with all applicable laws.

## Prohibited Uses
Users agree NOT to:
- Violate legal rights of others
- Engage in illegal activity (CSAM, abuse, terrorism, violence)
- Use for unlawful, invasive, infringing, defamatory, fraudulent purposes
- Distribute viruses, malware, deceptive content
- Gain unauthorized access or disrupt services
- Generate spam or mass email
- Access other services violating their ToS

## Open Source Status
Almost all code is public on GitHub. Some features (Canvas fingerprinting) are closed source to prevent reverse engineering by anti-bot providers.

## Acceptable Use
Software is for:
- Security research
- Testing bot detection systems
- Privacy protection research
- Educational purposes
- Legitimate automation needs

---

Here is the GitHub repository data prepared for consumption by a Large Language Model, following the structured and cohesive approach outlined. The data has been extracted, cleaned, normalized, and organized into logical documents.

***

### **Document 1: Camoufox - Introduction and Core Concepts**

**URL:** `https://camoufox.com`

#### **Introduction**

Camoufox is an open-source anti-detect browser with robust fingerprint injection. It is designed to provide a powerful, undetectable anti-fingerprinting solution that blends in with regular user traffic.

**Important Notice:**
> Hello! In March 2025, I had a medical emergency and have been hospitalized since. I haven't been able to continue my work for some time. I was previously updating Camoufox daily, and plan to return to that schedule as soon as I'm able. My goal is to have Camoufox back to its usual performance and be fully caught up by the end of 2025. Thank you for your patience and understanding.

#### **Design and Implementation**

*   **Fingerprinting Protection:** Privacy-focused browsers often make users more identifiable by creating unique fingerprints. Camoufox solves this by operating at a lower level, modifying device information in the C++ implementation instead of injecting JavaScript. Built on Firefox and research from the Tor project, LibreWolf, and Arkenfox, Camoufox aims to avoid common user identification methods.
*   **Crowdblending:** Camoufox aims to blend in with real-world traffic. It uses BrowserForge to rotate device information (screen, OS, hardware) to mimic the statistical distribution of real-world device characteristics. It also uses a natural mouse movement algorithm.
*   **Stealth:** Camoufox avoids bot detection by sandboxing and isolating all of Playwright's internal Page Agent Javascript from the main world, making it impossible for a page to detect its presence.

#### **Why Firefox and not Chromium?**

1.  **Open Source:** Chrome is closed source, making it harder to patch than Firefox. Anti-bot providers can detect the use of Chromium instead of Chrome.
2.  **Detection Surface:** The Chrome DevTools Protocol (CDP) is more widely used and a more common target for bot detection. Juggler (used by Playwright with Firefox) operates at a lower level and is less prone to JS leaks.
3.  **Fingerprinting Research:** More research exists for fingerprinting resistance on Firefox compared to Chromium.

#### **Is Camoufox open source?**
Almost all of Camoufox's code is publicly available on GitHub. However, certain features like the Canvas fingerprint rotation patch are closed source to prevent reverse engineering by anti-bot providers.

***

### **Document 2: Features and Stealth Performance**

**URL:** `https://camoufox.com/features`, `https://camoufox.com/stealth`

#### **Features List**

*   **Fingerprint Spoofing:**
    *   Navigator properties (device, browser, locale, etc.).
    *   Screen size, resolution, etc.
    *   WebGL parameters, extensions, context attributes, and shader precision formats.
    *   CPU Canvas fingerprint via modified anti-aliasing logic (closed source).
    *   Inner and outer window viewport sizes.
    *   AudioContext sample rate, output latency, and max channel count.
    *   Device voices & playback rates.
    *   Number of microphones, webcams, and speakers.
    *   Network headers (Accept-Languages, User-Agent).
    *   WebRTC IP spoofing at the protocol level.
    *   Geolocation, timezone, and locale spoofing.
    *   Battery API spoofing.
*   **Stealth Patches:**
    *   Avoids main world execution leaks; all page agent javascript is sandboxed.
    *   Fixes `navigator.webdriver` detection.
    *   Fixes Firefox headless detection via pointer type.
    *   Uses non-default screen & window sizes.
*   **Human-like Cursor Movement:**
    *   Built-in algorithm for natural cursor trajectories.
*   **Anti Font Fingerprinting:**
    *   Automatically uses correct system fonts for the target User Agent.
    *   Bundled with Windows, Mac, and Linux system fonts.
    *   Prevents font metrics fingerprinting by randomly offsetting letter spacing.
*   **Debloat/Optimizations:**
    *   Stripped out many Mozilla services for faster performance and lower memory usage (~200mb).
    *   Includes patches from LibreWolf & Ghostery to remove telemetry.
    *   Removed all CSS animations for speed.
*   **Addons:**
    *   Load Firefox addons via the Python interface.
    *   Includes uBlock Origin with custom privacy filters by default.
*   **Python Interface:**
    *   Automatically generates & injects unique device characteristics based on real-world distribution.
    *   Avoids proxy detection by matching geolocation, timezone, & locale to the proxy's region.
    *   Remote server hosting support.
    *   Built-in virtual display buffer for headless servers.

#### **Stealth Performance**
Camoufox performs well against major Web Application Firewalls (WAFs).

| Test | Status | Notes |
| :--- | :--- | :--- |
| CreepJS | ✔️ | 71.5%. Successfully spoofs all OS predictions. |
| Rebrowser Bot Detector | ✔️ | All tests pass. |
| BrowserScan | ✔️ | 100%. Spoofs all geolocation & locale proxy detection. |
| reCaptcha Score | ✔️ | 0.9 on demo sites. |
| DataDome | ✔️ | All test sites pass. |
| Imperva | ✔️ | Passes. |
| Cloudflare | ✔️ | Passes Turnstile and Interstitial checks. |
| WebRTC IP Spoofing | ✔️ | Spoofs Host & STUN IP correctly on Browserleaks, CreepJS, etc. |
| Font Fingerprinting | ✔️ | Rotates all metrics on Browserleaks and CreepJS. |
| Incolumitas | ✔️ | 0.8-1.0 score. |
| SannySoft | ✔️ | Passes. |
| Fingerprint.com | ✔️ | Passes. |

***

### **Document 3: Python Interface and Usage**

**URL:** `https://camoufox.com/python`, `https://camoufox.com/python/installation`, `https://camoufox.com/python/usage`

#### **Installation**
1.  **Install the package:** The `geoip` extra is recommended for use with proxies.
    ```bash
    pip install -U "camoufox[geoip]"
    ```
2.  **Download the browser:**
    ```bash
    camoufox fetch
    ```
3.  **CLI Commands:**
    *   `camoufox fetch`: Fetch the latest version of Camoufox.
    *   `camoufox path`: Display the path to the Camoufox executable.
    *   `camoufox remove`: Remove all downloaded files.
    *   `camoufox server`: Launch a Playwright server.
    *   `camoufox test`: Open the Playwright inspector.
    *   `camoufox version`: Display the current version.

#### **Basic Usage**
Camoufox is a drop-in replacement for Playwright's browser initialization.

**Sync API**
```python
from camoufox.sync_api import Camoufox

with Camoufox() as browser:
    page = browser.new_page()
    page.goto("https://example.com")
```
**Async API** is also available.

#### **Parameters List**
All Playwright Firefox launch options are accepted, along with Camoufox-specific options:

*   **Device Rotation**
    *   `os: Optional[ListOrString]`: "windows", "macos", "linux", or a list to choose from.
    *   `screen: Optional[Screen]`: Constrain screen dimensions using a `browserforge.fingerprints.Screen` instance.
    *   `webgl_config: Optional[Tuple[str, str]]`: Use a specific WebGL vendor/renderer pair, e.g., `("Apple", "Apple M1, or similar")`.
*   **Configuration**
    *   `humanize: Optional[Union[bool, float]]`: Enable human-like cursor movement. Set to `True` or a max duration in seconds.
    *   `headless: Optional[Union[bool, Literal['virtual']]]`: Run headless. Use `'virtual'` on Linux for an Xvfb virtual display.
    *   `addons: Optional[List[str]]`: List of paths to extracted Firefox addons.
    *   `window: Optional[Tuple[int, int]]`: Set a fixed window size `(width, height)`. (Not recommended for production).
    *   `main_world_eval: Optional[bool]`: Enable script injection into the main world.
*   **Location & Language**
    *   `geoip: Optional[Union[str, bool]]`: Set to `True` to auto-detect IP or provide a specific IP to match geolocation, timezone, and locale.
    *   `locale: Optional[Union[str, List[str]]]`: Set a specific locale, e.g., `"en-US"`.
*   **Toggles**
    *   `block_images: Optional[bool]`: Block image requests.
    *   `block_webrtc: Optional[bool]`: Block WebRTC.
    *   `block_webgl: Optional[bool]`: Block WebGL.
    *   `disable_coop: Optional[bool]`: Disable Cross-Origin-Opener-Policy, useful for clicking elements in cross-origin iframes like Cloudflare Turnstile.
*   **Advanced**
    *   `config: Optional[Dict[str, Any]]`: Manually override individual Camoufox config properties.

***

### **Document 4: Advanced Python Features**

**URL:** `https://camoufox.com/python/geoip`, `https://camoufox.com/python/browserforge`, `https://camoufox.com/python/remote-server`, `https://camoufox.com/python/virtual-display`, `https://camoufox.com/python/main-world-eval`

#### **GeoIP & Proxy Support**
By passing `geoip=True` or a target IP address, Camoufox automatically spoofs longitude, latitude, timezone, country, locale, and the WebRTC IP address to match the IP's location. It also spoofs the browser's language based on regional language distribution.

**Usage:**
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

#### **BrowserForge Integration**
Camoufox uses BrowserForge to generate fingerprints. By default, it generates a random fingerprint matching the specified `os` and `screen` constraints.
```python
from camoufox.sync_api import Camoufox
from browserforge.fingerprints import Screen

with Camoufox(
    os=('windows', 'macos', 'linux'),
    screen=Screen(max_width=1920, max_height=1080),
) as browser:
    page = browser.new_page()
```

#### **Remote Server**
Camoufox can run as a remote WebSocket server, allowing connection from other languages that support Playwright.

**Launch Server:**
```bash
python -m camoufox server
```
Or configure programmatically:
```python
from camoufox.server import launch_server
launch_server(port=1234, ws_path='hello')
```
**Connect to Server:**
```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.firefox.connect('ws://localhost:1234/hello')
    page = browser.new_page()
```

#### **Virtual Display (Linux)**
To run Camoufox headfully on a headless Linux server, install `xvfb` and use `headless="virtual"`.
```bash
# Debian-based
sudo apt-get install xvfb
```
```python
from camoufox.sync_api import Camoufox

with Camoufox(headless="virtual") as browser:
    page = browser.new_page()
```

#### **Main World Execution**
By default, `page.evaluate()` runs in an isolated world and cannot modify the DOM. To modify the DOM, enable main world execution and prefix your script with `mw:`. **Warning:** This can be detected by the target website.

```python
with Camoufox(main_world_eval=True) as browser:
    page = browser.new_page()
    page.goto("https://example.com")
    # This will remove the h1 element
    page.evaluate("mw:document.querySelector('h1').remove()")
```

***

### **Document 5: Fingerprinting Protection Details**

**URL:** `https://camoufox.com/fingerprint/*`

#### **General Approach (Fingerprint Injection)**
Data is intercepted at the C++ implementation level, making changes undetectable through JavaScript inspection. Config data not set by the user is automatically populated using BrowserForge fingerprints to mimic real-world traffic.

#### **Canvas**
Instead of adding detectable noise, Camoufox uses a patched build of Skia with modified subpixel rendering logic. This makes it impossible to distinguish from a device that naturally renders anti-aliasing differently. This patch is closed-source.
*   `canvas:aaOffset`: Offset each pixel's transparency when drawing anti-aliasing.

#### **Cursor Movement**
Camoufox has built-in support for human-like cursor movement, rewritten in C++ from rifosnake's HumanCursor algorithm.
*   `humanize`: (boolean) Enable/disable human-like cursor movement.
*   `showcursor`: (boolean) Toggles a cursor highlighter for debugging (not visible to the page).

#### **Fonts**
Camoufox prevents font fingerprinting by:
1.  Bundling default fonts for Windows 11, macOS Sonoma, and Linux (TOR bundle).
2.  Automatically loading the correct default fonts for the spoofed User-Agent OS.
3.  Allowing custom fonts to be loaded via the `fonts` parameter.
4.  Randomly shifting the spacing of each letter by 0-0.1px to defeat font metrics fingerprinting.

#### **Geolocation & Intl**
Spoofs location, timezone, and internationalization APIs.
*   `geolocation:latitude`, `geolocation:longitude`: Sets GPS coordinates.
*   `timezone`: Sets a TZ timezone identifier (e.g., "America/Chicago").
*   `locale:language`, `locale:region`: Spoofs the Intl API and system language/region.

#### **Media & Audio**
*   **Media Devices**: Spoofs the number of microphones, webcams, and speakers.
*   **AudioContext**: Spoofs `sampleRate`, `outputLatency`, and `maxChannelCount`.

#### **Navigator**
All navigator properties can be fully spoofed. `navigator.webdriver` is always `false`.
*   Includes `userAgent`, `platform`, `hardwareConcurrency`, `language`, `doNotTrack`, etc.

#### **Screen & Window**
*   **Screen**: Spoofs `screen.width`, `screen.height`, `screen.availWidth`, `screen.colorDepth`, etc.
*   **Window**: Spoofs `window.innerWidth`, `window.outerWidth`, `window.devicePixelRatio`, `window.history.length`, etc.

#### **WebGL**
Supports spoofing of all major WebGL properties. **Warning:** Do not randomly assign values, as this will lead to detection as an unknown device.
*   `webGl:renderer`: Name of the unmasked WebGL renderer.
*   `webGl:vendor`: Name of the unmasked WebGL vendor.
*   `webGl:supportedExtensions`: An array of supported WebGL extensions.
*   `webGl:contextAttributes`: A dictionary of WebGL context attributes.
*   `webGl:parameters`: A dictionary of WebGL parameters (keyed by GL enums).
*   `webGl:shaderPrecisionFormats`: A dictionary of WebGL shader precision formats.
*   *Equivalent properties exist for `webGl2`.*

#### **WebRTC IP**
Implements WebRTC IP spoofing at the protocol level by modifying ICE candidates and SDP.
*   `webrtc:ipv4`: IPv4 address to use.
*   `webrtc:ipv6`: IPv6 address to use.

***

### **Document 6: Development and Building**

**URL:** `https://camoufox.com/development/*`

#### **Build System Overview**
Camoufox's build system is based on the LibreWolf system. It applies fingerprint masking patches, addons, debloat optimizations, and fonts to the Firefox source code to produce portable builds for Linux, Windows, and macOS.

#### **Building in CLI (Linux Only)**
**Warning:** The build system is designed for Linux. WSL will not work.

1.  **Clone the repository:**
    ```bash
    git clone --depth 1 https://github.com/daijro/camoufox
    cd camoufox
    ```
2.  **Prepare source code:**
    ```bash
    make dir
    ```
3.  **Bootstrap the system (one-time setup):**
    ```bash
    make bootstrap
    ```
4.  **Build and package:**
    ```bash
    python3 multibuild.py --target linux windows macos --arch x86_64 arm64 i686
    ```

#### **Building in Docker (for non-Linux systems)**
1.  **Create the Docker image:**
    ```bash
    docker build -t camoufox-builder .
    ```
2.  **Build Camoufox:**
    ```bash
    docker run -v "$(pwd)/dist:/app/dist" camoufox-builder --target <os> --arch <arch>
    ```
    To use the host's `.mozbuild` directory for caching:
    ```bash
    docker run \
      -v "$HOME/.mozbuild:/root/.mozbuild:rw,z" \
      -v "$(pwd)/dist:/app/dist" \
      camoufox-builder \
      --target <os> \
      --arch <arch>
    ```

#### **Development Tools & Workflow**
A developer UI is available via `scripts/developer.py` (`make edits`) to manage, create, and edit patches.

The `workflow` page provides a detailed flowchart for debugging why a website is flagging the browser. The process involves systematically testing a clean Firefox build, then adding layers (headless mode, patches, configs) to isolate the cause of detection. If all else fails, the final step is to deobfuscate the website's JavaScript to understand its detection methods.

**Key Debugging Commands:**
*   `make ff-dbg`: Setup vanilla Firefox with minimal patches.
*   `make build`: Build the source code.
*   `make run`: Run the built browser.
*   `make run args="--headless https://test.com"`: Run in headless mode.
*   `make edits`: Open the developer UI to manage patches.
*   `make edit-cfg`: Edit `camoufox.cfg`.

***

### **Document 7: Testing, Research, and Legal**

**URL:** `https://camoufox.com/tests/*`, `https://camoufox.com/webgl-research`, `https://camoufox.com/legal`

#### **WebGL Research**
Camoufox maintains a database of WebGL vendor and renderer combinations seen in Firefox on different operating systems. This data is used to ensure that generated WebGL fingerprints correspond to real-world hardware combinations, avoiding detection. The research page lists common combinations and marks whether sufficient data has been collected for accurate simulation.

#### **Test Pages**
Camoufox provides several pages for testing browser fingerprints:
*   **WebGL Test (`/tests/webgl`):** Displays your browser's complete WebGL fingerprint in a JSON format compatible with Camoufox's configuration.
*   **Canvas Tests (`/tests/canvas`):** A comprehensive suite that runs multiple tests (text rendering, shapes, gradients, shadows) to generate canvas hashes and check for randomization.
*   **Low-Entropy Canvas Test (`/tests/private/canvas-low-entropy`):** A specific test to check for known rendering engine results.
*   **Shadow DOM Test (`/tests/shadowdom`):** A demo page for testing clicks on elements inside a shadow DOM.
*   **Button Click Test (`/tests/buttonclick`):** A simple page with a button for click testing.

#### **Legal & Donation**
*   **Liability Disclaimer:** Camoufox is provided without warranty and is intended for ethical and research purposes only. Users are responsible for complying with all applicable laws. The acceptable use policy prohibits illegal, malicious, or fraudulent activities.
*   **Donate:** The project is maintained by an independent student researcher and accepts donations to support development and tuition.
