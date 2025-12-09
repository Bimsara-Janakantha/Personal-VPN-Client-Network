# 🔧 Hardware Used

This project uses two main devices: an **ISP-provided Huawei ONT/router** and a **EDUP AX3000 Wi-Fi 6 router flashed with OpenWrt**. Below are the exact models, specifications, and rationale for their selection.

---

## 1. ISP Gateway: Huawei HG8245H5

- **Type**: Fiber ONT + Wi-Fi Router (provided by ISP)
- **Brand**: Huawei
- **Model**: HG8245H5
- **Firmware**: Proprietary (managed by ISP)
- **Network Role**:  
  - Fiber termination (GPON)  
  - DHCP server on `192.168.1.0/24`  
  - Primary Wi-Fi (2.4 GHz only, non-VPN network)

### Why Keep It?
- ISP does **not allow bridge mode** for most residential customers.
- Acts as a stable, maintenance-free upstream gateway.
- Allows **dual-network setup**:  
  - `192.168.1.x` → direct internet (for printers, guests, smart TVs that don’t need VPN)  
  - `192.168.2.x` → full VPN (via EDUP)

> ℹ️ No configuration changes were made to the ISP device beyond reserving a static IP (`192.168.1.2` or any free IP from the DHCP server) for the EDUP router.

---

## 2. OpenWrt VPN Gateway: EDUP AX3000

- **Brand**: EDUP
- **Model**: AX3000 (also sold as **EP-AX3000** or **EDUP Wi-Fi 6 Router**)
- **Chipset**: MediaTek MT7981 (MT7981BA + MT7976CN + MT7531AE)
- **RAM**: DDR3 256MB RAM
- **Flash Storage**: 128 MB (plenty of space for OpenWrt + packages)
- **Wi-Fi**:  
  - 2.4 GHz: 574 Mbps
  - 5 GHz: 2402 Mbps
  - Wi-Fi 6 (802.168.11ax) support
- **Ports**: 1× Gigabit WAN, 4 × Gigabit LAN
- **OpenWrt Support**: ✅ Fully supported in [OpenWrt 21.02+](https://openwrt.org/releases/21.02/start)

### Purchase Links
- [AliExpress – EDUP AX3000 (Global Version) Link 1](https://www.aliexpress.com/item/1005008583866110.html?spm=a2g0o.order_list.order_list_main.5.58881802j38tGS)
- [AliExpress – EDUP AX3000 (Global Version) Link 2](https://www.aliexpress.com/item/1005010433588357.html?spm=a2g0o.detail.pcDetailTopMoreOtherSeller.4.2c8f25bc8InWiN&gps-id=pcDetailTopMoreOtherSeller&scm=1007.40050.354490.0&scm_id=1007.40050.354490.0&scm-url=1007.40050.354490.0&pvid=cc83e3eb-91ca-4d11-acaf-2035f99f41a6&_t=gps-id:pcDetailTopMoreOtherSeller,scm-url:1007.40050.354490.0,pvid:cc83e3eb-91ca-4d11-acaf-2035f99f41a6,tpp_buckets:668%232846%238114%231999&pdp_ext_f=%7B%22order%22%3A%22-1%22%2C%22eval%22%3A%221%22%2C%22sceneId%22%3A%2230050%22%2C%22fromPage%22%3A%22recommend%22%7D&utparam-url=scene%3ApcDetailTopMoreOtherSeller%7Cquery_from%3A%7Cx_object_id%3A1005010433588357%7C_p_origin_prod%3A) *(similar sellers)*
- [AliExpress – EDUP AX3000 (Global Version) Link 3](https://www.aliexpress.com/item/1005010089099843.html?spm=a2g0o.detail.pcDetailTopMoreOtherSeller.2.166bapvBapvByc&gps-id=pcDetailTopMoreOtherSeller&scm=1007.40050.354490.0&scm_id=1007.40050.354490.0&scm-url=1007.40050.354490.0&pvid=293d7a7f-0a56-456b-b438-878cb0f79211&_t=gps-id:pcDetailTopMoreOtherSeller,scm-url:1007.40050.354490.0,pvid:293d7a7f-0a56-456b-b438-878cb0f79211,tpp_buckets:668%232846%238114%231999&pdp_ext_f=%7B%22order%22%3A%22297%22%2C%22eval%22%3A%221%22%2C%22orig_sl_item_id%22%3A%221005010089099843%22%2C%22orig_item_id%22%3A%221005008583866110%22%2C%22sceneId%22%3A%2230050%22%2C%22fromPage%22%3A%22recommend%22%7D&utparam-url=scene%3ApcDetailTopMoreOtherSeller%7Cquery_from%3A%7Cx_object_id%3A1005010089099843%7C_p_origin_prod%3A1005008583866110) *(similar sellers)*
- [OpenWrt Hardware Database Entry](https://openwrt.org](https://openwrt.org/releases/21.02/start )

### Why This Router?
- **Ample RAM & flash**: Unlike many budget routers (e.g., 64 MB flash), it can run OpenVPN + LuCI without storage issues.
- **Hardware NAT offload**: Improves throughput when not using VPN.
- **Wi-Fi 6 support**: Future-proof for client devices.
- **Active OpenWrt community**: Stable, well-maintained support.
- **Cost-effective**: ~$50–60 USD with strong performance.

> 💡 **Note**: This is **not** the same as the smaller "EDUP AX1800" or older models. Ensure you have the **AX3000** version with **128 MB flash**.

---

## 🔌 Physical Connection Diagram
