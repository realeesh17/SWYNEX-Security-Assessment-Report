# SWYNEX Network Security Analysis

## 1. Objective

To analyse network traffic in a controlled Kali Linux environment using Wireshark and TShark, identify observed protocols, understand normal network communication, and document potential security concerns with appropriate mitigations.

## 2. Scope and Authorization

Testing was performed in a personal lab using Kali Linux, VMware Workstation, and a locally hosted OWASP Juice Shop application. Traffic was generated using standard diagnostic and client commands.

The analysis was limited to the user's own lab and ordinary network traffic generated for learning purposes.

## 3. Tools Used

- Kali Linux
- Wireshark
- TShark
- curl
- ping
- nslookup
- Docker
- OWASP Juice Shop

## 4. Methodology

1. Captured ICMP traffic on the loopback interface.
2. Generated DNS queries and inspected query and response packets.
3. Captured TCP and TLS traffic associated with an HTTPS connection.
4. Generated an HTTP HEAD request to the locally hosted Juice Shop application.
5. Inspected the HTTP response and its headers.
6. Used TShark protocol hierarchy statistics to review saved PCAPNG files.

## 5. Observations

### 5.1 ICMP Loopback Traffic

Four ICMP echo requests and four replies were observed for a ping to 127.0.0.1. The command reported 0% packet loss.

**Evidence:** `evidence/01-icmp-loopback.png`

**Interpretation:** Local loopback communication was successful. This does not establish external network connectivity.

### 5.2 DNS Traffic

DNS queries and responses were observed between the Kali VM and its configured DNS resolver. The captured traffic included A and AAAA queries.

**Evidence:** `evidence/02-dns-query-response.png`

**Interpretation:** DNS was used to request domain-name information. DNS traffic may reveal queried domain names to parties able to observe unencrypted DNS traffic.

**Recommendation:** Use an appropriately configured encrypted DNS service where suitable, and apply network monitoring and access controls.

### 5.3 HTTPS and TLS Traffic

TCP traffic to destination port 443 and TLS records, including a Client Hello, were observed in Wireshark.

**Evidence:** `evidence/03-https-tls-analysis.png`

**Interpretation:** The capture shows TLS communication consistent with an HTTPS connection. IP addresses, ports, packet sizes, and timing can remain visible even when application data is encrypted.

**Recommendation:** Use properly configured HTTPS/TLS, validate certificates, and keep TLS implementations updated.

### 5.4 Local HTTP Request and Response

A HEAD request to `http://127.0.0.1:3000` and a corresponding `HTTP/1.1 200 OK` response were observed.

**Evidence:** `evidence/04-http-request-response.png`

**Interpretation:** The local Juice Shop application responded successfully. The request and response headers were readable in the packet capture.

**Recommendation:** Use HTTPS with appropriate TLS configuration when transmitting sensitive information across networks.

### 5.5 HTTP Response Headers

The response included `X-Content-Type-Options: nosniff` and `X-Frame-Options: SAMEORIGIN`. It also included `Access-Control-Allow-Origin: *`.

**Evidence:** `evidence/05-http-response-headers.png`

**Interpretation:** The observed response contains headers that provide browser-side protections. The wildcard CORS header warrants review against the application's intended cross-origin access requirements, but it does not independently prove a vulnerability.

**Recommendation:** Review CORS policies against business requirements and restrict permitted origins when appropriate. Verify security headers against the application's deployment requirements.

### 5.6 TCP Connection Establishment

A TCP SYN packet to destination port 443 was observed, consistent with the initiation of a TCP connection.

**Evidence:** `evidence/06-tcp-syn-analysis.png`

**Interpretation:** A SYN packet is normal TCP connection-establishment behaviour and is not, by itself, evidence of an attack.

**Recommendation:** Monitor unusual connection patterns and investigate repeated or abnormal connection attempts in their wider context.

## 6. Risk Assessment

This exercise demonstrated normal protocol behaviour and potential areas for defensive review. The available evidence does not establish a confirmed exploitable vulnerability or malicious network activity.

| Observation | Assessment |
|---|---|
| ICMP loopback requests and replies | Informational |
| DNS queries and responses | Informational; privacy depends on DNS configuration |
| TCP/TLS communication | Informational |
| Local HTTP without TLS | Potential transport-confidentiality concern if used across an untrusted network |
| Wildcard CORS response header | Requires application-specific review |
| TCP SYN packet | Normal connection behaviour |

## 7. Limitations

- Captures represent a limited period of activity in a lab environment.
- Background traffic was also present in some capture files.
- The loopback HTTP test does not establish that the service is externally exposed.
- A protocol hierarchy alone does not prove malicious activity or a vulnerability.
- No confirmed security vulnerability was established by these observations.

## 8. Conclusion

The exercise provided practical experience in packet capture, protocol filtering, DNS analysis, TCP connection inspection, TLS traffic analysis, and HTTP header review using Wireshark and TShark.

The observations were documented with screenshots and saved packet captures. Any potential security concerns were treated as items for further review rather than confirmed vulnerabilities without supporting evidence.

## 9. Evidence and Captures

Screenshots are stored in the `evidence/` directory, and PCAPNG files are stored in the `captures/` directory.

The packet captures can be reopened in Wireshark for independent inspection of the recorded traffic.