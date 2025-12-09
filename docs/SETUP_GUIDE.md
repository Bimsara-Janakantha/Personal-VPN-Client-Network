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

## ✅ Step 2: General Settings (Optional)

Since the router is in default settings, it may differ from your timezone and region.

1. In LuCI, go to **System → System → General Settings**
   <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ba4e2802-f420-4e4b-ad70-4c861f7cbac6" />
2. Change **Hostname** as you wish (e.g. EDUP)
3. Set Timezone (e.g. Asia/Colombo)
4. Click **Save**
5. Then go to the **Time Synchronization** tab.
   <img width="1920" height="1084" alt="image" src="https://github.com/user-attachments/assets/5b82c2ce-f9a2-45d4-9a3f-e1dd981e2baf" />
6. Remove all NTP servers and add NTP servers for your region. (e.g. `time.cloudflare.com`, `pool.ntp.org`, `lk.pool.ntp.org`)
7. Keep other settings as they are and **Save**
8. Then **Save & Apply**

---

## 🔌 Step 3: Configure WAN (Connect to ISP Router)

1. Connect a **LAN port on ISP Router** → to the **WAN port** on EDUP AX3000.
2. In LuCI: **Network → Interfaces → WAN → Edit**
3. Set:
   - **Protocol**: `DHCP client`
   - **Firewall settings**: Zone = `wan`
4. Click **Save & Apply**
<img width="1770" height="931" alt="image" src="https://github.com/user-attachments/assets/4e3d8d62-a197-43cf-a28f-28fd8b5dfcbc" />
<img width="1752" height="441" alt="image" src="https://github.com/user-attachments/assets/4a92c0c7-f980-4868-9c4b-8a590d99885f" />

✅ **Verify**:
- Go to **Status → Overview**
- Under **Network → IPv4 Upstream**, you should see IP: `192.168.1.2/24` as the address

> ❌ If no IP appears:
> - Reboot EDUP AX3000 (**System → Reboot** or **OFF and ON the EDUP router**)
> - Double-check static lease on ISP router (is MAC address correct?)

---

## 🌐 Step 4: Change LAN IP (Avoid Conflict with ISP Router)

Since Huawei uses `192.168.1.0/24`, we’ll move OpenWrt to `192.168.2.0/24`.

### A: Add the new IPv4 (DHCP) Range
1. In LuCI, go to **Network → Interfaces**
2. Click **LAN → Edit**
3. Under **General Settings**
4. Add the ➕ button near **IPv4 address**
5. Add a new **IPv4 address**: `192.168.2.1/24`
6. Keep other settings as they are
7. Click **Save**
8. Then click **Save & Apply**
9. After reconfiguring the router, use the new address `http://192.168.2.1` to access the LuCI.

### B: Remove the old IPv4 (DHCP) Range
1. In LuCI, go to **Network → Interfaces**
2. Click **LAN → Edit**
3. Under **General Settings**
4. Remove the old **IPv4 address** by clicking ❌ button nect to the `192.168.1.1/24` address
5. Keep other settings as they are
6. Click **Save**
7. Then click **Save & Apply**
8. Wait until the configuration is done.
9. After reconfiguring the router, LuCI can be accessed only from the new address `http://192.168.2.1`.
   
> ⚠️ Your browser will disconnect. **Reconnect at**:
> ```
> http://192.168.2.1
> ```

---

## 📶 Step 5: Set Up Dual-Band Wi-Fi

We’ll create two secure Wi-Fi networks — both on the new `192.168.2.0/24` subnet.

### A. Configure 2.4 GHz Wi-Fi
1. Go to **Network → Wireless**
2. Click **Edit** next to **SSID: Openwrt_2.4G**
3. Under **General Setup → Interface Configuration → General Setup**:
   - **Mode**: `Access Point`
   - **SSID**: `Personal-VPN-2.4` (or your preferred name)
   - **Network**: `lan`
   <img width="1212" height="537" alt="image" src="https://github.com/user-attachments/assets/3121214f-52cb-478e-a384-e56c9ff1d31f" />

4. Under **Wireless Security**:
   - **Encryption**: `WPA2-PSK/WPA3-SAE Mixed Mode`
   - **Key**: `YourStrongPassword123!`
   <img width="600" height="220" alt="image" src="https://github.com/user-attachments/assets/d6b5c081-9d25-4e7c-828c-de489e49b79c" />

5. Click **Save**
6. Click **Save & Apply**

### B. Configure 5 GHz Wi-Fi
1. Click **Edit** next to **SSID: Openwrt_5G**
2. Same settings:
   - **SSID**: `Personal-VPN-5`
   - **Mode**: `Access Point`
   - **Network**: `lan`
   - **WPA2-PSK/WPA3-SAE Mixed Mode** + strong password
3. Click **Save**
4. Click **Save & Apply**

✅ Now connect a phone to `Personal-VPN-5` — it should get an IP like `192.168.2.101`.

---

## 📦 Step 6: Install OpenVPN and LuCI App

1. Go to **System → Software**
2. Click **Update lists** (top right)
3. In the **Filter** box, type:
   - **openvpn-openssl** → install
   - **luci-app-openvpn** → install
4. Restart your router.
   <img width="875" height="723" alt="image" src="https://github.com/user-attachments/assets/81f754bb-47ec-4e9c-b109-5499b026e6aa" />
   > Most of the time, `luci-app-openvpn` was pre-installed with the system.
   
   > If you receive an error while attempting to install the `openvpn-openssl` package, check the `Overwrite files from other package(s)` checkbox

> ⏳ Wait 30–60 seconds. You may see a screen about dependencies — **check “Overwrite files...”** if prompted, then click **Install**.

✅ After install, refresh the page. You should now see **VPN → OpenVPN** in the menu.

---

## 🔐 Step 7: Add Your OpenVPN Configuration

1. Download and extract the VPN configuration files to your computer.  
   Be sure to choose the **IP address** option in the configuration generator.

2. On your router, navigate to **VPN → OpenVPN**.

3. Under the **OVPN configuration file upload** section, click **Browse** and select the `.ovpn` file for the VPN server you want to connect to.  
   Give it any name (e.g., `OVPN`), then click **Upload**.

4. Click **Edit** next to the created OpenVPN instance.  
   Enter your IVPN account ID (e.g., `ivpnXXXXXXXX` or `i-XXXX-XXXX-XXXX`, case-sensitive) and **any password** (e.g., `ivpn`) in **two separate lines** in the text box at the bottom.

5. Append the credentials file path to the `auth-user-pass` line in the first text box.  
   The full path is displayed above the second text box.  
   Example:  `auth-user-pass /etc/openvpn/Austria.auth`

6. Click **Save**.

<img width="866" height="598" alt="image" src="https://github.com/user-attachments/assets/f6f06bbe-84dd-4b4c-910e-abd89884e471" />

> **Note:** Steps 4–6 may vary depending on your OpenVPN provider.  
> For some providers, these steps may not be required.

7. Return to the main **OpenVPN** section, check the **Enabled** checkbox, and click **Save & Apply**.


---

## 🌐 Step 8: Create the `VPN` Interface for `tun0`

Even if `tun0` doesn’t exist yet, we’ll create a placeholder.

1. Go to **Network → Interfaces → Add new interface**
2. Fill in:
- **Name**: `VPN`
- **Protocol**: `Unmanaged`
- **Interface**: type exactly → `tun0` then press Enter key.

<img width="817" height="217" alt="image" src="https://github.com/user-attachments/assets/9490b99f-d21b-4d45-9390-e53ec0c0e386" />


3. Click **Create Interface**
4. In the interface properties window, ensure that Bring up on boot is checked, then click **Save & Save & Apply** buttons.

---

## 🔥 Step 9: Configure Firewall Zones (Critical!)

We’ll ensure **only VPN traffic leaves your network**.
Navigate to **Network → Firewall**

### A. Edit the `vpn` Zone
1. Click the **Add** button and enter the following configuration:
   - **Name**: Give it any name (e.g. ovpn_fw)
   - **Input**: `reject`
   - **Output**: `accept`
   - **Forward**: `reject`
   - ✔️ **Masquerading**
   - ✔️ **MSS clamping**
   - **Covered networks**: select the previously created VPN tunnel interface (e.g. VPN)
   - **Allow forward to destination zones**: `Unspecified`
   - **Allow forward from source zones**: `lan`

  <img width="813" height="685" alt="image" src="https://github.com/user-attachments/assets/67093cce-da10-4141-b000-7c31d35e821c" />

2. Click **Save** and **Save & Apply**

### B. Edit the `lan` Zone (Configure a Kill-switch)
To ensure the traffic on your LAN devices travels strictly via the VPN tunnel and to prevent any possible leaks if the router disconnects from the VPN server for any reason.

1. Go to **Network → Firewall**
2. Find **lan** → click **Edit**
3. Set: **Allow forward to destination zones**: **ONLY `vpn`** (remove `wan`!)
4. Click **Save** and **Save & Apply**

   <img width="813" height="685" alt="image" src="https://github.com/user-attachments/assets/cac9078b-052a-4b47-98c9-5ac376c4cb20" />


> 🔒 This creates a **kill switch**: if OpenVPN stops, LAN traffic has nowhere to go.

---

## ▶️ Step 10: Start OpenVPN & Verify

1. Go to **Network → Interfaces**
2. You can see the incoming and outgoing traffic through the VPN interface.
3. Open another tab in your web browser and visit the [whatismyipaddress.com](https://whatismyipaddress.com/).
4. You can see your VPN provider's IP rather than the ISP's

✅ **Success!** Your Personal VPN Client Network is live.

---

## 🧪 Step 11: Test the Kill Switch

1. In LuCI: **Services → OpenVPN → Stop**
2. On your phone (still on `Personal-VPN-5`):
- Try loading a website → should **fail**
- [whatismyipaddress.com](https://whatismyipaddress.com/) → should time out

✅ If internet dies → your kill switch works.  
✅ If it still works → double-check **Step 8B** (LAN must NOT forward to `wan`).

---

## 🔄 Step 12: Reboot & Final Validation

1. Go to **System → Reboot → Perform reboot**
2. Wait 60 seconds
3. After reboot:
- OpenVPN should auto-start
- Connect to `Personal-VPN-2.4` → verify via [whatismyipaddress.com](https://whatismyipaddress.com/)
- Confirm public IP is the **VPN IP**

---

## 🎉 Done!

You now have:
- A **private, encrypted Wi-Fi network** for all your devices
- **Automatic protection** — no per-device setup
- **Fail-safe privacy** — no leaks if VPN drops

Enjoy your **Personal VPN Client Network**! 🔐

---

> 💡 **Need help?**  
> - Check logs: **Status → System Log**  
> - Open an Issue in this repo with your OpenWrt version and provider name
