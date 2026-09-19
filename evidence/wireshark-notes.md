# Wireshark Traffic Analysis Notes

Observed in a controlled Kali <-> Metasploitable 2 capture:

- ARP address resolution
- ICMP echo request/reply traffic
- TCP three-way handshake: SYN -> SYN/ACK -> ACK
- HTTP GET / HTTP/1.1
- HTTP/1.1 200 OK

Security observation: HTTP application traffic was visible in clear text because the service used unencrypted HTTP on TCP/80. Sensitive web traffic should use HTTPS/TLS.
