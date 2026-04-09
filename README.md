# Monero Ban List

A banlist for Monero nodes.

These nodes were found displaying behaviour that the normal Monero nodes would not do. The code and the method that was used 
to tell these nodes apart is here: https://github.com/Boog900/p2p-proxy-checker. Some nodes are now hiding this fingerprint,
you can see the network state with the monitor by @Rucknium here: https://moneronet.info/.

### Usage

To use download `ban_list.txt` and start `monerod` with `--ban-list /path/to/ban_list.txt` 
or add `ban-list=/path/to/ban_list.txt` to your `bitmonero.conf` file.

### Signatures 

To verify the signatures get our GPG keys from these locations:

| Person        | Location                                                                                                              |
|---------------|-----------------------------------------------------------------------------------------------------------------------|
| boog900       | <https://github.com/Cuprate/cuprate/tree/7b8756fa80e386fb04173d8220c15c86bf9f9888/misc/gpg_keys>                      |
| jeffro256     | <https://github.com/monero-project/monero/blob/004ead1a14d60ff757880c5b16b894b526427829/utils/gpg_keys/jeffro256.asc> |
| Rucknium      | <https://rucknium.me/pgp.txt>                                                                                         |
| SyntheticBird | <https://gist.github.com/SyntheticBird45/fd48b3bded266159cc0981c61f0e93f3>                                            |
| hinto-janai   | <https://github.com/Cuprate/cuprate/blob/049cec184f8d46d2fad3274a041ff5e72eabcb73/misc/gpg_keys/hinto-janai.asc>      |

Then import the keys and verify the signatures:

```bash
gpg --verify ./sigs/boog900.sig ban_list.txt
```

You should now see: 
```
gpg: Signature made Wed Dec  4 23:27:28 2024 GMT
gpg:                using EDDSA key A875F544CB569CB96889791E42AB1287CB0041C2
gpg: Good signature from "Boog900 ...
```

Now repeat with as many `Person`'s signatures as you please.

---
### Automated Monero Banlist Updater
This script automates the process of keeping your Monero node's banlist up to date. It downloads the latest list, compares it with your current one, and only restarts the daemon if changes are detected.
#### The Universal Script
Create a file (e.g., `/usr/local/bin/update-monero-banlist.sh`) and paste the following code.

**Note**: Check the **CONFIGURATION** section below the script to adapt it to your specific environment (Docker or Native).
```bash
#!/bin/bash

# ==============================================================================
# CONFIGURATION - ADJUST THESE SETTINGS
# ==============================================================================

# 1. The URL of the banlist
BANLIST_URL="https://raw.githubusercontent.com/Boog900/monero-ban-list/refs/heads/main/ban_list.txt"

# 2. Where to save the banlist (Adjust this path!)
#    - For Docker: Use the path on your HOST machine (e.g., in your monero volume)
#    - For Native: Usually /home/youruser/.bitmonero/ban_list.txt
TARGET_FILE="/path/to/your/monero/ban_list.txt"

# 3. Choose your restart method (Comment/Uncomment the one you need)
#    METHOD A: For Docker users (monerod = Name of your container)
RESTART_CMD="/usr/bin/docker restart monerod"

#    METHOD B: For Native/Systemd users
# RESTART_CMD="/usr/bin/systemctl restart monero.service"

# ==============================================================================
# SCRIPT LOGIC (No need to change anything below this line)
# ==============================================================================

TEMP_FILE="/tmp/monero_banlist_new.txt"

echo "$(date): Checking for updates..."

# 1. Download the list
if ! /usr/bin/curl -s -o "$TEMP_FILE" "$BANLIST_URL" || [ ! -s "$TEMP_FILE" ]; then
    echo "ERROR: Download failed or file is empty."
    rm -f "$TEMP_FILE"
    exit 1
fi

# 2. Compare content (Skip if TARGET_FILE does not exist yet)
if [ -f "$TARGET_FILE" ] && cmp -s "$TEMP_FILE" "$TARGET_FILE"; then
    echo "INFO: List is already up to date. No restart needed."
    rm "$TEMP_FILE"
    exit 0
fi

# 3. Apply update
echo "INFO: New banlist detected. Updating..."
# Create directory if it doesn't exist
mkdir -p "$(dirname "$TARGET_FILE")"
mv "$TEMP_FILE" "$TARGET_FILE"

# Ensure the file is readable by the Monero daemon (Docker or Native)
chmod 644 "$TARGET_FILE"

# 4. Restart Node
echo "INFO: Executing restart: $RESTART_CMD"
if $RESTART_CMD > /dev/null 2>&1; then
    echo "SUCCESS: Monero node updated and restarted."
else
    echo "ERROR: Restart failed. Check your permissions or service/container name."
    exit 1
fi
```
#### Installation & Setup
**1. Preparation**
Make the script executable after saving:
```bash
sudo chmod +x /usr/local/bin/update-monero-banlist.sh
```
**2. Node Configuration**
Your Monero node needs to know it should use a banlist. Add this line to your `bitmonero.conf`:
```
ban-list=/path/to/your/ban_list.txt
```
**Docker Users**: Ensure the `ban-list` path in your `.conf` file points to the location **inside** the container, while the `TARGET_FILE` in the script points to the location on your **host** machine.

**3. Automate with Cron**
To run this update automatically (e.g., every day at midnight), add a cronjob:
* Open crontab: `sudo crontab -e`
* Add this line:
```bash
0 0 * * * /usr/local/bin/update-monero-banlist.sh > /dev/null 2>&1
```
#### Troubleshooting
* **Permissions**: The script should be run as `root` (via crontab) to ensure it has permission to restart services or containers.
* **Paths**: Double-check that `TARGET_FILE` matches the location your Monero daemon actually reads.
* **First Run**: On the first run, the script will always perform a restart because it needs to create the initial `ban_list.txt` file.
