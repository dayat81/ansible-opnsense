# Manual Virtual IP Configuration Guide

Since the API approach is having issues, here's the step-by-step manual configuration:

## 1. Configure Virtual IP

1. **Open Web Browser and Navigate to:**
   ```
   https://192.168.18.47
   ```

2. **Login with:**
   - Username: `root`
   - Password: `opnsense`

3. **Navigate to Virtual IP Settings:**
   ```
   Interfaces → Virtual IPs → Settings
   ```

4. **Add New Virtual IP:**
   - Click the **"+"** button
   - Configure the following:
     - **Mode**: `IP Alias`
     - **Interface**: `WAN` (select from dropdown)
     - **Address**: `192.168.18.49`
     - **Network**: `/24` (or select 24 from dropdown)
     - **Description**: `Virtual IP for WAN services`
     - **Bind**: ✅ (checked)
     - **Expand**: ✅ (checked)
   - Click **Save**

## 2. Add Firewall Rule

1. **Navigate to Firewall Rules:**
   ```
   Firewall → Rules → WAN
   ```

2. **Add New Rule:**
   - Click **"+"** button at the top
   - Configure:
     - **Action**: `Pass`
     - **Quick**: ✅ (checked)
     - **Interface**: `WAN`
     - **Direction**: `in`
     - **TCP/IP Version**: `IPv4`
     - **Protocol**: `any`
     - **Source**: `any`
     - **Destination**: 
       - Type: `Single host or alias`
       - Address: `192.168.18.49`
     - **Description**: `Allow traffic to Virtual IP 192.168.18.49`
     - **Log**: ✅ (optional, for troubleshooting)
   - Click **Save**

## 3. Apply Changes

1. **Apply Configuration:**
   - You should see an orange notification bar at the top
   - Click **"Apply changes"** to activate the configuration

## 4. Verification Commands

After configuration, run these commands to verify:

```bash
# Test from your machine
ping -c 4 192.168.18.49

# Check if it's reachable
curl -k https://192.168.18.49 --connect-timeout 5

# Or use the Ansible verification playbook
ansible-playbook verify_virtual_ip.yml
```

## 5. Troubleshooting

If the virtual IP still doesn't work:

1. **Check Interface Status:**
   - Go to `Interfaces → Overview`
   - Verify WAN interface is up and has the correct IP range

2. **Check Virtual IP Status:**
   - Go to `Interfaces → Virtual IPs → Settings`
   - Verify the VIP shows as active

3. **Check Firewall Logs:**
   - Go to `System → Log Files → Firewall`
   - Look for blocked traffic to 192.168.18.49

4. **Check System Logs:**
   - Go to `System → Log Files → General`
   - Look for interface or VIP configuration errors