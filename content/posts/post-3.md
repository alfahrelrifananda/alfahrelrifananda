---
title: "Create a free custom VPN toggle script for Waybar"
date: 2026-01-18T23:15:00+07:00
description: "Tired of manually managing VPN connections? Here's how I built a custom toggle script for Waybar that randomly selects servers and keeps me protected with just one click."
tags: [GNU/Linux, Scripting, Tutorial]
---

Managing VPN connections on Linux shouldn't require opening terminals and typing commands. After getting tired of manually starting and stopping OpenVPN services, I built a custom toggle script for Waybar that does it all with a single click. Here's how it works and how you can build your own.

If you're using a tiling window manager with Waybar, you probably appreciate quick access to system controls. But VPN management often gets left out—you end up with some clunky system tray app or resort to terminal commands. I wanted something better: a clean status indicator in my bar, one-click toggling, and random server selection for better privacy.

Let me walk you through building this solution. It's surprisingly straightforward, and once it's set up, you'll wonder how you lived without it.

## What We're Building

The end result is a custom Waybar module that:

* Shows your current VPN status and connected country
* Toggles your VPN connection with a single click
* Randomly selects from your available servers for better privacy
* Sends desktop notifications when connecting or disconnecting
* Handles multiple ProtonVPN configs automatically

This isn't just a status indicator—it's a complete VPN management solution that lives right in your status bar.

## Why ProtonVPN?

I chose ProtonVPN for this setup because it's privacy-focused, has excellent free tier servers, and works perfectly with OpenVPN. The configs are clean, well-maintained, and just work. You could adapt this for other VPN providers, but ProtonVPN makes it especially easy.

## The Setup: Getting Your VPN Configs Ready

Before we write any scripts, we need to get our VPN configurations in order. This is the foundation everything else builds on.

### Installing the Essentials

First, you'll need OpenVPN and notification support:

```bash
# Arch Linux
sudo pacman -S openvpn libnotify

# Ubuntu/Debian  
sudo apt install openvpn libnotify-bin

# Fedora
sudo dnf install openvpn libnotify
```

Nothing fancy here—just the standard OpenVPN client and notification tools that most Linux systems use.

### Getting Your ProtonVPN Configs

Head over to your ProtonVPN account dashboard and download the OpenVPN configuration files. You'll find them under Downloads → OpenVPN configuration files. Grab the servers you want to use—I recommend getting a mix of different countries for variety.

Once downloaded, move them to the OpenVPN client directory and rename them:

```bash
# Move the configs
sudo mv ~/Downloads/*.ovpn /etc/openvpn/client/

# Rename from .ovpn to .conf (systemd requires this)
cd /etc/openvpn/client/
for file in *.ovpn; do
    sudo mv "$file" "${file%.ovpn}.conf"
done
```

The rename is crucial—systemd's OpenVPN service specifically looks for `.conf` files, not `.ovpn` files.

### Setting Up Authentication

ProtonVPN configs need your credentials to connect. Instead of getting prompted every time, we'll set up a credentials file:

```bash
sudo nano /etc/openvpn/client/protonvpn-credentials.txt
```

Add two lines—your ProtonVPN username on the first line, password on the second. That's it. Now secure it:

```bash
sudo chown openvpn:openvpn /etc/openvpn/client/protonvpn-credentials.txt
sudo chmod 600 /etc/openvpn/client/protonvpn-credentials.txt
```

Here's the catch: the downloaded configs often don't reference this credentials file correctly. They'll have just `auth-user-pass` without a path, which means they'll prompt you for credentials. Let's fix that:

```bash
sudo sed -i 's|^auth-user-pass$|auth-user-pass /etc/openvpn/client/protonvpn-credentials.txt|' /etc/openvpn/client/*.conf
```

Verify the fix worked:

```bash
grep "auth-user-pass" /etc/openvpn/client/*.conf
```

You should see the full path to the credentials file on every line.

### Fixing Systemd Timeout Issues

Here's something that tripped me up initially: OpenVPN connections can take 30-60 seconds to establish, but systemd's default timeout is 90 seconds for the entire startup process. Sometimes that's not quite enough, especially on slower connections.

Create a systemd override to give it more time:

```bash
sudo mkdir -p /etc/systemd/system/openvpn-client@.service.d/
sudo tee /etc/systemd/system/openvpn-client@.service.d/override.conf <<EOF
[Service]
TimeoutStartSec=120
EOF
sudo systemctl daemon-reload
```

This gives OpenVPN two full minutes to connect, which is plenty even for the slowest connections.

## Building the Toggle Script

Now for the fun part—writing the script that actually manages your VPN connections.

Create the script file:

```bash
mkdir -p ~/.config/waybar
touch ~/.config/waybar/vpn-toggle.sh
chmod +x ~/.config/waybar/vpn-toggle.sh
```

Open it in your editor and let's build it piece by piece.

### The Server Configuration

Start by defining which VPN servers you want to use:

```bash
#!/bin/bash
# VPN Toggle Script for ProtonVPN with Random Server Selection

CONFIGS=(
    "ca-free"
    "jp-free"
    "no-free"
    "mx-free-12.protonvpn.udp"
    "nl-free-135.protonvpn.udp"
    "pl-free-2.protonvpn.udp"
    "ro-free-28.protonvpn.udp"
    "sg-free-15.protonvpn.udp"
    "ch-free-4.protonvpn.udp"
)

STATE_FILE="/tmp/protonvpn-state"
CURRENT_FILE="/tmp/protonvpn-current"
```

The config names should match your `.conf` files exactly (minus the `.conf` extension). The state files track which server you're connected to—we'll use these for the status display.

### Country Name Mapping

Nobody wants to see cryptic abbreviations in their status bar. Let's map country codes to full names:

```bash
get_country_name() {
    case "$1" in
        ca) echo "Canada" ;;
        jp) echo "Japan" ;;
        no) echo "Norway" ;;
        mx) echo "Mexico" ;;
        nl) echo "Netherlands" ;;
        pl) echo "Poland" ;;
        ro) echo "Romania" ;;
        sg) echo "Singapore" ;;
        ch) echo "Switzerland" ;;
        *) echo "Unknown" ;;
    esac
}
```

Add entries for whatever servers you're using. The function extracts the country code from the config name and returns the full country name.

### Checking VPN Status

We need to know if a VPN is currently running:

```bash
is_vpn_running() {
    systemctl is-active --quiet "openvpn-client@*"
}
```

This checks if any OpenVPN client service is active. The wildcard matches all possible configs, so we don't need to check each one individually.

### Stopping the VPN

When disconnecting, we want to make sure everything stops cleanly:

```bash
stop_vpn() {
    # Stop all openvpn services
    sudo systemctl stop "openvpn-client@*" 2>/dev/null
    
    # Clean up state files
    rm -f "$STATE_FILE" "$CURRENT_FILE"
    
    # Give it a moment to fully stop
    sleep 0.5
    
    # Notify the user
    notify-send -t 3000 -u normal "VPN Disconnected" "Your connection is no longer protected"
}
```

The sleep is important—we want to make sure the service actually stops before the function returns. The notification gives you immediate feedback.

### Starting the VPN

This is where the magic happens—random server selection and connection:

```bash
start_vpn() {
    # Stop any running VPN first
    sudo systemctl stop "openvpn-client@*" 2>/dev/null
    sleep 1
    
    # Pick a random server
    RANDOM_CONFIG="${CONFIGS[$RANDOM % ${#CONFIGS[@]}]}"
    
    # Extract country info
    COUNTRY_CODE=$(echo "$RANDOM_CONFIG" | cut -d'-' -f1)
    COUNTRY_NAME=$(get_country_name "$COUNTRY_CODE")
    
    # Notify we're connecting
    notify-send -t 3000 -u normal "VPN Connecting" "Connecting to $COUNTRY_NAME..."
    
    # Start the VPN
    sudo systemctl start "openvpn-client@${RANDOM_CONFIG}"
    sleep 3
    
    # Check if it actually connected
    if systemctl is-active --quiet "openvpn-client@${RANDOM_CONFIG}"; then
        echo "connected" > "$STATE_FILE"
        echo "$RANDOM_CONFIG" > "$CURRENT_FILE"
        notify-send -t 3000 -u normal "VPN Connected" "Connected to $COUNTRY_NAME"
    else
        notify-send -t 3000 -u critical "VPN Failed" "Could not connect to $COUNTRY_NAME"
    fi
}
```

The `$RANDOM` variable gives us a random number, and we use modulo to select a random index from our configs array. The three-second sleep gives the VPN time to establish the connection before we check if it succeeded.

### The Main Toggle Logic

Finally, tie it all together:

```bash
# Main toggle logic
if is_vpn_running; then
    stop_vpn
else
    start_vpn
fi
```

Simple and clean—if a VPN is running, stop it. If not, start one.

## Building the Status Display

The toggle script handles the actual VPN management. Now we need a script that shows the current status in Waybar.

Create the status script:

```bash
touch ~/.config/waybar/vpn-status.sh
chmod +x ~/.config/waybar/vpn-status.sh
```

Here's the complete status script:

```bash
#!/bin/bash
# VPN Status Script for Waybar

CURRENT_FILE="/tmp/protonvpn-current"

# Country name mapping (same as toggle script)
get_country_name() {
    case "$1" in
        ca) echo "Canada" ;;
        jp) echo "Japan" ;;
        no) echo "Norway" ;;
        mx) echo "Mexico" ;;
        nl) echo "Netherlands" ;;
        pl) echo "Poland" ;;
        ro) echo "Romania" ;;
        sg) echo "Singapore" ;;
        ch) echo "Switzerland" ;;
        *) echo "VPN" ;;
    esac
}

# Check VPN status and output JSON for Waybar
if systemctl is-active --quiet "openvpn-client@*"; then
    if [ -f "$CURRENT_FILE" ]; then
        CONFIG=$(cat "$CURRENT_FILE")
        COUNTRY_CODE=$(echo "$CONFIG" | cut -d'-' -f1)
        COUNTRY_NAME=$(get_country_name "$COUNTRY_CODE")
        echo "{\"text\":\" $COUNTRY_NAME\",\"tooltip\":\"VPN Connected: $CONFIG\",\"class\":\"connected\"}"
    else
        echo "{\"text\":\" Connected\",\"tooltip\":\"VPN Connected\",\"class\":\"connected\"}"
    fi
else
    echo "{\"text\":\"󰷷 Disconnected\",\"tooltip\":\"VPN Disconnected - Click to connect\",\"class\":\"disconnected\"}"
fi
```

This outputs JSON that Waybar can parse. The `text` field is what shows in your bar, `tooltip` appears on hover, and `class` lets us style it with CSS.

## Integrating with Waybar

Now we need to tell Waybar about our new module.

### Waybar Configuration

Edit your Waybar config file (usually `~/.config/waybar/config`):

```json
{
    "modules-right": [
        "custom/vpn",
        "pulseaudio",
        "clock"
    ],
    
    "custom/vpn": {
        "exec": "~/.config/waybar/vpn-status.sh",
        "interval": 5,
        "return-type": "json",
        "on-click": "~/.config/waybar/vpn-toggle.sh",
        "format": "{}"
    }
}
```

The module runs every 5 seconds to check status. When you click it, it runs the toggle script.

### Styling the Module

Add some visual polish in your Waybar CSS file (`~/.config/waybar/style.css`):

```css
#custom-vpn.connected {
    background-color: #a6e3a1;
    color: #1e1e2e;
    padding: 0 10px;
    margin: 0 5px;
    border-radius: 5px;
    font-weight: bold;
}

#custom-vpn.disconnected {
    background-color: #f38ba8;
    color: #1e1e2e;
    padding: 0 10px;
    margin: 0 5px;
    border-radius: 5px;
}
```

These colors are from the Catppuccin theme, but use whatever matches your setup. The key is having distinct styling for connected and disconnected states.

## Setting Up Passwordless Sudo

The scripts need to run systemctl commands with sudo privileges. Rather than typing your password every time, set up passwordless sudo for these specific commands:

```bash
sudo visudo
```

Add this line (replace `your_username` with your actual username):

```
your_username ALL=(ALL) NOPASSWD: /usr/bin/systemctl start openvpn-client@*, /usr/bin/systemctl stop openvpn-client@*
```

This is secure because it only allows these specific systemctl commands—nothing else gets passwordless access.

## Testing Your Setup

Before restarting Waybar, let's make sure everything works.

Test the toggle script manually:

```bash
~/.config/waybar/vpn-toggle.sh
```

You should see a notification and your VPN should connect to a random server. Check the status:

```bash
systemctl status "openvpn-client@*"
```

Test the status script:

```bash
~/.config/waybar/vpn-status.sh
```

You should see JSON output with your connection info.

If everything looks good, restart Waybar:

```bash
killall waybar && waybar &
```

## Customizing for Your Setup

The beauty of this approach is how easy it is to customize.

### Adding More Servers

Just add the config name to the CONFIGS array and update the country mapping:

```bash
CONFIGS=(
    "ca-free"
    "us-free-01.protonvpn.udp"
    "de-free-15.protonvpn.udp"
    # Keep adding...
)

get_country_name() {
    case "$1" in
        # ... existing mappings
        us) echo "United States" ;;
        de) echo "Germany" ;;
        # Keep adding...
    esac
}
```

### Custom Notification Styling

Want different notification icons? Modify the `notify-send` calls:

```bash
notify-send -i network-vpn -t 3000 -u normal "VPN Connected" "Connected to $COUNTRY_NAME"
```

### Adding Connection Logging

Track your VPN usage by adding logging:

```bash
LOG_FILE="$HOME/.local/share/vpn-toggle.log"

# In start_vpn():
echo "$(date): Connected to $COUNTRY_NAME" >> "$LOG_FILE"

# In stop_vpn():
echo "$(date): Disconnected" >> "$LOG_FILE"
```

## Why This Approach Works

This setup has served me well for months now. Here's why it's better than the alternatives:

* **One-click simplicity.** No menus, no terminal commands, just click and connect.
* **Privacy through randomness.** Different server each time makes tracking harder.
* **Visual feedback.** You always know your VPN status at a glance.
* **No dependencies.** Just bash, systemd, and standard tools.
* **Easy maintenance.** Add or remove servers by editing one array.

The whole system is about 100 lines of bash across two files. It's simple, reliable, and does exactly what you need without any bloat.

## My Recommendation

If you're running Waybar and use a VPN regularly, this is absolutely worth setting up. It takes maybe 20 minutes to get everything configured, and then it just works. Every day. Reliably.

The random server selection is particularly nice—it's one less decision to make, and it naturally varies your connection pattern. Sometimes I'm browsing from Canada, sometimes from Japan, sometimes from Switzerland. All with zero effort.

Give it a try. Download your ProtonVPN configs, set up the scripts, and enjoy having proper VPN management right in your status bar. You might find, like I did, that it's exactly what you've been missing.

Because managing your VPN shouldn't be work. It should be one click. And with this setup, that's exactly what it is.
