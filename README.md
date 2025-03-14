# NSECX
> Research project on NSEC[3] walking for DNSSEC enabled Zones

![](./.screens/preview.gif)

## [Work in Progress]

The repository contains utilities for DNSSEC zone enumeration and subdomain discovery via NSEC/NSEC3 walking. It focuses on extracting and analyzing DNSSEC records for TLDs and specific target domains. Meant for educational purposes, security research, and sanctioned penetration testing, these tools aid in uncovering the underlying mechanisms of DNS security.

## DNSSEC Statistics
| Status                                   | Percentage | TLDs  |
| ---------------------------------------- | ---------- | ----- |
| [NSEC3](./dnssec_stats/nsec3.txt)        | 90%        | 1313 |
| [NSEC](./dnssec_stats/nsec.txt)          | 3%         | 51    |
| [NO DNSSEC](./dnssec_stats/nodnssec.txt) | 7%         | 98    |

###### These statistics are updated daily at midnight UTC via Github Actions.

## NSEC Pitfalls
- Results inconsistent, must hop dns servers on ALL issues to continue the crawl.
- Running into \000 *(null)* characters in sub-domains *(strange bind version [issue](https://gitlab.isc.org/isc-projects/bind9/-/issues/2779) missing "w" character in the charmap)*

- Running into *.domain.tld issues creates a crawling loop :
```
Next domain: myfreedom.auto.
Next domain: ne.auto.
Next domain: neom.auto.
Next domain: netdirector.auto.
Next domain: netprophet.auto.
Next domain: netto.auto.
Next domain: newjersey.auto.
Next domain: nexteer.auto.
Next domain: nextev.auto.
Next domain: nh.auto.
Next domain: nic.auto.
Next domain: *.nic.auto.
Next domain: _c311ff38bcd400b0adf7fa2b71732858.nic.auto.
Next domain: a.nic.auto.
Next domain: b.nic.auto.
Next domain: c.nic.auto.
Next domain: d.nic.auto.
Next domain: web1.nic.auto.
Next domain: web2.nic.auto.
Next domain: whois.nic.auto.
Next domain: _aa5536969dd3a62238209b6b2b750c1c.whois.nic.auto.
Next domain: www.nic.auto.
Next domain: _b529263a31adafb2e3be5d632e66c16b.www.nic.auto.
Next domain: nic.auto.
Next domain: *.nic.auto.
Next domain: _c311ff38bcd400b0adf7fa2b71732858.nic.auto.
Next domain: a.nic.auto.
Next domain: b.nic.auto.
Next domain: c.nic.auto.
Next domain: d.nic.auto.
Next domain: web1.nic.auto.
Next domain: web2.nic.auto.
Next domain: whois.nic.auto.
Next domain: _aa5536969dd3a62238209b6b2b750c1c.whois.nic.auto.
Next domain: www.nic.auto.
Next domain: _b529263a31adafb2e3be5d632e66c16b.www.nic.auto.
Next domain: nic.auto.
Next domain: *.nic.auto.
Next domain: _c311ff38bcd400b0adf7fa2b71732858.nic.auto.
```

## References
- https://www.rfc-editor.org/rfc/rfc5155.html

___

###### Mirrors for this repository: [acid.vegas](https://git.acid.vegas/nsecx) • [SuperNETs](https://git.supernets.org/acidvegas/nsecx) • [GitHub](https://github.com/acidvegas/nsecx) • [GitLab](https://gitlab.com/acidvegas/nsecx) • [Codeberg](https://codeberg.org/acidvegas/nsecx)
