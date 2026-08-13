# Rocky Linux Installation

## Overview

This section covers the initial Rocky Linux installation for the HPC cluster.

The virtual cluster consists of:
- 1 Head Node
- 1 Compute Node

The real-life cluster consists of:
- 1 Head Node
- 2 Compute Nodes

## VirtualBox Configuration

### Head Node

| Adapter | Type | Purpose |
|---|---|---|
| Adapter 1 | NAT | Internet access |
| Adapter 2 | Internal Network | Cluster communication |
| Adapter 3 | Host-only | MobaXTerm connection |

### Compute Node

| Adapter | Type | Purpose |
|---|---|---|
| Adapter 1 | Internal Network | Cluster communication |

> Both cluster adapters must use the same VirtualBox Internal Network.

## Real-Life Configuration With 3 Nodes

### Head Node

| Port | Type | Purpose |
|---|---|---|
| Port 1 | NAT | Internet access |
| Port 2 | Internal Network | Head to Compute 1 |
| Port 3 | Internal Network | Head to Compute 2 |

### Compute Node 1

| Port | Type | Purpose |
|---|---|---|
| Port 1 | Internal Network | Compute 1 to Head |
| Port 2 | Internal Network | Compute 1 to Compute 2 |

### Compute Node 2

| Port | Type | Purpose |
|---|---|---|
| Port 1 | Internal Network | Compute 2 to Head |
| Port 2 | Internal Network | Compute 2 to Compute 1 |

## Rocky Linux Installation

Install Rocky Linux on all machines.

### OS Setup

1. Select your language and press **Continue**.
2. Under **System**, select **Installation Destination** and choose the disk you want the OS installed on.
3. On the head node, go to **Network** and configure the ports connected to the compute nodes.
4. On the compute nodes, go to **Network** and configure the ports as shown in the design above. **Note:** don't forget to set the DNS.
5. Set the root password and create a user on all devices. Don't forget to give the user admin (sudo) privileges.

### On the Head Node

Once everything is set up, run these steps on the head node to ensure communication between the nodes and the internet.

#### Configure nftables

Open the main nftables config file:

```bash
sudo vi /etc/sysconfig/nftables.conf
```

Uncomment the following line:

```
include "/etc/nftables/main.nft"
```

Now open `main.nft`:

```bash
sudo vi /etc/nftables/main.nft
```

<img width="747" height="751" alt="image" src="https://github.com/user-attachments/assets/32ce8a34-e15c-443b-b984-def99e7122ab" />

Add all the relevant ports to the elements on the head node, and uncomment the following line so the NAT rules are included:

```
include "/etc/nftables/nat.nft"
```

Now open `nat.nft`:

```bash
sudo vi /etc/nftables/nat.nft
```

<img width="832" height="583" alt="image" src="https://github.com/user-attachments/assets/e21652bd-2dad-45a8-ba85-c74d9e1849f9" />

In `nat.nft`, under `set masq_interfaces`, set the elements to your head node's outbound ports, and under `set masq_ips`, set the elements to your network address(es).


Enable and start nftables:

```bash
sudo systemctl enable nftables
sudo systemctl restart nftables
sudo systemctl start nftables
```
#### Enable IP Forwarding
```bash
sudo vi etc/sysctl.d/99-custom.conf
add the line: net.ipv4.ip_forward = 1
```

#### Disable firewalld

```bash
sudo systemctl disable firewalld
sudo systemctl stop firewalld
sudo systemctl mask firewalld
```

### Ensuring Connectivity

If everything is set up properly, SSH into the compute nodes:

```bash
ssh 192.168.15.2
```

Then test connectivity:

```bash
ping google.com
ping 192.168.15.1
```

### Edit Hostnames

```bash
sudo vi /etc/hosts
```

<img width="153" height="43" alt="image" src="https://github.com/user-attachments/assets/6e692128-19a3-417c-b888-11fb0b54023f" />

### Final Step

After installation, run this command on each node to make sure the OS is fully updated:

```bash
sudo dnf update -y
```
