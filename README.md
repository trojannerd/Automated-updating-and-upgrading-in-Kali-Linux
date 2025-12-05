# Automated-updating-and-upgrading-in-Kali-Linux
# Only run when WiFi connection comes up
    #!/bin/bash
    IFACE="$1"
    STATUS="$2"
    
    
    if [ "$STATUS" = "up" ]; then
    
        # Grab current SSID
        SSID=$(nmcli -t -f active,ssid dev wifi | grep yes | cut -d: -f2)
        TARGET_SSID="RO'ZALI"
    
        LASTRUN_FILE="/var/log/auto_update_last_run"
    
        if [[ "$IFACE" == wlp* || "$IFACE" == wlan* ]]; then
            if [ "$SSID" = "$TARGET_SSID" ]; then
    
                # Get today's date
                TODAY=$(date "+%Y-%m-%d")
    
                # If last run file doesn’t exist OR date changed → run update
                if [ ! -f "$LASTRUN_FILE" ] || [ "$(cat $LASTRUN_FILE)" != "$TODAY" ]; then
    
                    echo "Connected to $SSID. Running auto update..."
                    echo "$TODAY" | sudo tee "$LASTRUN_FILE" > /dev/null
    
                    /usr/local/bin/update_upgrade
    
                else
                    echo "Already updated today. Skipping."
                fi
    
            else
                echo "Connected to $SSID — not the target WiFi. Skipping."
            fi
        fi
      fi

# Runs and Logs data automatically.

    #!/bin/bash
    echo "Starting system update"
    sudo apt update
    echo "Upgrading installed packages"
    sudo apt upgrade -y
    echo "Logging update and upgrade details"
    sudo apt update &> update_log.txt
    sudo apt upgrade -y &>> update_log.txt

