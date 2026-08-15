
Today I tested how my internet traffic actually travels from my device to the outside world, how IPs are shared, and why some routers hide themselves in traceroute.

### 1. Private IP vs Public IP (CGNAT at the Tower)
* When I connect to mobile data/Wi-Fi, my device gets a **private IP** (e.g., `100.64.0.12`).
* Thousands of users connect to the same cellular tower/ISP and share **one single public IP** (e.g., `203.0.113.45`).
* This is called **CGNAT (Carrier-Grade NAT)**. It translates all our private IPs into one public IP so we can access the internet without running out of IPv4 addresses.
[My Device: 100.64.0.12] ──┐
[Other User: 100.64.0.15] ──┼──> [Tower / CGNAT] ──> [Public IP: 203.0.113.45] ──> [Server]
[Other User: 100.64.0.88] ──┘


### 2. How Traceroute Finds the Path (TTL)

* Every packet has a **TTL (Time to Live)** value.
* Each router the packet passes through reduces TTL by 1.
* When TTL reaches 0, the router drops the packet and sends back a message with its own IP.
* `traceroute` starts with `TTL = 1`, then `TTL = 2`, `TTL = 3`, discovering one router at a time along the path.

**Command used:**
traceroute -I -n 8.8.8.8

* `-I`: Uses ICMP packets.
* `-n`: Shows raw IP numbers without waiting for domain name lookups.

### 3. Why the Tower Router Shows `* * *`

When running the trace, some hops didn't show an IP address and only showed `* * *`:

1  192.168.1.1       2 ms
2  100.64.0.1        5 ms
3  * * *
4  142.250.190.46   22 ms

**Why this happens:**

* I tried forcing it with UDP, TCP, and ICMP, but the router still hid its IP.
* The router is not broken—it is passing my data forward normally.
* It is configured to **drop or ignore ping/traceroute replies** to save router CPU and keep the internal ISP network hidden for security.



