# Digital Signage Player

Web-based player application for displaying digital signage content on any device with a browser.

## Features

- Auto-registration with unique display code
- Real-time content updates
- Multi-region layout support
- Image and video playback
- Playlist rotation
- Automatic scaling to fit any screen size
- Offline-ready with reconnection logic

## Supported Devices

- **Raspberry Pi** (with Chromium in kiosk mode)
- **Android TV** (with Chrome/Firefox)
- **Smart TVs** (with built-in browser)
- **Windows/Mac/Linux** (any modern browser)
- **Tablets** (iPad, Android tablets)

## Quick Start

### Option 1: Simple HTTP Server (Python)

```bash
cd player/public
python3 -m http.server 8080
```

Then open: `http://localhost:8080`

### Option 2: Node.js HTTP Server

```bash
npm install -g http-server
cd player/public
http-server -p 8080
```

### Option 3: PHP Built-in Server

```bash
cd player/public
php -S localhost:8080
```

## Setup Instructions

### 1. Start the Player

Open the player in a browser on your display device. You'll see a setup screen with a unique code (e.g., "ABC123").

### 2. Register in CMS

1. Login to the CMS Dashboard
2. Navigate to "Displays"
3. Click "Add Display"
4. Enter the display code from step 1
5. Enter a name and location
6. Click "Add Display"
7. **IMPORTANT**: Copy the access token shown (it's only shown once!)

### 3. Configure Player

1. In the player window, you'll see a token input field
2. Paste the access token you copied from the CMS
3. Click "Save Token & Connect"
4. The player will authenticate and connect

### 4. Assign Content

1. Back in the CMS, go to Displays
2. Find your display
3. Assign a layout from the dropdown
4. The player will automatically start displaying content within 30 seconds

## Authentication

The player uses token-based authentication for security:

- Each display has a unique access token
- Token is generated when display is created in CMS
- Token must be entered in player to authenticate
- Token is stored securely in browser localStorage
- If token is lost, regenerate it in CMS

**Security Note**: Never share your display tokens publicly. Treat them like passwords.

## Configuration

### API URL

The player automatically detects the API URL based on the environment:

**Development (localhost):**
- Automatically uses `http://localhost:8000/api`

**Production:**
- Automatically uses the same hostname as the player
- Example: If player is at `https://player.yourdomain.com`, it uses `https://player.yourdomain.com/api`

**Manual Override:**
You can override the API URL using a query parameter:
```
http://yourplayer.com?api=https://api.yourdomain.com/api
```

**Custom Configuration:**
Edit `index.html` and modify the `getApiUrl()` function if you need custom logic.

### Refresh Interval

Change how often the player checks for updates (default: 30 seconds):

```javascript
refreshInterval = setInterval(checkForContent, 30000); // 30 seconds
```

## Raspberry Pi Setup

### 1. Install Raspberry Pi OS Lite

### 2. Install Chromium

```bash
sudo apt-get update
sudo apt-get install chromium-browser unclutter
```

### 3. Auto-start in Kiosk Mode

Edit `/etc/xdg/lxsession/LXDE-pi/autostart`:

```bash
@xset s off
@xset -dpms
@xset s noblank
@chromium-browser --kiosk --incognito --disable-infobars http://localhost:8080
```

### 4. Disable Screen Blanking

```bash
sudo nano /etc/lightdm/lightdm.conf
```

Add under `[Seat:*]`:
```
xserver-command=X -s 0 -dpms
```

### 5. Setup Player to Start on Boot

Create `/etc/systemd/system/player.service`:

```ini
[Unit]
Description=Digital Signage Player
After=network.target

[Service]
Type=simple
User=pi
WorkingDirectory=/home/pi/player/public
ExecStart=/usr/bin/python3 -m http.server 8080
Restart=always

[Install]
WantedBy=multi-user.target
```

Enable and start:
```bash
sudo systemctl enable player
sudo systemctl start player
```

## Android TV Setup

### 1. Install Chrome or Firefox

### 2. Enable Developer Options

Settings → About → Click "Build" 7 times

### 3. Enable USB Debugging

### 4. Install Kiosk Browser App

Search for "Kiosk Browser" in Play Store

### 5. Configure Kiosk Browser

- Set URL: `http://your-server-ip:8080`
- Enable fullscreen
- Disable navigation
- Set auto-start on boot

## Smart TV Setup

### 1. Open Built-in Browser

Most Smart TVs have a web browser app

### 2. Navigate to Player URL

Enter: `http://your-server-ip:8080`

### 3. Bookmark for Easy Access

Save the URL for quick access

## Windows/Mac Kiosk Setup

### Chrome Kiosk Mode

```bash
# Windows
chrome.exe --kiosk --app=http://localhost:8080

# Mac
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --kiosk --app=http://localhost:8080
```

### Firefox Kiosk Mode

```bash
firefox --kiosk http://localhost:8080
```

## Troubleshooting

### Display Code Not Showing

- Check if backend API is running
- Verify API_URL in index.html
- Check browser console for errors

### Content Not Loading

- Ensure display is registered in CMS
- Verify layout is assigned to display
- Check if content files exist in backend storage
- Look for CORS errors in browser console

### Videos Not Playing

- Ensure video format is supported (MP4 recommended)
- Check if autoplay is allowed in browser
- Try muting videos (autoplay requires muted)

### Screen Goes Blank

- Disable screen saver/sleep mode
- Check power settings
- Ensure player is set to auto-refresh

### Connection Lost

- Player will automatically retry every 10 seconds
- Check network connection
- Verify backend is accessible from display device

## Network Requirements

### Ports

- Player: 8080 (or your chosen port)
- Backend API: 8000 (default Laravel)
- Frontend CMS: 3000 (development)

### Firewall

Ensure display device can access:
- Backend API (port 8000)
- Storage files (port 8000/storage)

## Performance Tips

### For Raspberry Pi

- Use Raspberry Pi 4 (4GB+ RAM) for best performance
- Use hardware-accelerated video playback
- Optimize video files (H.264, 1080p max)
- Use wired Ethernet instead of WiFi

### For All Devices

- Keep videos under 100MB
- Use compressed images (JPEG, WebP)
- Limit number of regions (max 4-6)
- Use local network for faster loading

## Advanced Features

### Offline Mode

Player caches last known layout and continues playing even if connection is lost.

### Auto-recovery

If player crashes or loses connection, it automatically:
- Retries connection every 10 seconds
- Reloads last known content
- Shows error message briefly

### Scaling

Player automatically scales layout to fit any screen size while maintaining aspect ratio.

## Production Deployment

### 1. Use HTTPS

Configure SSL certificate for secure connections

### 2. Use CDN

Serve media files from CDN for better performance

### 3. Monitor Displays

Set up monitoring to track display status

### 4. Remote Management

Use VNC or TeamViewer for remote access to displays

### 5. Backup Power

Use UPS to prevent display shutdown during power outages

## License

Proprietary
