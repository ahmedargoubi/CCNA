# Lab Guide - Test Network Latency with Ping and Traceroute

## Lab Overview

**Objective**: Measure and evaluate network latency using ping and traceroute commands to establish a network performance baseline.

**Duration**: 45-60 minutes

**Required Resources**:
- 1 PC with Internet access
- Command prompt access
- Permission to use ping/traceroute (check with instructor)

---

## Understanding the Lab Concepts

### What is Network Latency?
**Latency** is the time it takes for data to travel from source to destination, measured in **milliseconds (ms)**.

**Latency is affected by**:
- Physical distance between devices
- Number of hops (routers) in the path
- Network congestion
- Processing delays at each router
- Quality of network links

### Why Create a Baseline?
A **baseline** is a snapshot of normal network performance that helps:
- Identify performance problems
- Compare current vs normal performance
- Plan network capacity
- Troubleshoot issues
- Document network behavior

---

## Part 1: Use Ping to Document Network Latency

### Understanding the Ping Command

**What ping measures**:
- **Round-Trip Time (RTT)**: Time for packet to reach destination and return
- **Packet Loss**: Percentage of packets that didn't return
- **Minimum, Maximum, Average**: Statistical analysis of RTT

### Step 1: Verify Connectivity

**Purpose**: Test basic connectivity to target websites

**Regional Internet Registry (RIR) Websites**:
- **LACNIC** (Latin America): www.lacnic.net
- **AFRINIC** (Africa): www.afrinic.net
- **APNIC** (Asia Pacific): www.apnic.net

**Note**: RIPE (Europe) and ARIN (North America) don't respond to ICMP, so they can't be used.

**Commands to verify connectivity**:

```cmd
C:\Users\User1> ping www.lacnic.net
C:\Users\User1> ping www.afrinic.net
C:\Users\User1> ping www.apnic.net
```

**Expected output example**:
```
Pinging www.lacnic.net [200.3.14.184] with 32 bytes of data:
Reply from 200.3.14.184: bytes=32 time=220ms TTL=51
Reply from 200.3.14.184: bytes=32 time=231ms TTL=51
Reply from 200.3.14.184: bytes=32 time=243ms TTL=51
Reply from 200.3.14.184: bytes=32 time=255ms TTL=51

Ping statistics for 200.3.14.184:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 220ms, Maximum = 255ms, Average = 237ms
```

**Troubleshooting**:

**If you get IPv6 addresses instead of IPv4**:
```cmd
C:\Users\User1> ping -4 www.lacnic.net
```
The `-4` option forces IPv4 resolution.

**If ping fails**:
- Check internet connectivity
- Verify firewall isn't blocking ICMP
- Try a different website
- Check with instructor about network restrictions

---

### Step 2: Collect Network Data

**Purpose**: Gather sufficient data for statistical analysis (25 samples per site)

**Why 25 samples?**
- More samples = more accurate statistics
- Captures variation in network performance
- Identifies patterns and anomalies

**Windows Ping Options**:

To see all available options:
```cmd
C:\Users\User1> ping
```

**Key options**:
- **-n count**: Number of echo requests to send
- **-l size**: Size of packet to send (default 32 bytes)
- **-4**: Force IPv4
- **-6**: Force IPv6
- **-t**: Continuous ping (Ctrl+C to stop)

**Collecting data and saving to files**:

```cmd
C:\Users\User1> ping -n 25 www.lacnic.net > lacnic.txt
C:\Users\User1> ping -n 25 www.afrinic.net > afrinic.txt
C:\Users\User1> ping -n 25 www.apnic.net > apnic.txt
```

**Understanding the command**:
- `ping -n 25`: Send 25 echo requests
- `www.lacnic.net`: Target destination
- `>`: Redirect output to file (overwrites if exists)
- `lacnic.txt`: Output filename

**Important Notes**:
- Terminal appears blank while command runs (output redirected to file)
- May require administrative privileges on some systems
- Each command takes about 5-30 seconds depending on latency

**Alternative - Appending to existing file**:
```cmd
C:\Users\User1> ping -n 25 www.lacnic.net >> lacnic.txt
```
Using `>>` appends results instead of overwriting.

---

### Step 3: Verify Data Collection

**Check files were created**:
```cmd
C:\Users\User1> dir *.txt
```

**Expected output**:
```
Volume in drive C is OS
Volume Serial Number is 0A97-D265

Directory of C:\Users\User1

02/07/2013  12:59 PM    1,642 afrinic.txt
02/07/2013  01:00 PM    1,615 apnic.txt
02/07/2013  12:58 PM    1,589 lacnic.txt
               3 File(s)      4,846 bytes
```

**View file contents**:
```cmd
C:\Users\User1> more lacnic.txt
```

**Sample output analysis**:
```
Pinging www.lacnic.net [200.3.14.184] with 32 bytes of data:
Reply from 200.3.14.184: bytes=32 time=220ms TTL=51
Reply from 200.3.14.184: bytes=32 time=231ms TTL=51
Reply from 200.3.14.184: bytes=32 time=243ms TTL=51
...
Ping statistics for 200.3.14.184:
    Packets: Sent = 25, Received = 24, Lost = 1 (4% loss),
Approximate round trip times in milli-seconds:
    Minimum = 175ms, Maximum = 522ms, Average = 253ms
```

**What to record**:

| Website | Minimum (ms) | Maximum (ms) | Average (ms) | Packet Loss |
|---------|--------------|--------------|--------------|-------------|
| www.afrinic.net | | | | |
| www.apnic.net | | | | |
| www.lacnic.net | 175 | 522 | 253 | 4% |

**Tips for viewing files**:
- Press **Spacebar** to scroll through file
- Press **q** to quit viewing
- Or open files in Notepad: `notepad lacnic.txt`

---

### Analysis Questions

**Question 1: How is delay affected by geographical location?**

**Expected observations**:
- **Greater distance = Higher latency**: Packets traveling farther take longer
- **Different routes = Different delays**: Traffic to different continents uses different paths
- **Regional variations**: Some regions have better infrastructure

**Example analysis**:
```
From North America:
- LACNIC (South America): 200-300ms average
- AFRINIC (Africa): 300-400ms average  
- APNIC (Asia): 150-250ms average

Conclusion: Geographical distance significantly impacts latency.
Undersea cables and routing paths also play major roles.
```

**Factors to consider**:
1. **Physical distance**: Light travels at finite speed through fiber
2. **Number of hops**: More routers = more processing delay
3. **Infrastructure quality**: Developed regions often have better networks
4. **Traffic routing**: May not take most direct path

---

## Part 2: Use Traceroute to Document Network Latency

### Understanding Traceroute

**What traceroute does**:
- Discovers the path packets take through the network
- Measures latency at each hop
- Identifies bottlenecks and problem areas

**How it works**:
1. Sends packets with incrementing TTL values
2. Each router decrements TTL and drops packet when TTL=0
3. Router sends back **ICMP Time Exceeded** message
4. Source learns router's IP address and latency
5. Process continues until destination reached

**Windows vs Linux/Cisco**:
- **Windows**: `tracert` - Uses ICMP Echo Requests
- **Linux/Cisco**: `traceroute` - Uses UDP packets

---

### Step 1: Run Traceroute and Save Results

**Commands**:
```cmd
C:\Users\User1> tracert www.lacnic.net > traceroute_lacnic.txt
C:\Users\User1> tracert www.afrinic.net > traceroute_afrinic.txt
C:\Users\User1> tracert www.apnic.net > traceroute_apnic.txt
```

**If IPv6 addresses appear, force IPv4**:
```cmd
C:\Users\User1> tracert -4 www.lacnic.net > traceroute_lacnic.txt
```

**Note**: Traceroute takes longer than ping (several minutes per command)

---

### Step 2: Examine Traced Paths

**View results**:
```cmd
C:\Users\User1> more traceroute_lacnic.txt
```

**Sample output with analysis**:
```
Tracing route to www.lacnic.net [200.3.14.184]
over a maximum of 30 hops:

  1    3 ms    1 ms    2 ms  192.168.0.1
  2    *       *       *     Request timed out.
  3   14 ms   10 ms    9 ms  173-219-1-12.suddenlink.net [173.219.1.12]
  4   39 ms   38 ms   45 ms  173-219-1-232.suddenlink.net [173.219.1.232]
  5    *      38 ms   40 ms  173-219-1-98.suddenlink.net [173.219.1.98]
  6    *      35 ms   38 ms  lag-102.ear1.Chicago3.Level3.net [4.28.58.177]
  7    *       *       *     Request timed out.
  8   80 ms   79 ms   77 ms  GLOBAL-CROS.ear3.Miami2.Level3.net [4.15.156.54]
  9  341 ms  221 ms  222 ms  et-0-0-4-0.ptx-b.spo-piaf.algartelecom.com.br [168.197.23.182]
 10    *       *       *     Request timed out.
 11  197 ms  222 ms  334 ms  201-048-035-089.static.ctbctelecom.com.br [201.48.35.89]
 12  225 ms  175 ms  176 ms  xe-4-2-1-0.core1.nu.registro.br [200.160.0.180]
 13  269 ms  222 ms  221 ms  xe-0-0-0.ar3.nu.registro.br [200.160.0.249]
 14  217 ms  228 ms  218 ms  ae0-0.gw1.jd.lacnic.net [200.160.0.212]
 15    *     281 ms  220 ms  200.3.12.34
 16  231 ms  233 ms  212 ms  www.lacnic.net [200.3.14.184]

Trace complete.
```

**Understanding the output**:

**Each line shows**:
- **Hop number**: Position in the path (1, 2, 3...)
- **Three RTT measurements**: Three probes sent per hop
- **IP address**: Router's IP at that hop
- **Hostname**: Domain name of router (if resolved)

**Special symbols**:
- **\* \* \*** = Request timed out (router didn't respond)
- This is NORMAL - many routers don't respond to traceroute for security

**Key analysis points**:

**Hop 1** (192.168.0.1):
- Local gateway
- Very fast response (1-3 ms)
- Always the first hop

**Hop 6** (4.28.58.177):
- Average 37 ms latency
- Still within ISP network

**Hops 8-9** (78ms to 298ms):
- **MAJOR LATENCY INCREASE**
- Traffic crosses international link (USA to Brazil)
- Undersea cable adds significant delay

**Hop 16** (destination):
- Final destination reached
- Average 225 ms total latency

---

### Analysis Questions

**Question 2: What can you conclude regarding the relationship between round-trip time and geographical location?**

**Key observations from traceroute**:

1. **Local hops are fast** (1-50ms)
   - Hops 1-6: Within local ISP network
   - Low latency, minimal distance

2. **International links add significant delay**
   - Hops 8-9: 78ms → 298ms (220ms increase!)
   - Crossing oceans/continents is expensive

3. **Each hop adds latency**
   - Processing delay at each router
   - Cumulative effect throughout path

4. **Geographic distance correlates with latency**
   - Closer destinations = fewer hops = lower latency
   - Farther destinations = more hops = higher latency

**Example conclusion**:
```
Geographical location directly affects latency due to:
- Physical distance requiring more time for signal transmission
- Number of intermediate routers (hops) in the path
- International links (especially undersea cables) adding significant delay
- Different ISPs and network infrastructures in different regions
```

---

## Part 3: Extended Traceroute

### Using the -d Option

**Purpose**: Disable DNS reverse lookup to get more accurate timing

**Why use -d?**
- DNS lookups add delay to results
- Can make traceroute inaccurate
- Faster execution
- More reliable timing measurements

**Commands**:
```cmd
C:\Users\User1> tracert -d www.lacnic.net > traceroute_d_lacnic.txt
C:\Users\User1> tracert -d www.afrinic.net > traceroute_d_afrinic.txt
C:\Users\User1> tracert -d www.apnic.net > traceroute_d_apnic.txt
```

**Comparison of outputs**:

**Without -d option**:
```
6    *      35 ms   38 ms  lag-102.ear1.Chicago3.Level3.net [4.28.58.177]
```

**With -d option**:
```
6    *       *       *     Request timed out.
```
*OR*
```
6   35 ms   37 ms   38 ms  4.28.58.177
```

**Question 3: What is different about the tracert output when the -d option was added?**

**Key differences**:
1. **No hostnames shown** - Only IP addresses displayed
2. **Faster execution** - No waiting for DNS lookups
3. **More accurate timing** - DNS delay removed from measurements
4. **Cleaner output** - Simpler to read and analyze

**When to use -d**:
- When you need accurate latency measurements
- When DNS is slow or unavailable
- When hostnames are not important
- For automated scripts and baselines

---

## Additional Traceroute Options

**View all Windows tracert options**:
```cmd
C:\Users\User1> tracert
```

**Common options**:
```cmd
tracert -d        # Don't resolve hostnames
tracert -h 20     # Maximum hops (default 30)
tracert -w 2000   # Timeout in milliseconds (default 4000)
tracert -4        # Force IPv4
tracert -6        # Force IPv6
```

**Example with multiple options**:
```cmd
C:\Users\User1> tracert -d -h 20 -w 2000 www.lacnic.net
```

---

## Cisco IOS Extended Traceroute

**On Cisco devices, extended traceroute is interactive**:

```cisco
Router# traceroute
Protocol [ip]: 
Target IP address: 200.3.14.184
Source address: 10.1.1.1
Numeric display [n]: y
Timeout in seconds [3]: 2
Probe count [3]: 5
Minimum Time to Live [1]: 
Maximum Time to Live [30]: 20
Port Number [33434]: 
Loose, Strict, Record, Timestamp, Verbose[none]: 
Type escape sequence to abort.
Tracing the route to 200.3.14.184
VRF info: (vrf in name/id, vrf out name/id)
  1 10.1.1.2 4 msec 4 msec 4 msec 4 msec 4 msec
  2 10.2.2.2 8 msec 8 msec 8 msec 8 msec 8 msec
  3 200.3.14.184 12 msec 12 msec 12 msec 12 msec 12 msec
```

**Key parameters**:
- **Numeric display**: Skip DNS lookups (like -d)
- **Timeout**: How long to wait for response
- **Probe count**: Number of probes per hop (default 3)
- **Max TTL**: Maximum hops to try

---

## Reflection Questions

### Question 1: What do you need to do for an accurate baseline?

**To create an accurate network latency baseline**:

1. **Test at different times of day**
   - Morning (low traffic)
   - Midday (medium traffic)
   - Evening (high traffic)
   - Overnight (minimal traffic)

2. **Test on different days**
   - Weekdays vs weekends
   - Holidays vs normal days
   - Account for business cycles

3. **Collect multiple samples**
   - Minimum 25 samples per test
   - More samples = more accurate statistics
   - Identify patterns and outliers

4. **Test regularly**
   - Daily tests for critical links
   - Weekly tests for general monitoring
   - Monthly reports for trends

5. **Document everything**
   - Record date and time of tests
   - Note any network changes
   - Track environmental factors
   - Keep historical data

6. **Test multiple destinations**
   - Local resources
   - Regional resources
   - International resources
   - Critical business services

**Example baseline schedule**:
```
Week 1: 
- Monday: Test at 9am, 12pm, 3pm, 6pm, 9pm
- Tuesday: Test at 9am, 12pm, 3pm, 6pm, 9pm
- Wednesday: Test at 9am, 12pm, 3pm, 6pm, 9pm
- Thursday: Test at 9am, 12pm, 3pm, 6pm, 9pm
- Friday: Test at 9am, 12pm, 3pm, 6pm, 9pm
- Saturday: Test at 12pm, 6pm
- Sunday: Test at 12pm, 6pm

Repeat for 4 weeks to capture typical patterns
```

---

### Question 2: How can you use the baseline information?

**Uses of baseline data**:

1. **Troubleshooting Performance Issues**
   - Compare current performance to baseline
   - Identify when problems started
   - Determine severity of issues
   - Example: "Latency to server is now 500ms, baseline was 50ms"

2. **Capacity Planning**
   - Predict when upgrades needed
   - Justify budget requests
   - Plan for growth
   - Example: "Latency increasing 10% per quarter, upgrade needed in 6 months"

3. **SLA Monitoring**
   - Verify service level agreements
   - Track provider performance
   - Document violations
   - Example: "ISP promised <100ms latency, baseline shows 150ms average"

4. **Change Management**
   - Measure impact of network changes
   - Validate improvements
   - Rollback if performance degrades
   - Example: "After router upgrade, latency improved 30%"

5. **Problem Detection**
   - Set alerts based on baseline
   - Identify issues before users complain
   - Proactive monitoring
   - Example: "Alert when latency exceeds baseline by 50%"

6. **Trend Analysis**
   - Identify long-term patterns
   - Predict future problems
   - Optimize network design
   - Example: "Latency spikes every Friday at 5pm due to backups"

7. **Documentation and Reporting**
   - Demonstrate network health
   - Management reports
   - Historical records
   - Example: "Network performance improved 20% after infrastructure upgrade"

---

## Lab Best Practices

### Tips for Successful Lab Completion

1. **Organization**
   - Create a dedicated folder for test files
   - Use consistent naming conventions
   - Document all commands used

2. **Record Keeping**
   - Note exact time of each test
   - Record any anomalies
   - Save all output files
   - Create summary spreadsheets

3. **Multiple Tests**
   - Run tests multiple times
   - Test at different times
   - Compare results
   - Look for patterns

4. **Network Awareness**
   - Be aware of network policies
   - Don't overwhelm the network
   - Check with administrator first
   - Use reasonable test intervals

5. **Data Analysis**
   - Calculate averages
   - Identify outliers
   - Look for trends
   - Create charts/graphs

---

## Common Issues and Solutions

### Problem: Ping or Tracert Doesn't Work

**Solutions**:
- Check firewall settings
- Verify internet connectivity
- Try different websites
- Use -4 option for IPv4
- Check with network administrator

### Problem: "Request Timed Out" Messages

**Explanation**: 
- Normal behavior for some routers
- Security policy blocks ICMP
- Not necessarily a problem

**Action**: 
- Note which hops don't respond
- Focus on hops that do respond
- Compare patterns across tests

### Problem: High Packet Loss

**Possible causes**:
- Network congestion
- Faulty equipment
- ISP issues
- Wireless interference

**Action**:
- Test at different times
- Try wired connection
- Contact ISP if consistent
- Document for baseline

### Problem: Inconsistent Results

**Explanation**:
- Network conditions change constantly
- Normal variation expected
- This is why we collect multiple samples

**Action**:
- Run more tests
- Calculate averages
- Look for patterns
- Focus on trends, not individual readings

---

## Summary and Key Takeaways

### Important Concepts

1. **Latency is affected by distance and network path**
2. **Baseline measurements require multiple samples over time**
3. **Ping tests connectivity and measures round-trip time**
4. **Traceroute reveals the path and latency at each hop**
5. **Geographic location significantly impacts network performance**

### Key Commands Learned

```cmd
# Ping commands
ping www.example.com
ping -n 25 www.example.com > output.txt
ping -4 www.example.com

# Traceroute commands
tracert www.example.com
tracert -d www.example.com > output.txt
tracert -4 www.example.com

# File operations
dir *.txt
more filename.txt
```

### Skills Developed

✅ Using ping to measure network latency  
✅ Using traceroute to discover network paths  
✅ Redirecting command output to files  
✅ Analyzing network performance data  
✅ Creating network baselines  
✅ Understanding geographic impact on latency  

---

*This lab guide provides comprehensive coverage of network latency testing for CCNA certification.*