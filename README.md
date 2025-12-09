# 🔐 Personal VPN Client Network

> **A whole-home privacy solution: route all your devices through a trusted OpenVPN connection — configured entirely via OpenWrt’s LuCI web interface.**

This project shows how to transform an **EDUP AX3000** router running **OpenWrt 21.02** into a dedicated **Personal VPN Client Network**. Devices connected to its Wi-Fi automatically tunnel all traffic through your chosen OpenVPN provider — with leak protection, automatic fail-safe (kill switch), and seamless coexistence with your existing ISP router.

No per-device setup. No apps. Just **one private, encrypted Wi-Fi network** for everything.

> ✨ **Designed for beginners**: Core configuration uses **only the LuCI web UI** — no terminal commands required.

---

## 💡 Why a "Personal VPN Client Network"?

Most people install VPN apps on phones or laptops — but what about your **smart TV, gaming console, IoT lights, or children’s tablets**? These often can’t run VPN software, leaving them exposed to tracking, censorship, or ISP monitoring.

A **Personal VPN Client Network** flips the model:
- Instead of protecting *devices*, you protect the *network* they connect to.
- Any device that joins your secure Wi-Fi **inherits full VPN protection** — automatically.
- You maintain a separate, non-VPN network (e.g., on your ISP router) for devices that don’t need privacy (printers, guests, etc.).

This is **privacy by design**, built on open-source firmware you control — not a closed commercial service.

---

## 🎯 Key Features

- ✅ **Whole-network encryption**: All traffic from your private Wi-Fi → OpenVPN tunnel
- ✅ **Automatic kill switch**: Blocks internet if VPN drops — no leaks
- ✅ **Dual-band support**: 2.4 GHz & 5 GHz SSIDs (e.g., `Home-2.4-VPN`, `Home-5-VPN`)
- ✅ **Coexists with ISP router**: Works alongside with ISP router (no bridge mode needed)
- ✅ **DNS/WebRTC leak prevention**: Enforced at the router level
- ✅ **Persistent & boot-safe**: Starts automatically after power loss
- ✅ **LuCI-first setup**: Full configuration via web interface — ideal for beginners

---

## 📦 Hardware Setup

```Bash
[ Internet ]
     ↑
[ Huawei Router ] ← ISP router (192.168.1.0/24, DHCP)
     ↑ (LAN port → WAN port)
[ EDUP AX3000 ] ← OpenWrt VPN gateway (192.168.2.0/24)
     ├─ Wi-Fi 2.4G → "Personal-VPN-2.4"
     └─ Wi-Fi 5G → "Personal-VPN-5"
```

- **ISP Network**: `192.168.1.x` → direct internet (no VPN)
- **EDUP Network**: `192.168.2.x` → 100% VPN-enforced

---

## 🛠️ Software Used

- **OpenWrt 21.02.x** (with LuCI web UI)
- **OpenVPN** (`openvpn-openssl`)
- **luci-app-openvpn** (for GUI management)
- Your vpn provider’s `.ovpn` configuration file (client.ovpn)

> 💡 Works with most major providers: ProtonVPN, Mullvad, IVPN, PIA, etc.

---

## 📝 Setup Highlights (LuCI Web UI Only)

1. **Change EDUP LAN IP** to `192.168.2.1` (avoid conflict with ISP Router)
2. **Configure WAN** as DHCP client → gets `192.168.1.2` (static lease on ISP Router)
3. **Set up dual-band Wi-Fi** under new `192.168.2.0/24` network
4. **Install OpenVPN + LuCI app** via **System → Software**
5. **Upload your `.ovpn` config** in **VPN → OpenVPN**
6. **Create `VPN` interface** bound to `tun0`
7. **Configure firewall zones**:
   - `lan` → forward only to `vpn` (not `wan`)
   - `vpn` → masquerading enabled, forward to `wan` (for tunnel handshake)
8. **Verify** with [ipleak.net](https://ipleak.net)

> Full step-by-step guide included in this repo.

---

## 🧪 Validation

From any device on your **Personal VPN Wi-Fi**:
- ✅ Public IP = your **VPN provider’s server**
- ✅ ISP field ≠ your real ISP (e.g., not “ISP”)
- ✅ No DNS leaks
- ✅ Internet dies instantly if OpenVPN stops (kill switch active)

---

## ⚠️ Notes

- **Double NAT is intentional**: Keeps your ISP router functional while adding privacy.
- **IPv6 is disabled**: Prevents leak vectors (re-enable only if your VPN supports it).
- **Performance**: Expect 10–200 Mbps throughput on EDUP AX3000 (CPU-limited by software encryption).
- **Storage**: On 16MB-flash devices, prefer `openvpn-mbedtls` to save space.

---

## 📁 Repository Structure

```Bash
.
├── README.md # This overview
├── docs/
│ ├── HARDWARE_GUIDE.md # Complete Hardware walkthrough and product links
│ └── SETUP_GUIDE.md # Complete LuCI-based walkthrough
├── configs/
│ └── example.ovpn # Template (secrets redacted)
└── screenshots/ # LuCI UI reference (optional)
```

> 🔐 **Your network. Your rules. Your privacy.**  
> Build a Personal VPN Client Network — and take back control.
