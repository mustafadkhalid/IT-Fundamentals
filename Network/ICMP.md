# ICMP RFC 792

## ICMP Overview

- ICMP is an integral part of IP and should be implemented by every IP module.
- ICMP is used for several situations:
  - when datagram cannot reach destination
  - when gateway doesn't have buffer capacity to forward datagram
  - when gateway can direct host to follow shorter path
  - etc.

- ICMP purpose is to provide feedback about problems in the communication environment, not to make IP reliable. If reliability is required, higher layer like TCP in transport layer could be used.

- No ICMP message is sent about ICMP messages. This is because of infinite regress.

- ICMP is only sent about errors in handling fragment zero of fragmented datagrams because usually enough data exist in fragment zero.

- ICMP minimum size is 8 bytes.

---

## ICMP Error Reporting and Query Messages

### Error Reporting
- Destination Unreachable message
- Source Quench message
- Time Exceeded message
- Parameter Problem message
- Redirect
- etc.

### Query Messages
- Echo Request and Echo Reply message
- Timestamp Request and Timestamp Reply
- Information Request and Information Reply
- etc.

---

## ICMP Type Fields

- Type 0 and 8: Echo Reply and Echo Request
- Type 3: Destination Unreachable
- Type 4: Source Quench message
- Type 5: Redirect
- Type 11: Time Exceeded message
- Type 12: Parameter Problem message
- Type 13 and 14: Timestamp Request and Timestamp Reply
- Type 15 and 16: Information Request and Information Reply
- etc.

---

## ICMP Header Rules

- The first octet of the ICMP message is the Type field.
- Any field marked "unused" must be set to zero when sent.
- Receiver must ignore unused fields except for checksum processing.

---

# ICMP Type 3: Destination Unreachable Message

### Format

| Field | Description |
|------|-------------|
| Type | 3 |
| Code | Reason for unreachable |
| Checksum | Error detection |
| Unused | Must be zero |
| Data | IP header + 64 bits of original datagram |

### Codes

- 0 → net unreachable
- 1 → host unreachable
- 2 → protocol unreachable
- 3 → port unreachable
- 4 → fragmentation needed (DF flag in IP header)
- 5 → source route failed

Code 1 and 2 may be sent by host. Code 0, 1, 4, 5 may be sent by gateway.

### Description

- Sent when network is unreachable.
- Sent when host is unreachable.
- Sent when protocol or port is unreachable.
- Sent when fragmentation is required but DF flag is set.

---

# ICMP Type 11: Time Exceeded Message

### Format

| Field | Description |
|------|-------------|
| Type | 11 |
| Code | Reason for timeout |
| Checksum | Error detection |
| Unused | Must be zero |
| Data | IP header + 64 bits |

### Codes

- 0 → TTL exceeded (gateway)
- 1 → fragment reassembly time exceeded (host)

### Description

- TTL is decremented at each router.
- If TTL becomes 0, packet is dropped.
- Host may drop packet if fragments do not arrive in time.
- No message sent if fragment zero is missing.

---

# ICMP Type 12: Parameter Problem Message

### Format

| Field | Description |
|------|-------------|
| Type | 12 |
| Code | Error type |
| Checksum | Error detection |
| Pointer | Location of error |
| Unused | Reserved |
| Data | IP header + 64 bits |

### Pointer

Indicates exact byte where error occurred in IP header.

### Pointer Map

- 0 → version + IHL
- 1 → TOS
- 2–3 → total length
- 4–5 → identification
- 6–7 → flags + fragment offset
- 8 → TTL
- 9 → protocol
- 10–11 → checksum
- 12–15 → source address
- 16–19 → destination address
- 20+ → options

### Description

- Sent when IP header is malformed.
- Sent when invalid options exist.
- Packet is discarded before ICMP is generated.

---

# ICMP Type 5: Redirect Message

### Format

| Field | Description |
|------|-------------|
| Type | 5 |
| Code | Redirect type |
| Checksum | Error detection |
| Gateway Address | Better next hop |
| Data | IP header + 64 bits |

### Codes

- 0 → network
- 1 → host
- 2 → TOS + network
- 3 → TOS + host

### Description

- Sent by gateway when better route exists.
- Host is informed to use another gateway.
- Original packet is still forwarded.
- Not sent if source routing is used.

---

# ICMP Type 0 and 8: Echo Messages

### Format

| Field | Description |
|------|-------------|
| Type | 0 or 8 |
| Code | 0 |
| Checksum | Error detection |
| Identifier | Session ID |
| Sequence Number | Packet order |
| Data | Payload |

### Types

- 8 → Echo Request
- 0 → Echo Reply

### Description

- Used by ping.
- Data must be returned unchanged.
- Identifier matches request/reply.
- Sequence number increments per request.
- IP source and destination are reversed in reply.
