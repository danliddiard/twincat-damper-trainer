# Light Switch Project: Moving to the CP6606 Panel (TwinCAT 4024)

**Date:** Wed Oct 28, 2026
**Course:** TECE 1420 Beckhoff / TwinCAT + Git independent study
**Status:** **WORKING.** Button 1 on the EL1008 turns on the light on EL2008 channel 1, with the program running on the panel's own runtime.

---

## Bottom Line

The project was moved from the laptop runtime (TwinCAT 4026.27) to the CP6606 panel (TwinCAT 4024.79, Windows CE 7). Software issues (version, platform, license, clock, links) took a couple of hours to work through. The single biggest time sink was the EtherCAT I/O not communicating, which turned out to be a **bad Ethernet cable** between the panel's EtherCAT port and the EK1100, plus a short detour where the cable was plugged into the **EK1100's X2 (OUT) port instead of X1 (IN)**.

**Rough timeline:**

| Time (MT) | Phase |
|---|---|
| ~10:24 | ADS route to the panel |
| ~12:00 to 12:35 | Version switch to 4024.79, Git branch, platform fix |
| ~12:35 to 13:30 | Build, I/O link, license, panel clock |
| ~13:30 to 15:00 | **EtherCAT not communicating (about 1.5 hours, ended with bad cable)** |
| ~15:00 to 15:21 | Rescan, relink, activate, download, working |

---

## Hardware and Network

| Item | Detail |
|---|---|
| Panel | Beckhoff CP6606-0001-0020, ARM Cortex-A8 1 GHz, mainboard CB3011-0002, 7" touch, built 12/2016 |
| Panel OS / runtime | Windows CE 7, TwinCAT 3.1.4024 |
| Hostname / AMS NetId | CP-2AE6A6 / 5.42.230.166.1.1 |
| LAN1 (X102) | MAC 00:01:05:2A:E6:A6, IP 192.168.1.50. Green cable to laptop (ADS / programming). Shows in TwinCAT as **FEC1**. |
| LAN2 (X103, labeled EtherCAT) | MAC 00:01:05:2A:E6:A7. Cable to EK1100 **X1 (IN)**. Shows in TwinCAT as **CCAT DPRAM Slot 0**. |
| I/O | Term 1 EK1100, Term 2 EL1008, Term 3 EL2008, Term 4 EL7342, Term 5 EL9011 end cap |
| Laptop | Dell, one Ethernet port (to panel LAN1). XAE 4026.27 with 4024.79 Remote Manager installed |
| Git repo | C:\TwinCAT-Projects\Light Switch Project, branch `Target-4024` (`main` keeps the 4026 version) |

**Program:** `Light := Switch;` with `MAIN.Switch AT %I* : BOOL` and `MAIN.LIGHT AT %Q* : BOOL`.

**Final I/O links:**
- `MAIN.Switch` to EL1008 channel 1 (IX 80.0), button 1
- `MAIN.LIGHT` to EL2008 channel 1

---

## Part 1: Software and Setup Issues (all resolved)

| # | Issue | Cause | Fix |
|---|---|---|---|
| 1 | ADS route security prompt (bidirectional vs unidirectional) | New 4026 route option | Kept **bidirectional**. The 4024 CE target predates unidirectional routes. |
| 2 | 4026 project, 4024 target | Windows CE tops out at 4024 | Installed 4024.79 support in the Package Manager. |
| 3 | "4024.79 cannot be loaded during this session" | Tried to switch versions with a project already open | Closed XAE, reopened with **no project loaded**, selected 4024.79 in the Remote Manager dropdown, then opened the solution. Accepted the "open with loaded version" prompt. |
| 4 | Opened the wrong copy of the project | OneDrive copy (Documents\TcXaeShell) is not in Git. Windows hides file extensions, so the .sln shows only as type "TcXaeShell Solution". | Reopened from C:\TwinCAT-Projects. Created branch `Target-4024`. |
| 5 | Wrong platform (TwinCAT OS ARMV7-A, then x86 on a project row) | Leftover from laptop/4026 setup | Added **TwinCAT CE7 (ARMV7)** via Configuration Manager > New. Accepted "Adjust platform" on activation. |
| 6 | `MAIN.LIGHT` link lost on build | Symbol re-import after version switch | Relinked (later redone after the rescan). |
| 7 | License violation: TC3 PLC / TC3 IO not found | No runtime license on panel | Generated 7-day trial license. |
| 8 | License status "issue time in the future" | **Panel clock was set to 2006** | Set panel date, time and time zone on the touchscreen, then **re-activated** so the license server re-read the clock. Licenses valid until about **Nov 5, 2026**. |

---

## Part 2: EtherCAT I/O Not Communicating (resolved: bad cable)

**Symptom:** EL1008 input LEDs 1 through 5 lit with the wired buttons (field wiring good), but `MAIN.Switch` stayed FALSE and nothing lit on the EL2008. Device 1 Online tab showed every terminal **INIT NO_COMM**.

### Everything Tried, In Order

| # | Adapter / action | Result | What it actually meant |
|---|---|---|---|
| 1 | Original setting (laptop Intel NIC, 192.168.1.77) | "Failed to connect to network adapter" | Leftover from when the project ran on the laptop |
| 2 | CCAT DPRAM Slot 0 | 0 frames sent | License was still broken at the time, so not a fair test |
| 3 | FEC1 | ~29,600 frames sent, nearly all lost | FEC1 = LAN1 (MAC ...E6:A6, IP 192.168.1.50) = **the laptop port**. Wrong port. |
| 4 | Back to CCAT (MAC ...E6:A7 confirmed = LAN2) | 0 frames sent, INIT NO_COMM | Correct port, but no response from the EK1100. **This was the bad cable.** |
| 5 | Scan Devices | Only found "EtherCAT Automation Protocol" on FEC1 | Rejected. Scan hides ports already in use, so CCAT didn't show. |
| 6 | Deleted Device 1, re-added on CCAT, Scan for boxes | No boxes found | Bad cable again |
| 7 | Address field 0xF0000000 vs list 0xF0010000 | Manual change reverted | Red herring. TwinCAT sets this itself for CCAT. |
| 8 | **Replaced the cable** | Still nothing at first | Cable had been plugged into **EK1100 X2 (OUT)** |
| 9 | **Moved cable to EK1100 X1 (IN)** | Scan found EK1100, EL1008, EL2008, EL7342, EL9011 | **Fixed** |

### Finishing Steps After the Cable Fix

1. Right-clicked **Device 1 (EtherCAT)** on CCAT > **Scan** > Yes to scan for boxes. All five terminals found.
2. Relinked the variables (the rescan wiped them):
   - `MAIN.Switch` > Change Link > Term 2 (EL1008) > Input, IX 80.0 (channel 1)
   - `MAIN.LIGHT` > Change Link > Term 3 (EL2008) > Channel 1 Output
3. **Activate Configuration** > Yes to restart in Run mode.
4. **PLC > Login** > download > **Start**.
5. Pressed button 1: Switch and LIGHT went TRUE, EL2008 channel 1 lit.

---

## Lessons Learned (why this took so long)

1. **Check the physical layer first when frames don't come back.** "Frames sent, all lost" and later "0 frames, no boxes found" both pointed at the link between the panel and the EK1100. A 2-minute cable swap or a look at the EK1100's X1 Link/Act LED would have saved most of the 1.5 hours spent on adapter settings and rescans.
2. **EK1100: X1 (top) is IN, X2 (bottom) is OUT.** The cable from the master must go into X1.
3. **Identify ports by MAC address, not by name.** The panel label lists MAC1 (LAN1) and MAC2 (LAN2). In TwinCAT, select an adapter and read the MAC on the Adapter tab. Here, FEC1 = LAN1 (laptop) and CCAT = LAN2 (EtherCAT).
4. **Never put EtherCAT on the port your laptop uses for ADS.** That's how the FEC1 attempt lost every frame.
5. **On an old CE panel, check the clock before generating a license.** A 2006 clock makes a fresh trial license look like it's from the future. Fix the clock, then re-activate.
6. **Pick the TwinCAT version before opening the project.** The Remote Manager can't switch versions once a solution is loaded.
7. **A version switch or rescan drops I/O links.** Plan to relink and check the Error List after either one.
8. **Scan only shows unused ports.** If the device you want is already assigned, it won't appear in the scan list.

---

## Still To Do

- [ ] **PLC > Activate Boot Project** so the program survives a power cycle
- [ ] Commit on `Target-4024` (message like `Retarget to 4024.79 / CE7 ARMV7 for CP-2AE6A6, I/O on CCAT`)
- [ ] Trial license expires about **Nov 5, 2026**. Regenerate via SYSTEM > License > 7 Days Trial License.
- [ ] Panel clock fell back to 2006, which usually means a **dead backup battery**. If it resets after a power loss, set the time before activating, and let the lab know.
- [ ] Mark the bad cable or throw it out so nobody else loses time on it.

