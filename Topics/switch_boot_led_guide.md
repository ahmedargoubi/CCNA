# Cisco Switch Boot Sequence & LED Indicators

## 5-Step Boot Sequence

### Step 1: POST (Power-On Self-Test)
- Loads from ROM
- Tests CPU, DRAM, Flash
- Hardware validation

### Step 2: Boot Loader
- Small program from ROM
- Runs after successful POST

### Step 3: CPU Initialization
- Initialize CPU registers
- Configure memory mapping and speed

### Step 4: Flash File System
- Initialize flash on system board
- Prepare access to IOS

### Step 5: Load IOS
- Locate IOS image in flash
- Load into RAM
- Transfer control to IOS
- Switch operational

**Boot Flow**: Power → POST → Boot Loader → Init CPU → Init Flash → Load IOS → Ready

**Time**: 30-90 seconds

---

## LED Indicators

### System LEDs (Left Panel)

#### 1. SYST (System Status)
| Color | Meaning |
|-------|---------|
| Off | No power |
| Green | Normal operation |
| Amber | Malfunction |

#### 2. RPS (Redundant Power Supply)
| Color | Meaning |
|-------|---------|
| Off | No RPS |
| Green | RPS operational |
| Amber | RPS fault |

---

### MODE Button Functions

**Press MODE button to cycle**: STAT → DUPLX → SPEED → PoE

#### STAT Mode (Port Status)
| LED | Meaning |
|-----|---------|
| Off | No link |
| Green | Link, idle |
| Blinking Green | Active traffic |
| Amber | STP blocking |

#### DUPLX Mode (Duplex)
| LED | Meaning |
|-----|---------|
| Off | Half-duplex |
| Green | Full-duplex |

#### SPEED Mode
| LED | Meaning |
|-----|---------|
| Off | 10 Mbps |
| Green | 100 Mbps |
| Blinking Green | 1 Gbps |

#### PoE Mode (Power over Ethernet)
| LED | Meaning |
|-----|---------|
| Off | No PoE |
| Green | PoE active |
| Amber | PoE disabled/fault |

---

## Quick Troubleshooting

### Boot Problems

**SYST LED Amber**: POST failure, hardware fault, corrupted IOS  
**SYST LED Off**: No power, failed power supply  
**Boot Loop**: Missing/corrupted IOS, check boot loader

### LED Diagnostics

**Check link**: MODE → STAT, look for green  
**Check speed**: MODE → SPEED, blinking green = 1 Gbps  
**Check PoE**: MODE → PoE, green = power supplied  
**Check duplex**: MODE → DUPLX, green = full-duplex

---

## CCNA Key Points

✅ Boot sequence: 5 steps (POST → Boot Loader → CPU → Flash → IOS)  
✅ POST and Boot Loader stored in ROM  
✅ IOS loaded from Flash into RAM  
✅ Green SYST = normal, Amber = problem  
✅ MODE button cycles through port information modes  
✅ Boot time: ~30-90 seconds

---

*Concise reference for switch boot process and LED indicators.*