# NSECX
> Research project on NSEC/NSEC3 walking for DNSSEC enabled zones

![](./.screens/preview.gif)

## Overview
DNSSEC zone enumeration via NSEC & NSEC3 walking. This can be used to enumerate every single name in a domain's zone, including entire TLD zones. Automatically detects the DNSSEC type and proceeds accordingly.

- **NSEC** zones are walked by following the chain and reconstructing the zone file in RFC 1035 format.

- **NSEC3** zones are enumerated by collecting hashed records into NDJSON for offline cracking.

There may be more efficient ways to do this. This project is a simple proof of concept designed to make the methodology clear for people to learn from.

## DNSSEC Statistics by TLDs

A `tldsec` script is also included that generates a DNSSEC statistics report for all TLDs *(see [dnssec_report.ndjson](dnssec_report.ndjson), last generated 2026-03-21)*.

| DNSSEC Status | TLDs | Percentage |
| ------------- | ---- | ---------- |
| NSEC3         | 1305 | 90.8%      |
| NSEC          | 53   | 3.7%       |
| No DNSSEC     | 78   | 5.4%       |

## NSEC Walking
NSEC records form a linked chain of every name in a zone, sorted in canonical order. Each NSEC record points to the next name that exists. By querying a name's NSEC record and following the chain, you can enumerate every name in the zone from start to finish. The chain wraps back to the zone apex when the walk is complete.

```
$ dig +short ripe.net NS
ns3.lacnic.net.
ns3.afrinic.net.
ns4.apnic.net.
manus.authdns.ripe.net.
rirns.arin.net.

$ dig +short ns3.lacnic.net A
200.3.13.14

$ dig +short @200.3.13.14 ripe.net NSEC
1password-bridge-1.ripe.net. A NS SOA MX TXT AAAA RRSIG NSEC DNSKEY CAA

$ dig +short @200.3.13.14 1password-bridge-1.ripe.net NSEC
_acme-challenge.1password-bridge-1.ripe.net. A AAAA RRSIG NSEC

$ dig +short @200.3.13.14 _acme-challenge.1password-bridge-1.ripe.net NSEC
_00489a358ae3e70d565ae6ef7ddaa39d.ripe.net. CNAME RRSIG NSEC

...chain continues until it wraps back to ripe.net, completing the walk
```

## NSEC3 Walking
NSEC3 replaced NSEC to prevent direct zone enumeration by hashing the owner names. Instead of revealing plaintext names, NSEC3 records contain hashed names and point to the next hash in the chain. By querying random non-existent names, the authoritative server returns NSEC3 records in denial-of-existence responses. Collecting enough of these responses reveals all the hashes in the zone, which can then be cracked offline with a dictionary or brute force to recover the original names.

```
$ dig +short ac NS
a0.nic.ac.
a2.nic.ac.
b0.nic.ac.
c0.nic.ac.

$ dig +short a0.nic.ac A
65.22.160.1

$ dig +noall +authority +dnssec @65.22.160.1 doesnotexist.ac A
89L2PMI49P4NKDQKVOUN1DU1MLGH481V.ac. 3600 IN NSEC3 1 1 0 73 8A4STVTE8MVBGVSG7HLMDRQ9GEUANRR4 NS SOA RRSIG DNSKEY NSEC3PARAM

$ dig +noall +authority +dnssec @65.22.160.1 zzzznotreal.ac A
EFUMREL3KGT3GMAEFPAUALNNN64NPAML.ac. 3600 IN NSEC3 1 1 0 73 EI4FMHPDUQ8NEIGK8VEIATEDQUG12DF6 A AAAA RRSIG
```
Each query for a non-existent name returns NSEC3 records proving the name doesn't exist, leaking hashed names in the process. Repeat with random queries until no new hashes appear.

The NSEC3 record fields `1 1 0 73` are the NSEC3 parameters:
- **Algorithm** (`1`) — hash algorithm, 1 = SHA-1
- **Flags** (`1`) — opt-out flag, 1 = unsigned delegations may be omitted
- **Iterations** (`0`) — number of additional times the hash is applied, 0 = just once
- **Salt** (`73`) — hex-encoded salt appended before hashing

The hash is computed as `SHA1(salt + SHA1(salt + name))` repeated for the iteration count. These parameters are needed to crack the hashes offline with tools like hashcat *(mode 8300)*.

## Usage
```sh
# Walk a single domain
./nwalk <domain>

# Walk a list of domains
cat domain_list.txt | ./nwalk

# Walk a list of domains in parallel
parallel -a domain_list.txt -j 10 ./nwalk

# Walk all TLDs
curl -s 'https://data.iana.org/TLD/tlds-alpha-by-domain.txt' | tail -n +2 | tr '[:upper:]' '[:lower:]' | ./nwalk
```

## References
- [RFC 1035 - Domain Names: Implementation and Specification](https://www.rfc-editor.org/rfc/rfc1035.html)
- [RFC 4034 - Resource Records for the DNS Security Extensions](https://www.rfc-editor.org/rfc/rfc4034.html)
- [RFC 4035 - Protocol Modifications for the DNS Security Extensions](https://www.rfc-editor.org/rfc/rfc4035.html)
- [RFC 5155 - DNS Security (DNSSEC) Hashed Authenticated Denial of Existence](https://www.rfc-editor.org/rfc/rfc5155.html)
- [RFC 7129 - Authenticated Denial of Existence in the DNS](https://www.rfc-editor.org/rfc/rfc7129.html)

___

###### Mirrors for this repository: [SuperNETs](https://git.supernets.org/acidvegas/nsecx) • [GitHub](https://github.com/acidvegas/nsecx) • [GitLab](https://gitlab.com/acidvegas/nsecx) • [Codeberg](https://codeberg.org/acidvegas/nsecx)
