# 🛠️ Setup Guide: Build Your Personal VPN Client Network

> **Goal**: Route all devices connected to your EDUP AX3000’s Wi-Fi through an OpenVPN tunnel — with automatic fail-safe (kill switch), no DNS leaks, and dual-band support.

This guide assumes:
- Your **ISP Router (Huawei HG8245H5)** is already working and handing out IPs in `192.168.1.0/24`.
  - Connect to the ISP router network. (Just type `192.168.1.1` in your browser URL bar).
  - Log in to the ISP router using the administrator credentials. (See the backside of the ISP Router).
  - Go to the DHCP server settings. (**Advanced Setting → WAN → DHCP Server**).
  - Verify the DHCP server settings are set up for the `192.168.1.1/24` network.   
- You've connected the ISP's router LAN port and the EDUP router WAN port using an RJ45 network cable. (Make sure that both routers are turned on).
- You’ve **reserved a static IP `192.168.1.2`** for your EDUP AX3000 on the SLT router (via MAC address binding).
  - In the ISP router settings, go to the **Advanced Settings → WAN → Static IP**
  - Add a new entry and bind the IP with the AX3000 MAC, then **save**.
  - Remove and replug the network cable to the ISP router.
- Your **EDUP AX3000** is freshly flashed with **OpenWrt 21.02-SNAPSHOT** (with LuCI web UI).
- You have a valid **OpenVPN configuration file** (`.ovpn`) from your provider.

> ✅ All steps use the **LuCI web interface** (accessible at `http://192.168.1.1` initially).

---

## 🔌 Step 1: Initial Login & Set Password

1. Connect your computer to the **EDUP AX3000**:
   - Via **Wi-Fi** (default SSID is often `OpenWrt`)
   - Or via **Ethernet** to any LAN port
2. Open a web browser and go to:
