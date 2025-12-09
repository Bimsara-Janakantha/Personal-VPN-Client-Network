# 🛠️ Setup Guide: Build Your Personal VPN Client Network

> **Goal**: Route all devices connected to your EDUP AX3000’s Wi-Fi through an OpenVPN tunnel — with automatic fail-safe (kill switch), no DNS leaks, and dual-band support.

This guide assumes (If not [Hardware Guide](https://github.com/Bimsara-Janakantha/Personal-VPN-Client-Network/blob/main/docs/HARDWARE_GUIDE.md)):
- Your **ISP Router (Huawei HG8245H5)** is already working and handing out IPs in `192.168.1.0/24`.
- You've connected the ISP's router LAN port and the EDUP router WAN port using an RJ45 network cable. (Make sure that both routers are turned on).
- You’ve **reserved a static IP `192.168.1.2`** for your EDUP AX3000 on the SLT router (via MAC address binding).
- Your **EDUP AX3000** is freshly flashed with **OpenWrt 21.02-SNAPSHOT** (with LuCI web UI).
- You have a valid **OpenVPN configuration file** (`.ovpn`) from your provider.

> ✅ All steps use the **LuCI web interface** (accessible at `http://192.168.1.1` initially).

---

## 🔌 Step 1: Initial Login & Set Password

1. Connect your computer to the **EDUP AX3000**:
   - Via **Wi-Fi** (default SSID is often `OpenWrt`)
   - Or via **Ethernet** to any LAN port
2. Open a web browser and go to: `http://192.168.1.1`
3. You’ll see a login screen.
  - If **no password is set (default)**, click **Login** with an empty password.
  - You’ll be prompted to **set a root password** (If not, go to System → Administration → Router Password).
4. Enter a **strong password** → click **Login** again.

> 🔐 This password is used for both LuCI and SSH. Keep it safe!

---

## 🌐 Step 3: General Settings (Optional)

Since the router is in default settings, it may differ from your timezone and region.

1. In LuCI, go to **System → System → General Settings**
   <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ba4e2802-f420-4e4b-ad70-4c861f7cbac6" />
2. Change **Hostname** as you wish (e.g. EDUP)
3. Set Timezone (e.g. Asia/Colombo)
4. Click **Save**
5. Then go to the **Time Synchronization** tab.
  <img width="1920" height="1084" alt="image" src="https://github.com/user-attachments/assets/5b82c2ce-f9a2-45d4-9a3f-e1dd981e2baf" />
6. Remove all NTP servers and add NTP servers for your region. (e.g. `time.cloudflare.com`, `pool.ntp.org`, `lk.pool.ntp.org`).
7. Keep other settings as they are and **Save**.
8. Then **Save & Apply**

---

## 🌐 Step 2: Change LAN IP (Avoid Conflict with SLT)

Since SLT uses `192.168.1.0/24`, we’ll move OpenWrt to `192.168.2.0/24`.

1. In LuCI, go to **Network → Interfaces**
2. Click **LAN → Edit**
3. Under **General Setup**:
- **IPv4 address**: `192.168.2.1`
- **IPv4 netmask**: `255.255.255.0`
4. Under **DHCP Server → General Setup**:
- **Start**: `100`
- **Limit**: `150` → gives IPs from `192.168.2.100` to `192.168.2.249`
5. Click **Save & Apply**

> ⚠️ Your browser will disconnect. **Reconnect at**:
> ```
> http://192.168.2.1
> ```

---

## 🔌 Step 3: Configure WAN (Connect to SLT Fiber One)

1. Connect a **LAN port on SLT Fiber One** → to the **WAN port** on EDUP AX3000.
2. In LuCI: **Network → Interfaces → WAN → Edit**
3. Set:
- **Protocol**: `DHCP client`
- **Firewall settings**: Zone = `wan`
4. Click **Save & Apply**

✅ **Verify**:
- Go to **Status → Overview**
- Under **WAN**, you should see IP: `192.168.1.2`
- Under **Network**, ping `8.8.8.8` → should succeed

> ❌ If no IP appears:
> - Reboot EDUP
> - Double-check static lease on SLT (is MAC address correct?)

---

## 📶 Step 4: Set Up Dual-Band Wi-Fi (Your Private VPN Network)

We’ll create two secure Wi-Fi networks — both on the new `192.168.2.0/24` subnet.

### A. Configure 2.4 GHz Wi-Fi
1. Go to **Network → Wireless**
2. Click **Edit** next to **2.4 GHz (radio0)**
3. Under **Wireless Network**:
- **Mode**: `Access Point`
- **SSID**: `Personal-VPN-2.4` (or your preferred name)
- **Network**: `lan`
4. Under **Wireless Security**:
- **Encryption**: `WPA2-PSK`
- **Key**: `YourStrongPassword123!`
5. Click **Save**

### B. Configure 5 GHz Wi-Fi
1. Click **Edit** next to **5 GHz (radio1)**
2. Same settings:
- **SSID**: `Personal-VPN-5`
- **Mode**: `Access Point`
- **Network**: `lan`
- **WPA2-PSK** + strong password
3. Click **Save**

✅ Now connect a phone to `Personal-VPN-5` — it should get an IP like `192.168.2.101`.

---

## 📦 Step 5: Install OpenVPN and LuCI App

1. Go to **System → Software**
2. Click **Update lists** (top right)
3. In the **Download and install package** box, type:
