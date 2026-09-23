# Day 7 Network Inventory Report

## Executive Summary

This report records the Week 1 practical mission for Phase 2. Three Ubuntu virtual machines were inventoried, tested for connectivity, and inspected for listening services, DNS behavior, and routing.

The lab is operating on a shared VMware network, `192.168.146.0/24`. All three systems use `192.168.146.2` as their default gateway. The captured tests show successful communication between the servers, successful Internet reachability, and successful hostname resolution for Google from the tested hosts.

Two investigation notes remain important:

- The first failed ping used `192.168.101`, which is not a host in the lab subnet. The shell resolved it as `192.168.0.101`; this was an invalid target, not evidence that the lab network was down.
- The fake-domain DNS test returned a timeout from the local resolver, rather than the expected `NXDOMAIN`. This should be retested during the next troubleshooting session.

## 1. Lab Topology

```text
[bio-lab / ubuntu-lab-1: 192.168.146.101] -- ens33 --+
[ubuntu-lab-2: 192.168.146.102] ----------- ens33 --+-- [Gateway: 192.168.146.2] -- [Internet]
[ubuntu-monitor: 192.168.146.103] --------- ens33 --+
```

Network mode was not explicitly captured in the terminal evidence. The VMware MAC addresses and shared `192.168.146.0/24` network indicate a common VMware virtual network.

## 2. IP and Host Inventory

| Lab role | Hostname | IPv4 address | Interface | MAC address | Default gateway |
|---|---|---|---|---|---|
| Primary server | `bio-lab` | `192.168.146.101` | `ens33` | `00:0c:29:dc:90:38` | `192.168.146.2` |
| Secondary server | `ubuntu-lab-2` | `192.168.146.102` | `ens33` | `00:50:56:23:b8:6c` | `192.168.146.2` |
| Operations workstation | `ubuntu-monitor` | `192.168.146.103` | `ens33` | `00:50:56:35:f6:5b` | `192.168.146.2` |

## 3. Connectivity Results

| Source | Destination | Result | Evidence |
|---|---|---:|---|
| `bio-lab` | `192.168.146.102` | PASS | 3/3 replies, 0% loss |
| `bio-lab` | `192.168.146.103` | PASS | 3/3 replies, 0% loss |
| `ubuntu-lab-2` | `192.168.146.101` | PASS | 3/3 replies, 0% loss |
| `ubuntu-lab-2` | `192.168.146.103` | PASS | 3/3 replies, 0% loss |
| `ubuntu-monitor` | `192.168.146.101` | PASS | 3/3 replies, 0% loss |
| `ubuntu-monitor` | `192.168.146.102` | PASS | 3/3 replies, 0% loss |
| `bio-lab` | `192.168.146.2` | PASS | 3/3 replies, 0% loss |
| All tested hosts | `8.8.8.8` | PASS | Replies received from each captured test |
| All tested hosts | `google.com` | PASS | Name resolved and replies received |
| `bio-lab` | `192.168.101` | INVALID TARGET | 100% loss; resolved to `192.168.0.101` |

The three server addresses were mutually reachable in the captured tests. A complete gateway matrix was not captured from every source, so those rows should be repeated if a fully symmetrical matrix is required.

## 4. Listening Port Inventory

The `sudo ss -tulnp` output showed the following common services on all three systems:

| Port | Protocol | Bind address | Process | Interpretation |
|---:|---|---|---|---|
| 22 | TCP | `0.0.0.0` and `[::]` | `sshd` | SSH listening on IPv4 and IPv6 |
| 80 | TCP | `0.0.0.0` and `[::]` | `nginx` | HTTP listening on IPv4 and IPv6 |
| 53 | TCP/UDP | `127.0.0.53`, `127.0.0.54` | `systemd-resolved` | Local DNS stub resolver |
| 323 | UDP | Loopback only | `chronyd` | Local time synchronization service |

### Port answers

1. Nginx was listening on port 80 on all three captured hosts, not only `bio-lab`.
2. Yes. All three hosts were listening on SSH port 22 over IPv4 and IPv6.
3. No listener on port 443 was shown, so HTTPS was not enabled in this capture.
4. UDP port 53 was bound by `systemd-resolved` on loopback addresses.

## 5. DNS Findings

Captured `dig +short` results from `bio-lab` included:

| Name | Result |
|---|---|
| `google.com` | Multiple A records, including `142.250.151.101`, `142.250.151.102`, `142.250.151.100`, `142.250.151.139`, `142.250.151.113`, and `142.250.151.138` |
| `github.com` | `140.82.121.4` |
| `ubuntu.com` | `185.125.190.20`, `185.125.190.21`, `185.125.190.29` |
| `thisdomaindoesnotexist12345.com` | Resolver timeout from `127.0.0.53:53` |

The nameserver address was not recorded from `/etc/resolv.conf`; the error identifies the local `systemd-resolved` stub at `127.0.0.53`. A successful `dig +short` returns one or more resource records. A nonexistent name should normally return no records with an `NXDOMAIN` status, while a timeout indicates that the resolver did not answer in time and is a different failure mode.

## 6. Routing Findings

All three hosts reported the same routing pattern:

```text
default via 192.168.146.2 dev ens33 proto static
192.168.146.0/24 dev ens33 proto kernel scope link src <host-ip>
```

For `8.8.8.8`, each host selected the gateway and interface shown below:

```text
8.8.8.8 via 192.168.146.2 dev ens33 src <host-ip>
```

Traceroute from `bio-lab` and `ubuntu-monitor` reached the gateway as hop 1, then displayed no responses for hops 2-5. This commonly means intermediate devices or the destination are filtering traceroute probes; it does not contradict the successful ping results. The `ubuntu-lab-2` traceroute could not begin because `google.com` temporarily failed name resolution at that moment.

### Routing answers

- Interface used for Internet traffic: `ens33`
- First hop: `192.168.146.2`
- Google was not fully traced within five hops in the captured output; only the gateway responded.

## 7. Problems and Diagnostic Interpretation

| Observation | Interpretation | Follow-up |
|---|---|---|
| Ping to `192.168.101` failed | Incorrect address format and wrong subnet; it resolved to `192.168.0.101` | Use the documented `192.168.146.x` addresses |
| Fake domain lookup timed out | Local resolver did not answer; this is not confirmed `NXDOMAIN` | Repeat `dig`, inspect `/etc/resolv.conf`, and test a known resolver if needed |
| `ubuntu-lab-2` traceroute could not resolve Google | Temporary DNS failure at command time | Retest DNS and traceroute separately |
| Traceroute stopped responding after hop 1 | Intermediate ICMP/traceroute replies were filtered or suppressed | Compare with `tracepath` or TCP-based testing in a later lab |

## 8. Acceptance Checklist

- [x] All three VMs identified with hostname, IP, MAC, and gateway
- [x] Server-to-server connectivity verified in captured tests
- [x] Internet reachability verified with `8.8.8.8`
- [x] Hostname resolution verified with `google.com`
- [x] Listening ports documented for all three VMs
- [x] Routes documented for all three VMs
- [x] Topology documented
- [x] Problems and diagnostic meaning recorded
- [ ] Complete gateway ping matrix captured from every VM
- [ ] `/etc/resolv.conf` nameserver list captured
- [ ] Fake-domain lookup repeated until `NXDOMAIN` or a confirmed resolver diagnosis is recorded

## 9. Reflection

I completed the Week 1 networking mission by inventorying three Ubuntu systems on a shared `192.168.146.0/24` network. I verified server-to-server communication, Internet reachability, DNS resolution, listening services, and default routes. The most important troubleshooting lesson was separating an invalid target address and a resolver timeout from a genuine network outage. The next step is to begin Week 2: SSH, remote administration, file transfers, firewall rules, and packet observation.
