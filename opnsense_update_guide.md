# OPNSense Update Guide for Ansible Compatibility

Based on the repository documentation, the `ansibleguy.opnsense` collection supports the **latest version of OPNSense**. Your current version appears to be from 2024, which may have API compatibility issues.

## Method 1: Web Interface Update (Recommended)

### 1. Access OPNSense Web Interface
```
https://192.168.18.47
Login: root/opnsense
```

### 2. Check Current Version
- Go to **System → Firmware → Status**
- Note the current version and available updates

### 3. Update OPNSense
1. **Navigate to System → Firmware → Updates**
2. **Check for updates:**
   - Click **"Check for updates"**
   - Review available updates
3. **Install updates:**
   - Click **"Download and install updates"**
   - Wait for the update process to complete
   - The system will reboot automatically

### 4. Verify Update
- After reboot, go to **System → Firmware → Status**
- Confirm you're running the latest version

## Method 2: SSH/Console Update

If you have SSH access:

```bash
# SSH to OPNSense
ssh root@192.168.18.47

# Check current version
opnsense-version

# Update firmware
opnsense-update

# Reboot if required
reboot
```

## Method 3: Force API Compatibility

If updating isn't possible, try enabling additional API features:

### 1. Enable API Access
- Go to **System → Settings → Administration**
- Under **Web GUI:**
  - ✅ Enable "API"
  - ✅ Enable "CORS" (if needed)
- Click **Save**

### 2. Check Available Plugins
- Go to **System → Firmware → Plugins**
- Look for and install:
  - `os-api-backup` (if available)
  - Any interface management plugins

### 3. Verify API Endpoints
After update, test API access:

```bash
# Test basic API connectivity
curl -k -u "YOUR_API_KEY:" "https://192.168.18.47/api/core/firmware/status"

# Test VIP API endpoint
curl -k -u "YOUR_API_KEY:" "https://192.168.18.47/api/interfaces/overview"
```

## Post-Update Steps

### 1. Verify Ansible Collection Compatibility
```bash
# Test the VIP module after update
ansible-playbook setup_api_and_vip.yml

# Run verification
ansible-playbook verify_virtual_ip.yml
```

### 2. Generate New API Credentials
After updating, you may need to regenerate API credentials:

1. **Go to System → Access → Users**
2. **Edit root user**
3. **Generate new API key** in the "API keys" section
4. **Update credentials file:**
```bash
cat > opnsense_api_credentials.txt << EOF
key=NEW_API_KEY_HERE
secret=
url=https://192.168.18.47
ssl_verify=false
EOF
```

## Expected API Endpoints After Update

A properly updated OPNSense should support these VIP API endpoints:
- `/api/interfaces/vip_settings/searchItem`
- `/api/interfaces/vip_settings/addItem`
- `/api/interfaces/vip_settings/setItem/{uuid}`
- `/api/interfaces/vip_settings/delItem/{uuid}`
- `/api/interfaces/vip_settings/reconfigure`

## Troubleshooting

### If Update Fails:
1. **Check disk space:** System → Information → System
2. **Clear cache:** System → Firmware → Settings → Clear cache
3. **Try different mirror:** System → Firmware → Settings → Change mirror

### If API Still Doesn't Work:
1. **Check firewall version compatibility**
2. **Verify API is enabled in settings**
3. **Try manual VIP configuration via web interface**
4. **Consider fresh OPNSense installation if version is very old**

## Version Recommendations

For best compatibility with this Ansible collection:
- **Minimum:** OPNSense 23.x or later
- **Recommended:** OPNSense 24.x or latest stable
- **Development:** OPNSense 25.x (development branch)

Run the update and then test the virtual IP configuration again!