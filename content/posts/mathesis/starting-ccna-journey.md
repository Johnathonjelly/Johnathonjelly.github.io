+++
date = '2025-09-18T16:59:38-07:00'
draft = false
title = "Lorem Ipsum Dolor Sit Amet"
description = 'Key learnings and insights from studying network fundamentals'
categories = ['Mathesis']
tags = ['CCNA', 'Networking', 'Study Notes', 'Chapter Summary']
+++

Starting my journey through the CCNA Official Cert Guide, documenting key concepts and insights from each chapter.

## What I Learned This Chapter

### OSI Model Deep Dive
- **Layer 1 (Physical)**: Electrical signals, cables, hubs
- **Layer 2 (Data Link)**: Frames, MAC addresses, switches
- **Layer 3 (Network)**: IP addresses, routing, routers
- **Layer 4 (Transport)**: TCP/UDP, port numbers

### TCP/IP Model vs OSI
The practical 4-layer TCP/IP model maps to OSI:
- **Network Access** (OSI Layers 1-2)
- **Internet** (OSI Layer 3)
- **Transport** (OSI Layer 4)
- **Application** (OSI Layers 5-7)

### Key Protocols by Layer
```
Application: HTTP, HTTPS, FTP, SMTP, DNS
Transport: TCP, UDP
Network: IP, ICMP, ARP
Data Link: Ethernet, Wi-Fi
```

## Practice Lab
Set up packet capture to observe different protocol headers:
- Ethernet frame structure
- IP packet headers
- TCP segment analysis

## Chapter Questions I Got Wrong
1. **Question**: What layer does a router primarily operate at?
   - **My Answer**: Layer 2 ❌
   - **Correct**: Layer 3 ✅
   - **Why**: Routers make forwarding decisions based on IP addresses (Layer 3)

## Next Chapter Preview
Chapter 2 covers Ethernet LANs and switching fundamentals.

*Part of my Mathesis series - documenting rigorous learning and study sessions.*