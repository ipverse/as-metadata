# as-metadata (formerly asn-info)

## 🔍 Try it online

Explore AS metadata interactively at **[Lens by ipverse](https://lens.ipverse.net)** — search IP addresses, prefixes, and autonomous systems without downloading anything.

## Overview

A comprehensive dataset of autonomous system metadata for all assigned ASNs (autonomous system numbers). 
Includes handle, organization name, and country code sourced from regional internet registries (RIR). 
Updated automatically when source data changes (checked daily).

Perfect for offline lookups, network analysis, threat intelligence, or any project where you need to map ASNs to organizations—no API rate limits, no external dependencies.

## Update notes

- **2026-03-04**: Added `metadata.lastModified` and `stats.prefixesLastModified` change-tracking timestamps; all timestamp fields now use date-only format (`yyyy-MM-dd`) to reduce file size
- **2026-02-08**: Added `category` (e.g. `isp`, `hosting`, `government_admin`) and `networkRole` (e.g. `tier1_transit`, `stub`) fields to AS metadata
- **2026-01-27**: Added `providerAsns` field with list of upstream transit provider ASNs
- **2026-01-18**: **Breaking change:** Replaced `upstreams`/`downstreams` with `providers`/`customers`/`peers` to accurately distinguish transit relationships from peering. Added `degree` and `reach` fields.
- **2026-01-08**: Added `registered` field (RIR registration date), `stats` section (prefix and connectivity statistics), and moved `lastAnnounced` to top level.
- **2026-01-03**: Repository renamed to `as-metadata`, CSV format changed to 4 columns (added country-code), JSON format added.
- **2025-08-03**: Removed opinionated handle cleanup and removed quotes around descriptions to improve RFC4180 compliance
- **2023-09-03**: Removed PEM certificates from description field

## Available formats

JSON (~55-60 MB) and CSV (~6 MB)

**JSON format:**
```json
[
  {
    "asn": 4711,
    "metadata": {
      "handle": "INTEC",
      "description": "INTEC Inc.",
      "countryCode": "JP",
      "country": "Japan",
      "origin": "authoritative",
      "category": "business",
      "networkRole": "stub",
      "registered": "1997-03-14",
      "lastModified": "2026-03-01"
    },
    "stats": {
      "ipv4": {
        "prefixes": 12,
        "prefixesAggregated": 8,
        "largestPrefix": 20
      },
      "ipv6": {
        "prefixes": 3,
        "prefixesAggregated": 2,
        "largestPrefix": 32
      },
      "connectivity": {
        "providers": 2,
        "providerAsns": [174, 3356],
        "customers": 5,
        "peers": 3,
        "degree": 10,
        "reach": 12
      },
      "prefixesLastModified": "2026-03-04"
    },
    "lastAnnounced": "2026-01-04"
  },
  {
    "asn": 4712,
    "metadata": {
      "handle": "JT-NET",
      "description": "JAPAN TOBACCO INC.",
      "countryCode": "JP",
      "country": "Japan",
      "origin": "authoritative",
      "category": null,
      "networkRole": null,
      "registered": "1997-03-14",
      "lastModified": null
    },
    "stats": null,
    "lastAnnounced": null
  }
]
```

**CSV format:**
```csv
asn,handle,description,country-code
0,IANA-RSVD-0,Internet Assigned Numbers Authority,US
1,LVLT-1,Level 3 Parent LLC,US
2,UDEL-DCN,University of Delaware,US
3,MIT-GATEWAYS,Massachusetts Institute of Technology,US
4,ISI-AS,University of Southern California,US
5,SYMBOLICS,WFA Group LLC,US
6,BULL-HN,ATOS IT Solutions and Services Inc.,US
7,DSTL,The Defence Science and Technology Laboratory,GB
.
.
.
```

## Field descriptions

### CSV fields

| Field | Description |
|---|---|
| `asn` | Autonomous System Number |
| `handle` | Registry handle/identifier |
| `description` | Organization name or description |
| `country-code` | ISO 3166-1 alpha-2 country code (`XX` if unknown) |

### JSON fields

| Field | Description |
|---|---|
| `asn` | Autonomous System Number |
| `metadata.handle` | Registry handle/identifier |
| `metadata.description` | Organization name or description |
| `metadata.countryCode` | ISO 3166-1 alpha-2 country code |
| `metadata.country` | Full country name |
| `metadata.origin` | Metadata source (see [origin values](#origin-values)) |
| `metadata.category` | AS classification; `null` if unclassified (see [AS category and network role](#as-category-and-network-role)) |
| `metadata.networkRole` | Network role; `null` if unknown (see [AS category and network role](#as-category-and-network-role)) |
| `metadata.registered` | RIR registration date (`yyyy-MM-dd`); `null` for inferred ASNs |
| `metadata.lastModified` | Date (`yyyy-MM-dd`) when any metadata field last changed (includes RIR data, category, network role, etc.); `null` if never announced |
| `stats` | Prefix and connectivity statistics; `null` for ASNs without route collector data |
| `stats.ipv4.prefixes` | Number of IPv4 prefixes announced |
| `stats.ipv4.prefixesAggregated` | Number of IPv4 prefixes after aggregation |
| `stats.ipv4.largestPrefix` | Largest IPv4 prefix announced (e.g. `/13` — smaller number = larger block) |
| `stats.ipv6.prefixes` | Number of IPv6 prefixes announced |
| `stats.ipv6.prefixesAggregated` | Number of IPv6 prefixes after aggregation |
| `stats.ipv6.largestPrefix` | Largest IPv6 prefix announced (e.g. `/29` — smaller number = larger block) |
| `stats.connectivity.providers` | Number of transit provider ASNs |
| `stats.connectivity.providerAsns` | List of transit provider ASNs (enables upstream quality analysis) |
| `stats.connectivity.customers` | Number of transit customer ASNs |
| `stats.connectivity.peers` | Number of settlement-free peer ASNs |
| `stats.connectivity.degree` | Total unique neighbor ASNs (providers + customers + peers) |
| `stats.connectivity.reach` | Customer cone size (ASNs reachable via customer relationships) |
| `stats.prefixesLastModified` | Date (`yyyy-MM-dd`) when announced prefixes last changed; only present when `stats` is non-null |
| `lastAnnounced` | Date (`yyyy-MM-dd`) when AS was last seen announcing prefixes; `null` if never seen |

### Origin values

| Value | Description |
|---|---|
| `authoritative` | From authoritative source |
| `inferred` | Inferred from routing information; may be inaccurate |
| `overlaid` | Curated correction applied over missing or wrong registry data |
| `none` | No metadata available |

## AS category and network role

### Category

Classifies the primary function of an autonomous system.

| Value | Description |
|---|---|
| `isp` | Internet service provider (broadband, DSL, cable, fiber, mobile) |
| `hosting` | Hosting, cloud, or content provider |
| `business` | Enterprise or non-profit organization |
| `education_research` | Educational or research institution |
| `government_admin` | Government entity or public administration |

`null` when unclassified.

Categorization is based on multiple signals and reflects the AS's *primary* function. It won't always be correct — many networks defy clean categorization (e.g. an ISP that also runs a hosting business, or a university with its own transit infrastructure). Take it as a useful default, not gospel.

### Network role

Describes the connectivity role of an autonomous system based on BGP topology.

| Value | Description |
|---|---|
| `tier1_transit` | Tier 1 transit provider with settlement-free global reach |
| `major_transit` | Major transit provider |
| `midsize_transit` | Mid-size transit provider |
| `access_provider` | Access or eyeball network |
| `content_network` | Content delivery network |
| `stub` | Stub AS with no transit customers |

`null` when unknown.

Role assignment is based on BGP connectivity metrics with opinionated thresholds. Reasonable people may disagree on where exactly to draw the line between "major" and "mid-size" transit, or when a network stops being a stub.

## How to use

Download the data directly:

**JSON:**
```bash
curl -O https://raw.githubusercontent.com/ipverse/as-metadata/master/as.json
```

**CSV:**
```bash
curl -O https://raw.githubusercontent.com/ipverse/as-metadata/master/as.csv
```

Or just clone the repo if that's your thing.

**Quick example (Python):**
```python
import json
with open('as.json') as f:
    data = {entry['asn']: entry for entry in json.load(f)}
print(data[4711]['metadata']['description'])  # INTEC Inc.
```

## Use cases
- Figure out who owns an ASN
- Enrich your IP intelligence tools
- Threat hunting and security research
- BGP analysis and network research
- Building dashboards or monitoring tools
- Offline lookups (no API rate limits to deal with)
- Pretty much anything where you need to map ASNs to operators

## Questions or issues?

Head over to the [feedback repository](https://github.com/ipverse/feedback) if you have questions, issues, or suggestions.

## License

This data is released under [CC0 1.0 Universal](LICENSE).
