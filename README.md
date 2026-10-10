# Hackintosh EFI Configuration Report - Lenovo ThinkPad 21DDS88B00

**Target OS:** macOS 15 Sequoia
**OpenCore Version:** Latest (Generated via OpCore-Simplify)
**SMBIOS:** MacBookPro16,2

---

## Hardware Configuration
- **CPU:** Intel Core i7-12800H (12th Gen - Alder Lake)
- **iGPU:** Intel Iris Xe (Completely disabled in BIOS)
- **dGPU:** NVIDIA RTX A1000 Laptop GPU (Active via Hardware MUX Switch)
- **Wi-Fi/BT:** Intel Wi-Fi 6E AX211 160MHz

---

## What Works
1. **macOS Boot:** Successfully boots into macOS 15 Sequoia.
2. **Hardware Acceleration (Metal API):** Unlike 99% of Hackintosh laptops with modern NVIDIA GPUs, this machine successfully utilizes the discrete NVIDIA card using the experimental NullMoth driver! The macOS UI is smooth and hardware-accelerated thanks to these critical hardware/NVRAM tweaks:
   - Enabled `ResizeGpuBars` = 13.
   - Enabled `AllowRelocationBlock`.
   - Disabled SIP (System Integrity Protection).
   - Blocked the native fallback framebuffer (`IONDRVSupport`).
3. **Wi-Fi:** Works stably via `itlwm.kext` (Currently hardcoded to `TP-Link_57B2_5G`). To automatically scan and connect to other networks, the **HeliPort** app is strictly required.
4. **Keyboard & Touchpad:** Fully working via VoodooPS2 and VoodooI2C.
5. **NVMe & Audio:** Basic functionality works.

---

## How to Connect to Wi-Fi (HeliPort)
Because Apple completely removed the legacy Wi-Fi framework in macOS 15 Sequoia, the native Wi-Fi menu cannot be used safely. Instead, this EFI uses the highly stable `itlwm.kext` which requires a 3rd-party app to manage connections.

**Instructions:**
1. Open Safari and download **HeliPort** from its official GitHub releases page (OpenIntelWireless).
2. Drag the downloaded `HeliPort.app` into your `Applications` folder.
3. Open HeliPort. A new Wi-Fi icon will appear in your top-right menu bar.
4. Click this icon to view available networks, enter passwords, and connect to Wi-Fi exactly like the native Apple menu!
*(Tip: Go to HeliPort Preferences and check "Launch at login" so it starts automatically every time you boot).*

---

## Known Limitations & Issues

Because your hardware combination (Intel 12th Gen + NVIDIA RTX) is considered extremely hostile for macOS, the following issues are unavoidable and currently cannot be completely fixed:

1. **High Temperatures & Battery Drain (Power Management Issue):**
   - **NVIDIA GPU:** The NullMoth driver is highly experimental and currently lacks Power Management (P-States). The GPU constantly runs at maximum clock speeds (100% capacity) and does not downclock when idle.
   - **Intel CPU:** 12th Gen chips are completely unsupported by Apple, meaning macOS cannot optimally schedule P-Cores and E-Cores.
   - *Workaround:* Install `Macs Fan Control` to manually increase fan speeds, and inject a custom `CPUFriendDataProvider` to force a low-power CPU profile.

2. **Display Glitches (No EDID & No Brightness Control):**
   - The NullMoth driver fails to read the physical display properties (EDID) of the Lenovo panel. As a result, macOS disables HiDPI (Retina Scaling) and removes the native Brightness slider.
   - *Workaround:* Install `BetterDisplay` to create a Dummy Display (which forces HiDPI scaling) and use its Software Dimming feature to control brightness.

3. **Sleep/Wake is Broken:**
   - Attempting to put the laptop to sleep will almost certainly result in a Kernel Panic or a black screen upon waking up. It is highly recommended to Shut Down the laptop instead of closing the lid.

4. **Locked to Discrete-Only Mode:**
   - Everything is configured to only work while the internal iGPU is disabled in the BIOS. If you ever switch the BIOS back to Hybrid Mode / Optimus, the laptop will instantly hang at `EXITBS:START` during boot due to architectural changes.

5. **Do NOT Update macOS:**
   - The NullMoth driver relies on heavy, undocumented kernel-level modifications for macOS 15. If Apple releases a minor update (e.g., 15.1, 15.2), installing it might instantly break the driver and leave you with a black screen.

---

**Conclusion:** Getting full graphics acceleration on this specific hardware configuration is an incredible technical achievement! However, due to the severe power management and thermal limitations, it is highly recommended to continue using Windows for heavy workloads and reserve macOS for light tasks or experimentation.
