# SSH Ping but Login Fails

This guide provides instructions for configuring and troubleshooting WireGuard on macOS. It focuses on optimizing the MTU setting for a network setup where an MTU value of **1380** is recommended, based on 0% packet loss observed at 1352 bytes of data.

---
**MTU**  
(Maximum Transmission Unit) is the largest size of a data packet that can be transmitted over a network. Setting the MTU correctly ensures that packets can be sent without fragmentation, which can improve network performance and reduce packet loss. When the MTU is too high for a network path, packets may be fragmented or dropped, causing connectivity issues. In the context of configuring WireGuard, choosing the appropriate MTU is crucial for maintaining stable connections and seamless VPN performance. The MTU should be adjusted according to the network's capabilities to avoid packet loss while ensuring efficient data transfer.

---

## Prerequisites

1. **macOS System**  
   Ensure you have administrator privileges to modify network configurations.

2. **WireGuard Configuration File**  
   Have the WireGuard configuration file (e.g., `wg0.conf`) ready. This file is typically provided by your VPN or network administrator.

---

## Steps to Configure WireGuard

### 1. Configure MTU

1. Open the WireGuard configuration file:

2. Add or update the MTU setting under the `[Interface]` section:
   
   ```ini
   [Interface]
   MTU = 1380
   ```

## Example `wg0.conf` File

```ini
[Interface]
PrivateKey = <your-private-key>
Address = 192.168.5.2/24
MTU = 1380

[Peer]
PublicKey = <peer-public-key>
Endpoint = 192.168.5.47:51820
AllowedIPs = 0.0.0.0/0, ::/0
PersistentKeepalive = 25
```

---
4. Save and exit the editor.

### 2. Start the WireGuard Interface

If the interface is already running, restart it.

### 3. Verify Connection

Check the status of the WireGuard interface:
```bash
wg show
```

Test the connection with `ping` to ensure stability:
```bash
ping -D -s 1352 8.8.8.8
```

**Output Example with MTU of 1352 (0% packet loss)**:
```
PING 8.8.8.8 (8.8.8.8): 1352 data bytes
1352 bytes from 8.8.8.8: icmp_seq=0 ttl=56 time=67.719 ms
1352 bytes from 8.8.8.8: icmp_seq=1 ttl=56 time=74.441 ms
1352 bytes from 8.8.8.8: icmp_seq=2 ttl=56 time=74.155 ms
^C
--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 67.719/72.105/74.441/3.104 ms
```

**Additional Example with MTU of 1472 (Packet Loss Observed)**:
```
PING 8.8.8.8 (8.8.8.8): 1472 data bytes
556 bytes from 10.1.1.9: frag needed and DF set (MTU 1492)
Vr HL TOS  Len   ID Flg  off TTL Pro  cks      Src      Dst
 4  5  00 dc05 84fe   0 0000  3f  01 9494 10.22.2.105  8.8.8.8 

Request timeout for icmp_seq 0
556 bytes from 10.1.1.9: frag needed and DF set (MTU 1492)
Vr HL TOS  Len   ID Flg  off TTL Pro  cks      Src      Dst
 4  5  00 dc05 36fd   0 0000  3f  01 e295 10.22.2.105  8.8.8.8 

Request timeout for icmp_seq 1
556 bytes from 10.1.1.9: frag needed and DF set (MTU 1492)
Vr HL TOS  Len   ID Flg  off TTL Pro  cks      Src      Dst
 4  5  00 dc05 c729   0 0000  3f  01 5269 10.22.2.105  8.8.8.8 

```

### 4. General Syntax for MTU Value Check

To test different MTU values, use:
```bash
ping -D -s <MTU-value> 8.8.8.8
```

For example, if you observe packet loss at 1395 but 0% loss at 1352, then set your MTU to **1380** for optimal performance.

---

## Troubleshooting

### Command Not Found Errors
- If `wg` or `wg-quick` commands are not found, ensure WireGuard tools are installed properly.

### Persistent Issues with MTU
- Test lower MTU values by running `ping` with smaller sizes and adjust the configuration file accordingly.
- Example:
  ```bash
  ping -D -s 1340 8.8.8.8
  ```

### Restart WireGuard Interface
- If changes are made to the configuration file, always restart the interface:
  ```bash
  sudo wg-quick down wg0
  sudo wg-quick up wg0
  ```

---

## Additional Notes
- The MTU value of **1380** is based on testing and may vary depending on your network setup.
- Regularly monitor the interface with:
  ```bash
  wg show
  ```

---
