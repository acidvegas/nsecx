# NSECX

###### Rsearch project on NSEC[3] walking for DNSSEC enabled Zones

## Work in progress: Come back later

The repository contains utilities for DNSSEC zone enumeration and subdomain discovery via NSEC/NSEC3 walking. It focuses on extracting and analyzing DNSSEC records for TLDs and specific target domains. Meant for educational purposes, security research, and sanctioned penetration testing, these tools aid in uncovering the underlying mechanisms of DNS security.

## Statistics
Based on my research at the time of writing this repository, after mapping 1,458 TLD zones, 89.78% use NSEC3, and 3.50% use NSEC, and 6.72% do not have DNSSEC features at all.

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
