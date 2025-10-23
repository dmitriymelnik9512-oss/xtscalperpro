# Building APK for XT-ScalperPro

## ⚠️ Important Note
Building Android APKs requires significant system resources and is best done on a local Linux machine or dedicated build server. Replit's environment has limitations for APK building.

## Option 1: Build Locally (Recommended)

### Requirements
- Linux (Ubuntu 20.04+ recommended) or WSL2 on Windows
- At least 8GB RAM
- 10GB+ free disk space
- Python 3.8+

### Step-by-Step Instructions

1. **Download Project Files**
   - Download all project files from this Replit
   - Ensure you have: `main.py`, `kivy_app.py`, `buildozer.spec`

2. **Install System Dependencies**
```bash
sudo apt update
sudo apt install -y git zip unzip openjdk-17-jdk python3-pip autoconf libtool pkg-config zlib1g-dev libncurses5-dev libncursesw5-dev libtinfo5 cmake libffi-dev libssl-dev
```

3. **Install Buildozer**
```bash
pip3 install --upgrade buildozer
pip3 install --upgrade cython
```

4. **Navigate to Project Directory**
```bash
cd /path/to/your/project
```

5. **Build the APK**
```bash
buildozer android debug
```

This will:
- Download Android SDK/NDK (first time only, ~2-3GB)
- Compile all dependencies
- Create APK in `bin/` folder
- Takes 20-40 minutes on first build

6. **Find Your APK**
```bash
ls -lh bin/*.apk
```

The APK will be named something like: `xtscalperpro-1.0-debug.apk`

## Option 2: Use GitHub Actions (Automated Cloud Build)

1. **Create GitHub Repository**
   - Create a new GitHub repo
   - Push all project files

2. **Add GitHub Action**
   Create `.github/workflows/build-apk.yml`:

```yaml
name: Build APK

on:
  push:
    branches: [ main ]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: 3.9
    
    - name: Install dependencies
      run: |
        sudo apt update
        sudo apt install -y openjdk-17-jdk
        pip install buildozer cython
    
    - name: Build APK
      run: |
        buildozer android debug
    
    - name: Upload APK
      uses: actions/upload-artifact@v2
      with:
        name: app-debug
        path: bin/*.apk
```

3. **Trigger Build**
   - Push to GitHub
   - Go to Actions tab
   - Download APK from artifacts

## Option 3: Use Online Build Services

### Replit Deployments (Alternative)
Instead of APK, you can:
1. Deploy this as a web app on Replit
2. Access it from any mobile browser
3. Add to home screen for app-like experience

### Third-Party Services
- **BeeWare/Briefcase**: Cross-platform app builder
- **Kivy Buildozer on Colab**: Use Google Colab for building
- **Ubuntu VM/VPS**: Rent a server for building

## Option 4: Pre-configured APK Builder (Docker)

```bash
docker run --rm -v "$PWD":/app kivy/buildozer android debug
```

## After Building

### Testing APK
1. Transfer APK to Android device
2. Enable "Install from Unknown Sources"
3. Install and test

### Important: Configure API Keys
Before using on mobile:
1. The APK needs XT_API_KEY and XT_API_SECRET
2. You can either:
   - Hardcode them in `main.py` (NOT recommended for security)
   - Create a config file the app reads
   - Use Android's secure storage

### For Production APK
```bash
buildozer android release
```

Then sign with your keystore.

## Troubleshooting

### Build Fails
- Ensure all system dependencies installed
- Check you have enough disk space
- Try: `buildozer android clean`

### APK Won't Install
- Check minimum Android version (API 21+)
- Enable installation from unknown sources
- Check APK isn't corrupted

### Runtime Errors
- Check permissions in AndroidManifest.xml
- Verify all dependencies are in buildozer.spec
- Test on different Android versions

## Quick Start Alternative

If you just want to test quickly:
1. Access the web version at your Replit URL
2. Open on mobile browser
3. Use as web app (no APK needed)

The web interface works perfectly on mobile browsers!
