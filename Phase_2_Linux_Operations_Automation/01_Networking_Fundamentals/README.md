# Build 01: Networking Fundamentals

## Status

Complete: Days 1-7

## Objective

Understand how Linux systems communicate and build a verified inventory of the three-server lab.

## Skills Demonstrated

- Identify hostnames, IPv4 addresses, MAC addresses, gateways, and interfaces
- Test server-to-server and Internet connectivity with `ping`
- Inspect listening services with `ss`
- Resolve hostnames with `dig`
- Read routing decisions with `ip route`
- Inspect the path to an external host with `traceroute`
- Record operational findings and unresolved symptoms

## Lab Result

All three servers share the `192.168.146.0/24` network and use `192.168.146.2` as the default gateway. The servers successfully reached one another in the captured tests, and each host was listening on SSH port 22 and HTTP port 80.

The detailed evidence and findings are documented in the [Day 7 Network Inventory Report](Day_07_Network_Inventory_Report.md).
