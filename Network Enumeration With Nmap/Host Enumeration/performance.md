# Nmap - Performance

Scanning performance becomes important when we scan large networks or work with limited bandwidth.

Nmap provides several options to control scan speed and behavior:

| Option | Description |
|---|---|
| `-T <0-5>` | Sets the timing template |
| `--min-parallelism <number>` | Sets the minimum number of parallel operations |
| `--initial-rtt-timeout <time>` | Sets the initial timeout for responses |
| `--max-rtt-timeout <time>` | Sets the maximum timeout for responses |
| `--min-rate <number>` | Sets the minimum number of packets sent per second |
| `--max-retries <number>` | Sets how many times Nmap retries unanswered probes |

The main trade-off is:

> Faster scans can reduce accuracy and may generate more noticeable network traffic.

---

## Timeouts

When Nmap sends a packet, it waits for a response from the target.

The time required for a packet to travel to the target and for the response to return is called:

**RTT — Round-Trip Time**

If the timeout is too high, the scan may take longer.

If the timeout is too low, Nmap may stop waiting before the target responds, causing us to miss hosts or ports.

### Default Scan

```bash
sudo nmap 10.129.2.0/24 -F
```

Example result:

```text
256 IP addresses
10 hosts up
39.44 seconds
```

### Optimized RTT

```bash
sudo nmap 10.129.2.0/24 -F \
--initial-rtt-timeout 50ms \
--max-rtt-timeout 100ms
```

Example result:

```text
256 IP addresses
8 hosts up
12.29 seconds
```

### Options

| Option | Description |
|---|---|
| `10.129.2.0/24` | Scans the entire `/24` network |
| `-F` | Scans the top 100 most common ports |
| `--initial-rtt-timeout 50ms` | Initial amount of time Nmap waits for a response |
| `--max-rtt-timeout 100ms` | Maximum amount of time Nmap waits for a response |

### Important

The optimized scan was much faster:

```text
39.44s → 12.29s
```

However, it detected fewer hosts:

```text
10 hosts → 8 hosts
```

Therefore:

> Setting RTT timeouts too low can cause us to miss hosts or services because slower responses may arrive after Nmap has already stopped waiting.

---

# Max Retries

If Nmap does not receive a response, it can resend the probe.

We can control the maximum number of retries with:

```bash
--max-retries <number>
```

The default value is generally:

```text
10
```

Reducing the number of retries can significantly increase scan speed.

For example:

```bash
sudo nmap 10.129.2.0/24 -F --max-retries 0
```

With:

```bash
--max-retries 0
```

Nmap does not retry an unanswered probe.

If the target does not respond to the first packet, Nmap continues the scan without sending another one.

### Comparing Results

Default scan:

```bash
sudo nmap 10.129.2.0/24 -F | grep "/tcp" | wc -l
```

Result:

```text
23
```

Without retries:

```bash
sudo nmap 10.129.2.0/24 -F --max-retries 0 | grep "/tcp" | wc -l
```

Result:

```text
21
```

### Important

Reducing retries makes the scan faster, but it can reduce reliability.

Packets may be:

- dropped
- delayed
- filtered
- lost because of network congestion

Therefore:

> A target that does not answer the first probe is not necessarily unreachable.

---

# Rates

We can control how quickly Nmap sends packets using:

```bash
--min-rate <number>
```

This tells Nmap to attempt to maintain at least the specified number of packets per second.

Example:

```bash
sudo nmap 10.129.2.0/24 -F --min-rate 300
```

This tells Nmap to try to send at least:

```text
300 packets per second
```

### Default Scan

```bash
sudo nmap 10.129.2.0/24 -F -oN tnet.default
```

Example:

```text
10 hosts up
29.83 seconds
```

### Optimized Scan

```bash
sudo nmap 10.129.2.0/24 -F \
-oN tnet.minrate300 \
--min-rate 300
```

Example:

```text
10 hosts up
8.67 seconds
```

### Options

| Option | Description |
|---|---|
| `-F` | Scans the top 100 ports |
| `-oN tnet.minrate300` | Saves output using normal Nmap format |
| `--min-rate 300` | Attempts to send at least 300 packets per second |

In this example, both scans detected the same number of open ports:

```text
23
```

But the scan using `--min-rate 300` was significantly faster.

---

## White-Box Scenario

Increasing the packet rate can be especially useful during a **white-box penetration test**.

In this scenario, we may:

- know the network topology
- know the available bandwidth
- be authorized to generate significant traffic
- have our scanning system whitelisted by security controls

This allows us to use more aggressive scanning configurations without being blocked as easily.

---

# Timing Templates

Instead of manually configuring several performance parameters, Nmap provides predefined timing templates.

Syntax:

```bash
-T <0-5>
```

There are six templates:

| Template | Name | Behavior |
|---|---|---|
| `-T0` | Paranoid | Extremely slow |
| `-T1` | Sneaky | Very slow |
| `-T2` | Polite | Slow |
| `-T3` | Normal | Default |
| `-T4` | Aggressive | Fast |
| `-T5` | Insane | Very fast |

The default is:

```bash
-T3
```

These templates automatically configure several internal timing parameters.

---

## T0 - Paranoid

```bash
-T0
```

Extremely slow scanning.

Designed to minimize traffic frequency and make scans less aggressive.

---

## T1 - Sneaky

```bash
-T1
```

Very slow scanning.

Produces traffic gradually instead of sending many probes quickly.

---

## T2 - Polite

```bash
-T2
```

Reduces network load and uses less bandwidth.

---

## T3 - Normal

```bash
-T3
```

Default Nmap timing behavior.

If we do not specify a timing template, Nmap normally uses this one.

---

## T4 - Aggressive

```bash
-T4
```

Uses more aggressive timing assumptions.

Common when:

- the network is stable
- latency is low
- we want faster scans
- we are not heavily concerned about generating noticeable traffic

---

## T5 - Insane

```bash
-T5
```

Uses extremely aggressive timing.

Example:

```bash
sudo nmap 10.129.2.0/24 -F -T5
```

It can significantly reduce scan time but assumes a very fast and reliable network.

It may cause us to:

- miss slow responses
- lose accuracy
- generate large amounts of traffic
- trigger IDS/IPS or other security mechanisms

---

# Timing Comparison

### Default

```bash
sudo nmap 10.129.2.0/24 -F -oN tnet.default
```

Result:

```text
32.44 seconds
```

### T5

```bash
sudo nmap 10.129.2.0/24 -F -oN tnet.T5 -T5
```

Result:

```text
18.07 seconds
```

Both scans found:

```text
23 open TCP ports
```

In this specific example, increasing the timing aggressiveness improved performance without losing results.

However:

> This does not mean that `-T5` will always produce the same results as a normal scan.

The behavior depends on:

- network latency
- packet loss
- firewall behavior
- IDS/IPS
- target responsiveness
- available bandwidth

---

# Performance vs Accuracy

The most important concept from this section is the trade-off between:

```text
Speed ↔ Accuracy
```

More aggressive settings usually mean:

```text
Higher speed
      ↓
Lower waiting time
      ↓
Fewer retries
      ↓
More packets per second
      ↓
Greater chance of missing responses
```

Therefore, we should not simply make every scan as fast as possible.

We should adapt the scan to the network environment.

---

# Key Commands

### Fast scan of top 100 ports

```bash
sudo nmap <target> -F
```

### Reduce RTT timeout

```bash
sudo nmap <target> --initial-rtt-timeout 50ms --max-rtt-timeout 100ms
```

### Disable retries

```bash
sudo nmap <target> --max-retries 0
```

### Set minimum packet rate

```bash
sudo nmap <target> --min-rate 300
```

### Aggressive timing

```bash
sudo nmap <target> -T4
```

### Maximum timing aggressiveness

```bash
sudo nmap <target> -T5
```

### Save normal output

```bash
sudo nmap <target> -oN scan.txt
```

---

# Main Takeaways

- **RTT** represents the round-trip time between sending a packet and receiving a response.
- Lowering RTT timeouts speeds up scans but may cause us to miss slow responses.
- `--max-retries` controls how many times Nmap retransmits unanswered probes.
- Fewer retries increase speed but reduce reliability.
- `--min-rate` controls the minimum packet sending rate.
- Higher packet rates can greatly increase scan speed.
- Nmap provides timing templates from `-T0` to `-T5`.
- `-T3` is the default.
- Higher timing templates are faster but more aggressive.
- Aggressive scans may trigger IDS/IPS or other security mechanisms.
- Scan performance should always be balanced against accuracy and the characteristics of the target network.