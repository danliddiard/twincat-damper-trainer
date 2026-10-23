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