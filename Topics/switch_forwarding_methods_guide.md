# Switches and Forwarding Methods - Complete Guide

## Table of Contents
1. [What is a Switch?](#what-is-a-switch)
2. [Frame Forwarding Methods](#frame-forwarding-methods)
3. [Memory Buffering in Switches](#memory-buffering-in-switches)
4. [Duplex and Speed Settings](#duplex-and-speed-settings)
5. [Auto-MDIX](#auto-mdix)
6. [Quick Reference](#quick-reference)

---

## What is a Switch?

A **switch** is a Layer 2 network device that forwards frames based on **MAC addresses**. It's much smarter than a hub!

### Switch vs Hub

| Feature | Hub | Switch |
|---------|-----|--------|
| **Intelligence** | Dumb (Layer 1) | Smart (Layer 2) |
| **Forwarding** | Floods to all ports | Forwards to specific port |
| **Bandwidth** | Shared among all devices | Dedicated per port |
| **Collisions** | Many collisions (CSMA/CD) | No collisions (full-duplex) |
| **Performance** | Poor with traffic | Excellent |

**Simple Analogy:**
- **Hub** = Party where everyone hears everything (shouting match!)
- **Switch** = Phone system where calls go directly to the right person

---

## Frame Forwarding Methods

When a switch receives a frame, it needs to decide **HOW** and **WHEN** to forward it. There are **4 main methods**:

### Overview of the 4 Methods

| Method | When Forwarding Starts | Error Checking | Speed | Latency |
|--------|----------------------|----------------|-------|---------|
| **Store-and-Forward** | After entire frame received | Full (FCS check) | Slowest | Highest |
| **Cut-Through** | After destination MAC read | None | Fastest | Lowest |
| **Fragment-Free** | After first 64 bytes | Checks collision fragment | Medium | Medium |
| **Adaptive Cut-Through** | Switches between methods | Dynamic | Variable | Variable |

---

### 1. Store-and-Forward Switching

#### How It Works

```
Step 1: Receive ENTIRE frame into buffer
Step 2: Check FCS (Frame Check Sequence) for errors
Step 3: If no errors → Forward to destination port
Step 4: If errors → Drop the frame
```

**Visual Process:**
```
Frame arrives → [Store Complete Frame] → [Check FCS] → Forward/Drop
                         ↓
                   Takes Time!
```

#### Characteristics

**✅ Advantages:**
- **Error checking**: Corrupted frames are dropped (won't waste bandwidth)
- **Most reliable**: Ensures only good frames forwarded
- **Works with different speeds**: Can receive at one speed, send at another
- **Required for**: Switched networks with different port speeds

**❌ Disadvantages:**
- **Higher latency**: Must wait for entire frame
- **Slower**: Processing takes more time
- **More memory**: Needs to store complete frames

#### When to Use
- **Mixed-speed networks** (10/100/1000 Mbps ports)
- **Quality matters** more than speed
- **Default method** on most modern switches

**Real-World Example:**
Like a postal sorting center that:
1. Receives complete package
2. Checks if package is damaged
3. Only forwards undamaged packages
4. Destroys damaged ones

---

### 2. Cut-Through Switching

#### How It Works

```
Step 1: Start receiving frame
Step 2: Read destination MAC address (first 6 bytes after preamble)
Step 3: Look up MAC address in table
Step 4: IMMEDIATELY start forwarding to destination port
        (while still receiving the rest of the frame!)
```

**Visual Process:**
```
Frame arriving → Read Dest MAC → Start Forwarding → Rest follows
                      ↓
                 Super Fast!
```

#### Characteristics

**✅ Advantages:**
- **Lowest latency**: Starts forwarding immediately
- **Fastest method**: No waiting for complete frame
- **Best for**: Latency-sensitive applications (VoIP, gaming)

**❌ Disadvantages:**
- **No error checking**: Forwards corrupted frames too
- **Wastes bandwidth**: Bad frames use network resources
- **Same speed required**: All ports must be same speed
- **Collision fragments**: May forward incomplete frames

#### When to Use
- **Low latency critical** (real-time applications)
- **All ports same speed**
- **Clean network** (low error rates)

**Real-World Example:**
Like a relay race where:
- Runner 1 passes baton to Runner 2
- Runner 2 starts running IMMEDIATELY
- Doesn't check if baton is damaged
- Just go, go, go!

---

### 3. Fragment-Free Switching (Modified Cut-Through)

#### How It Works

```
Step 1: Receive FIRST 64 BYTES of frame
Step 2: Check if this fragment is error-free
Step 3: Forward frame to destination
```

**Why 64 bytes?**
- **Minimum Ethernet frame size** = 64 bytes
- **Collision detection** happens within first 64 bytes
- **If frame ≥ 64 bytes** = No collision occurred
- **If frame < 64 bytes** = Collision fragment (runt frame)

**Visual Process:**
```
Frame arriving → [Wait for 64 bytes] → Check collision → Forward
                          ↓
                   Middle Ground!
```

#### Characteristics

**✅ Advantages:**
- **Filters collision fragments**: Drops runt frames
- **Better than cut-through**: Some error detection
- **Lower latency than store-and-forward**: Faster than full buffering
- **Compromise solution**: Balance between speed and reliability

**❌ Disadvantages:**
- **Doesn't catch all errors**: Only checks first 64 bytes
- **Still forwards some bad frames**: Errors after 64 bytes get through
- **More complex**: Needs to count bytes

#### When to Use
- **Moderate latency requirements**
- **Some quality control needed**
- **Networks with occasional collisions**

**Real-World Example:**
Like a security checkpoint that:
- Quickly checks ID (first 64 bytes)
- If ID looks valid → let through
- Doesn't do full body scan (complete frame check)
- Faster than full security, better than no security

---

### 4. Adaptive Cut-Through Switching

#### How It Works

```
Smart switch that CHANGES methods based on conditions:

Normal Operation → Use Cut-Through (fast!)
                      ↓
                Error rate increases
                      ↓
                Switch to Store-and-Forward (reliable!)
                      ↓
                Error rate decreases
                      ↓
                Switch back to Cut-Through (fast again!)
```

**Visual Process:**
```
Monitor Errors → High Errors? → Store-and-Forward
                      ↓
                 Low Errors? → Cut-Through
```

#### Characteristics

**✅ Advantages:**
- **Best of both worlds**: Fast when possible, reliable when needed
- **Automatic adaptation**: No manual configuration
- **Intelligent**: Responds to network conditions
- **Optimizes performance**: Balances speed and reliability

**❌ Disadvantages:**
- **More complex**: Requires monitoring and switching logic
- **Not all switches support it**: Usually enterprise-grade only
- **Slight overhead**: Monitoring takes some resources

#### When to Use
- **Variable network conditions**
- **Need both speed and reliability**
- **Enterprise environments**
- **When supported by switch**

**Real-World Example:**
Like a smart highway system:
- **Normal traffic** → Green lights, fast flow (cut-through)
- **Accident detected** → Slow down, check lanes (store-and-forward)
- **All clear** → Speed up again (cut-through)

---

## Comparison Chart: All 4 Methods

### Speed vs Reliability

```
Fast ←→ Slow
↑                                                                      ↑
Cut-Through    Fragment-Free    Adaptive         Store-and-Forward
(No check)     (Partial check)  (Dynamic)        (Full check)
↓                                                                      ↓
Unreliable ←→ Reliable
```

### Decision Tree

```
Do you need ERROR CHECKING?
├─ NO → Need LOWEST LATENCY?
│       ├─ YES → Cut-Through
│       └─ NO → Fragment-Free
│
└─ YES → Need DYNAMIC ADAPTATION?
         ├─ YES → Adaptive Cut-Through (if available)
         └─ NO → Store-and-Forward
```

---

## Memory Buffering in Switches

**Memory buffering** is how switches temporarily store frames before forwarding them. This is necessary when:
- Receiving faster than forwarding
- Multiple frames arrive for same destination port
- Different port speeds require buffering

### Two Types of Memory Buffering

---

### 1. Port-Based Memory Buffering

#### How It Works

Each **port has its own dedicated memory queue**.

**Structure:**
```
Port 1 → [Queue 1] → Forward
Port 2 → [Queue 2] → Forward
Port 3 → [Queue 3] → Forward
Port 4 → [Queue 4] → Forward
```

#### Characteristics

**Memory Allocation:**
- **Fixed memory per port**
- **Cannot share between ports**
- **Simple management**

**Example:**
```
Switch with 24 ports, 1 MB buffer memory:
Each port gets: 1 MB ÷ 24 = ~42 KB
```

**✅ Advantages:**
- **Simple to implement**
- **Predictable performance** per port
- **Fair allocation** (each port gets equal share)
- **No port starvation** (guaranteed buffer space)

**❌ Disadvantages:**
- **Inflexible**: Busy port can't borrow from idle ports
- **Wasted memory**: Unused buffers sit idle
- **Not optimal**: Some ports may need more, some less

**When Problems Occur:**
```
Port 1: Heavy traffic → Buffer FULL → Frames DROPPED
Port 2: No traffic → Buffer EMPTY → Memory wasted
Port 1 cannot use Port 2's buffer!
```

#### Real-World Analogy
Like a parking lot where:
- Each store has assigned spaces
- Store A is packed (needs more spaces)
- Store B is empty (spaces wasted)
- But Store A can't use Store B's spaces
- Fixed and inflexible!

---

### 2. Shared Memory Buffering

#### How It Works

All ports **share a common memory pool**.

**Structure:**
```
              [Common Memory Pool]
                      ↓
      ┌───────┬───────┼───────┬───────┐
      ↓       ↓       ↓       ↓       ↓
    Port 1  Port 2  Port 3  Port 4  Port 5
```

#### Characteristics

**Memory Allocation:**
- **Dynamic allocation** based on need
- **Shared among all ports**
- **Flexible and efficient**

**Example:**
```
Switch with 24 ports, 1 MB buffer:
Busy port can use up to 1 MB if needed!
Idle ports use 0 MB (no waste)
```

**✅ Advantages:**
- **Flexible**: Busy ports get more memory
- **Efficient**: No wasted memory
- **Better performance**: Can handle traffic bursts
- **Optimal utilization**: Memory goes where needed

**❌ Disadvantages:**
- **More complex**: Requires sophisticated management
- **Possible starvation**: One port could hog memory
- **Needs good algorithms**: Fair sharing requires logic

**How It Adapts:**
```
Port 1: Heavy traffic → Gets MORE buffer space
Port 2: No traffic → Uses NO buffer space
Port 3: Medium traffic → Gets SOME buffer space
Memory dynamically allocated!
```

#### Real-World Analogy
Like a parking lot where:
- All spaces are shared (first come, first served)
- Busy store gets more spaces when needed
- Empty stores don't waste any spaces
- Flexible and efficient!

---

### Port-Based vs Shared Memory

| Aspect | Port-Based | Shared Memory |
|--------|------------|---------------|
| **Allocation** | Fixed per port | Dynamic |
| **Flexibility** | Low | High |
| **Efficiency** | Can waste memory | Optimal usage |
| **Complexity** | Simple | Complex |
| **Burst Handling** | Limited | Excellent |
| **Fairness** | Guaranteed | Requires management |
| **Modern Use** | Older switches | Modern switches |

**Most modern switches use SHARED MEMORY buffering** because it's more efficient!

---

## Duplex and Speed Settings

**Duplex** and **Speed** are critical settings that determine how devices communicate.

### Understanding Duplex

**Duplex** refers to the direction of data flow.

---

### Half-Duplex

#### How It Works

**Only ONE direction at a time** - either send OR receive, not both simultaneously.

**Visual:**
```
Device A ←────── Device B  (B is sending)
         ────────→         (Now A is sending)
```

**Think of it like:**
- **Walkie-talkie**: "Over" (can't talk while listening)
- **One-lane bridge**: Cars alternate directions

#### Characteristics

**When Used:**
- **Hubs** (shared medium)
- **Old network equipment**
- **Wireless networks** (can't transmit and receive on same frequency)

**Requirements:**
- **CSMA/CD** must be enabled
- **Collision detection** active
- **Maximum segment length** restrictions apply

**Performance:**
- **Bandwidth shared** between send/receive
- **Collisions possible**
- **Lower throughput** (~50% of link speed)

**Example:**
```
10 Mbps Half-Duplex:
Effective throughput: ~5 Mbps (shared between directions)
```

---

### Full-Duplex

#### How It Works

**Both directions simultaneously** - send AND receive at the same time.

**Visual:**
```
Device A ←──────→ Device B  (Both sending and receiving)
```

**Think of it like:**
- **Telephone**: Both can talk at once (though not polite!)
- **Two-lane road**: Traffic flows both ways simultaneously

#### Characteristics

**When Used:**
- **Switches** (dedicated connections)
- **Modern networks** (default)
- **Point-to-point links**

**Requirements:**
- **No CSMA/CD** needed
- **No collision detection** (can't collide!)
- **Dedicated bandwidth** per port

**Performance:**
- **Full bandwidth** in EACH direction
- **No collisions** ever
- **Maximum throughput** (100% of link speed each way)

**Example:**
```
1 Gbps Full-Duplex:
Send: 1 Gbps
Receive: 1 Gbps
Total potential: 2 Gbps (1 Gbps each direction)
```

---

### Duplex Comparison

| Aspect | Half-Duplex | Full-Duplex |
|--------|-------------|-------------|
| **Direction** | One at a time | Simultaneous |
| **Collisions** | Possible | Impossible |
| **CSMA/CD** | Required | Disabled |
| **Throughput** | ~50% | 100% each way |
| **Usage** | Hubs, wireless | Switches |
| **Speed** | Slower | Faster |

---

### Speed Settings

**Speed** refers to the data rate of the link.

#### Common Ethernet Speeds

| Standard | Speed | Cable Type | Max Distance |
|----------|-------|------------|--------------|
| **10BASE-T** | 10 Mbps | Cat3 or better | 100m |
| **100BASE-TX** | 100 Mbps (Fast Ethernet) | Cat5 or better | 100m |
| **1000BASE-T** | 1 Gbps (Gigabit) | Cat5e or better | 100m |
| **10GBASE-T** | 10 Gbps | Cat6a or better | 100m |

#### Speed Examples in Real Life

```
10 Mbps:   Download 1 GB file in ~13 minutes
100 Mbps:  Download 1 GB file in ~80 seconds
1 Gbps:    Download 1 GB file in ~8 seconds
10 Gbps:   Download 1 GB file in ~0.8 seconds
```

---

### Auto-Negotiation

**Auto-negotiation** is a process where two devices automatically determine the best speed and duplex settings.

#### How Auto-Negotiation Works

```
Step 1: Devices send capabilities (FLP - Fast Link Pulses)
Step 2: Both devices list what they support
Step 3: Select HIGHEST common speed and duplex
Step 4: Configure both sides with agreed settings
```

**Negotiation Priority (Highest to Lowest):**
```
1. 1000 Mbps Full-Duplex
2. 1000 Mbps Half-Duplex
3. 100 Mbps Full-Duplex
4. 100 Mbps Half-Duplex
5. 10 Mbps Full-Duplex
6. 10 Mbps Half-Duplex
```

#### Configuration Options

**Option 1: Auto (Recommended)**
```cisco
interface GigabitEthernet0/1
 speed auto
 duplex auto
```

**Option 2: Manual (Specific Requirements)**
```cisco
interface GigabitEthernet0/1
 speed 100
 duplex full
```

---

### Duplex Mismatch Problem

**One of the most common network issues!**

#### What is Duplex Mismatch?

When two connected devices have **different duplex settings**.

**Problem Scenario:**
```
Switch Port: Full-Duplex
     ↕
PC NIC: Half-Duplex

Result: COMMUNICATION PROBLEMS!
```

#### Symptoms
- **Slow network** performance
- **Intermittent connectivity**
- **Packet loss**
- **CRC errors** on interface
- **Late collisions**
- **Works but very slow**

#### Why It Happens
```
Switch (Full-Duplex):
- Sends and receives simultaneously
- Doesn't expect collisions

PC (Half-Duplex):
- Sees incoming frames while sending
- Thinks: "COLLISION!"
- Stops transmitting
- CHAOS!
```

#### How to Avoid

**Best Practice #1: Use Auto-Negotiation**
```cisco
! On BOTH sides
speed auto
duplex auto
```

**Best Practice #2: If Manual, Match Both Sides**
```cisco
! Switch
interface Gi0/1
 speed 1000
 duplex full

! Must match on connected device!
```

**Verification:**
```cisco
show interface GigabitEthernet0/1
! Check speed and duplex settings
! Look for errors/collisions
```

---

## Auto-MDIX

**Auto-MDIX** = Automatic Medium-Dependent Interface Crossover

### The Old Problem: Cable Types

In the past, you needed **different cable types** for different connections:

#### Straight-Through Cable
**Used for:**
- Switch to Router
- Switch to PC
- Switch to Server

**Pinout:**
```
Pin 1 → Pin 1
Pin 2 → Pin 2
Pin 3 → Pin 3
...
```

#### Crossover Cable
**Used for:**
- Switch to Switch
- Router to Router
- PC to PC

**Pinout:**
```
Pin 1 → Pin 3
Pin 2 → Pin 6
Pin 3 → Pin 1
Pin 6 → Pin 2
```

#### The Frustration

```
Wrong cable type → No connection → Frustration! 😤
"I need a crossover cable!"
"No, wait, straight-through!"
"Where's my cable tester?!"
```

---

### Auto-MDIX Solution

**Auto-MDIX automatically detects and corrects cable type!**

#### How Auto-MDIX Works

```
Step 1: Device detects cable type being used
Step 2: Automatically adjusts internal wiring
Step 3: Works with BOTH straight-through and crossover cables!
```

**Visual:**
```
Switch Port with Auto-MDIX:
    ↓
[Detects cable type]
    ↓
[Adjusts pinout internally]
    ↓
Connection works! ✅
```

#### Benefits

**✅ Advantages:**
- **Any cable works**: Straight-through or crossover
- **No more frustration**: Wrong cable? No problem!
- **Simplifies installation**: No need to remember cable rules
- **Fewer cables needed**: Just buy straight-through

**Real-World Analogy:**
Like a **universal power adapter** that works in any country:
- US plug? ✅ Works!
- EU plug? ✅ Works!
- UK plug? ✅ Works!

---

### Configuring Auto-MDIX

#### Enable Auto-MDIX (Cisco)

```cisco
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# mdix auto
```

#### Verify Auto-MDIX

```cisco
Switch# show controllers ethernet-controller GigabitEthernet0/1

! Look for:
! Operational MDI: MDIX (or MDI)
```

#### Notes

- **Usually enabled by default** on modern switches
- **Requires auto-negotiation** for speed/duplex
- **Works with 10/100/1000 Mbps**

---

### Cable Types Summary (With Auto-MDIX)

**Before Auto-MDIX:**
```
Connection Type          Cable Needed
Switch to PC             Straight-Through
Switch to Switch         Crossover ⚠️
PC to PC                 Crossover ⚠️
```

**With Auto-MDIX:**
```
Connection Type          Cable Needed
Switch to PC             Any cable ✅
Switch to Switch         Any cable ✅
PC to PC                 Any cable ✅
```

**Modern Reality:**
- Just buy **straight-through cables**
- Auto-MDIX handles the rest!
- Crossover cables becoming obsolete

---

## Quick Reference

### Frame Forwarding Methods

| Method | Latency | Error Check | Best For |
|--------|---------|-------------|----------|
| Store-and-Forward | High | Full (FCS) | Mixed speeds, reliability |
| Cut-Through | Low | None | Low latency, same speeds |
| Fragment-Free | Medium | Partial (64B) | Compromise |
| Adaptive | Variable | Dynamic | Enterprise networks |

### Memory Buffering

| Type | Allocation | Efficiency | Modern Use |
|------|------------|------------|------------|
| Port-Based | Fixed per port | Lower | Older switches |
| Shared Memory | Dynamic | Higher | Modern switches ✅ |

### Duplex Settings

| Setting | Collisions | Throughput | Usage |
|---------|------------|------------|-------|
| Half-Duplex | Possible | ~50% | Hubs, wireless |
| Full-Duplex | None | 100% each way | Switches ✅ |

### Key Commands

```cisco
! View interface settings
show interface GigabitEthernet0/1

! Configure speed and duplex
interface GigabitEthernet0/1
 speed auto
 duplex auto
 mdix auto

! Verify auto-MDIX
show controllers ethernet-controller GigabitEthernet0/1
```

---

## Remember This!

✅ **Store-and-Forward** = Slowest but most reliable (checks errors)  
✅ **Cut-Through** = Fastest but no error checking  
✅ **Fragment-Free** = Middle ground (checks first 64 bytes)  
✅ **Shared Memory** = Better than port-based (modern switches)  
✅ **Full-Duplex** = No collisions, maximum performance  
✅ **Half-Duplex** = One direction at a time, collisions possible  
✅ **Auto-Negotiation** = Let devices figure out best settings  
✅ **Auto-MDIX** = Any cable works, no more crossover headaches!  
✅ **Duplex Mismatch** = Common problem, causes slow performance