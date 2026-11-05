# DNS Information Gathering Using the Dig Command

## Overview

This lab explores the `dig` command, also known as **Domain Information Groper**, a command-line tool used to query DNS (Domain Name System) servers and retrieve information about domain names and IP addresses.

Dig is useful during information gathering and reconnaissance because it can retrieve DNS records such as A, MX, NS, TXT, CNAME, and PTR records. It can also query specific DNS servers, perform reverse lookups, and trace the DNS resolution path.

## Lab Objective

Learn how to use the Dig command to gather DNS information.

## Lab Purpose

Understand how to query DNS nameservers for information about host addresses, mail exchanges, nameservers, and other DNS records.

## Lab Environment

- **Operating System:** Kali Linux
- **Tool:** Dig
- **Target domains:** `google.com`, `microsoft.com`, `quora.com`

## Lab Walkthrough

### Task 1: Verify Dig Installation and Perform a Basic DNS Query

Dig is available on Linux and macOS and is commonly pre-installed on Kali Linux.

Check the installed version:

```bash
dig -v
```

General command syntax:

```bash
dig [@server] name [type]
```

Where:

- `@server` specifies the DNS server to query (optional).
- `name` specifies the domain name being queried.
- `type` specifies the DNS record type (optional).

Perform a basic DNS lookup:

```bash
dig www.google.com
```

The default query requests an A record. The standard output includes several sections, such as the question, answer, authority, and additional information, as well as query statistics.

### Task 2: Display Only the Query Result

Use the `+short` option to display a simplified result:

```bash
dig www.google.com +short
```

This removes most of the detailed output and displays the returned answer values.

A domain may resolve to multiple IP addresses, depending on its DNS configuration and response.

### Task 3: Display Only the Answer Section

To remove the other output sections while retaining the DNS answer, use:

```bash
dig www.google.com +noall +answer
```

This is useful when you need a cleaner output that includes the record name, type, TTL, and returned value.

### Task 4: Query a Specific DNS Server

Dig allows you to specify the DNS server that should handle a query.

```bash
dig @8.8.8.8 www.google.com
```

This sends the DNS query to Google's public DNS resolver at `8.8.8.8`.

The `@` symbol identifies the DNS server being queried.

### Task 5: Query the ANY Record Type

The `ANY` query type requests information associated with a domain name:

```bash
dig www.google.com ANY
```

An important distinction: an `ANY` query is **not guaranteed to return every DNS record** associated with a domain. DNS servers may return only a subset of available records or a minimal response.

For reliable enumeration, query the record types individually.

### Task 6: Query Specific DNS Record Types

Dig allows you to retrieve specific DNS records by supplying the record type.

For example, query the mail exchange records of a domain:

```bash
dig google.com MX
```

Common DNS record types include:

| Record | Purpose |
|---|---|
| A | Maps a hostname to an IPv4 address. |
| AAAA | Maps a hostname to an IPv6 address. |
| MX | Identifies mail exchange servers. |
| NS | Identifies authoritative nameservers. |
| TXT | Stores text-based DNS information. |
| CNAME | Identifies an alias for another hostname. |
| PTR | Provides reverse DNS information. |

Examples:

```bash
dig google.com A
dig google.com NS
dig google.com TXT
dig google.com MX
```

For a hostname that uses a CNAME record, you can query it directly:

```bash
dig www.google.com CNAME
```

The records returned depend on the domain's DNS configuration.

### Task 7: Trace the DNS Resolution Path

Dig can trace the DNS delegation process from the root nameservers through the DNS hierarchy.

The original command:

```bash
dig +short +trace microsoft.com
```

For a more detailed view of the delegation path, use:

```bash
dig +trace microsoft.com
```

The `+trace` option follows DNS referrals from the root nameservers toward the authoritative nameservers for the requested domain.

This traces the DNS resolution path, not the physical network hops between systems.

### Task 8: Perform a Reverse DNS Lookup

Dig can also perform DNS queries using an IP address.

```bash
dig -x 142.251.156.119
```

The `-x` option performs a reverse DNS lookup by querying the PTR record associated with the IP address.

When a PTR record exists, the response may identify a hostname associated with that address.

### Task 9: Perform Multiple DNS Lookups Using a File

Dig supports batch queries, allowing multiple domain names to be queried from a single file.

First, create a file containing the domains:

```bash
printf '%s\n' google.com microsoft.com quora.com > domain_names.txt
```

Verify the contents:

```bash
cat domain_names.txt
```

The file should contain:

```text
google.com
microsoft.com
quora.com
```

Run the DNS queries using the file:

```bash
dig -f domain_names.txt +short
```

Dig reads the domain names from the file and performs the queries individually.

To display structured answer sections instead of only the short results, use:

```bash
dig -f domain_names.txt +noall +answer
```

### Task 10: Retrieve DNS TXT Records

DNS TXT records can contain useful domain information, including SPF policies and domain ownership verification data.

Query the TXT records for Quora:

```bash
dig +short TXT quora.com
```

The command returns the TXT records published for the domain, when available.

TXT records are commonly used for:

- Domain ownership verification.
- Email authentication policies such as SPF.
- Other domain-specific verification and configuration data.

## Key Concepts Covered

Through this lab, the following concepts are explored:

- DNS information gathering using Dig.
- Basic DNS queries and simplified output.
- Querying specific DNS resolvers.
- Identifying common DNS record types.
- Understanding DNS `ANY` queries and their limitations.
- Tracing DNS delegation and resolution.
- Reverse DNS lookups using PTR records.
- Performing batch DNS queries using a file.
- Retrieving TXT records for domain information.

## Practical Applications in Cybersecurity

DNS enumeration is an important part of the reconnaissance phase of security assessments.

The information gathered can help security professionals:

- Identify IP addresses associated with domain names.
- Discover mail servers and authoritative nameservers.
- Understand DNS configurations.
- Identify publicly available domain verification and email authentication records.
- Investigate reverse DNS information.
- Support broader infrastructure and attack-surface mapping.

## Important Notes

- DNS responses can change depending on the domain's configuration, DNS resolver, and time of the query.
- An `ANY` query should not be treated as a complete DNS record enumeration method.
- A reverse DNS lookup may return no hostname when a suitable PTR record is unavailable.
- DNS enumeration should be performed within an authorized security assessment or other permitted scope.

## References

- [BIND 9 Dig Manual](https://bind9.readthedocs.io/en/latest/manpages.html)
- [RFC 8482: Providing Minimal-Sized Responses to DNS Queries That Have QTYPE=ANY](https://www.rfc-editor.org/rfc/rfc8482.html)
