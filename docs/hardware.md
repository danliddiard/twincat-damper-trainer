# Hardware

## Station PC
| Item | Value |
|---|---|
| Model | Dell OptiPlex 3070 Micro |
| Hostname | ROBO-PC19-PLC1 |
| Network chip | Realtek (not on Beckhoff's supported list for build 4026) |
| Card slot | None |
| TwinCAT | Build 4026: XAE Shell, HMI 14.18, OPC UA Server. TwinCAT Standard pending. |

The EtherCAT master is moving to one of the lab's newer mini PCs with an Intel
network chip. Model: TBD.

## CP6606 panel PC (working controller as of Oct 28, 2026)
| Item | Value |
|---|---|
| Model | Beckhoff CP6606-0001-0020, 7" touch panel |
| CPU | ARM Cortex-A8 1 GHz (CB3011-0002 mainboard) |
| OS / runtime | Windows CE 7, TwinCAT 3.1.4024 (projects must be built for 4024, platform TwinCAT CE7 (ARMV7)) |
| Hostname / AMS NetId | CP-2AE6A6 / 5.42.230.166.1.1 |
| LAN1 (X102) | IP 192.168.1.50. Engineering laptop / ADS. Shows in TwinCAT as FEC1. |
| LAN2 (X103, EtherCAT) | EtherCAT master port to EK1100 X1 IN. Shows in TwinCAT as CCAT DPRAM Slot 0. |
| Notes | Clock had reset to 2006, likely a dead backup battery. Check the clock before generating a license. |

Rack on the panel (from the Oct 28 scan): EK1100, EL1008, EL2008, EL7342, EL9011 end cap.

## EtherCAT racks
Wiring: station PC → rack 1 X1 IN, rack 1 X2 OUT → rack 2 X1 IN

| Rack | Position | Part | Description |
|---|---|---|---|
| 1 | Coupler | EK1100 | EtherCAT coupler |
| 2 | Coupler | EK1100 | EtherCAT coupler |
| TBD | TBD | EL1008 | 8 digital inputs, 24 V DC |
| TBD | TBD | EL2008 | 8 digital outputs, 24 V DC |
| TBD | TBD | EL7342 | 2-channel DC motor terminal |
| TBD | TBD | TBD | Remaining cards: fill from the scan |

Analog cards (EL3xxx / EL4xxx): TBD from the scan

## Damper/fan trainer
| Signal | Direction | Type | Notes |
|---|---|---|---|
| 24 V DC power | Supply | | |
| Damper Open | TBD | Digital | Command or limit switch? |
| Damper Close | TBD | Digital | Command or limit switch? |
| Analog Fan Control | Output | TBD | 0-10 V or 4-20 mA? |
| Analog Prox | Input | TBD | What does it measure? |git 