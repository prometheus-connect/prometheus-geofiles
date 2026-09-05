# prometheus-geofiles

Trimmed V2Ray routing rules for Russia, rebuilt every six hours.

Drop-in replacements for the usual `geoip.dat` / `geosite.dat`, carrying only
the categories a real profile references. Built for [Happ](https://happ.su) on
iOS, but they are ordinary V2Ray files and work anywhere the format is read —
v2rayNG, Xray, sing-box.

## Files

| File | Size | Categories |
|---|---|---|
| `geoip.dat` | 1.38 MB | private, ru-blocked, ru-blocked-community, re-filter, ru-whitelist |
| `geosite.dat` | 1.53 MB | private, ru-blocked, category-ads, category-gov-ru |

```
https://raw.githubusercontent.com/prometheus-connect/prometheus-geofiles/main/geoip.dat
https://raw.githubusercontent.com/prometheus-connect/prometheus-geofiles/main/geosite.dat
```

The same two files are published to `auth.prometheus.info.gf/api/geo/`, and that
is what our own clients read. `raw.githubusercontent.com` does not answer from
retail Russian ISPs — from a Rostelecom line DNS resolves but TCP 443 never
establishes — and a client that cannot fetch its routing rules never starts its
tunnel. Both locations are written by the same build and are byte-identical.

## Why trimmed

Not to save bandwidth. On iOS the tunnel runs inside a Network Extension with a
memory ceiling around 50 MB, and the upstream `geosite.dat` is 73 MB of
protobuf — it cannot be parsed there at any download speed. Even the common
`ru-only` build is 5.4 MB, three quarters of which is an ad-domain list that a
profile with an empty `BlockSites` never consults.

Keeping only what a profile names brings that to about 1.5 MB, with no loss of
coverage in the categories that are kept: `ru-blocked` here holds the same
domains as upstream, to the entry.

## Sources

- IP: [runetfreedom/russia-blocked-geoip](https://github.com/runetfreedom/russia-blocked-geoip)
- Domains: [runetfreedom/russia-v2ray-rules-dat](https://github.com/runetfreedom/russia-v2ray-rules-dat)

Both are consumed as published; nothing is added, removed or reordered inside a
category. Every build is read back with an independent parser and the entry
counts compared against the source before it is pushed — a file a client cannot
parse would otherwise only reveal itself on someone's phone.

## Freshness

Rebuilt every six hours. Upstream republishes at least daily, so a weekly cadence
would quietly let a newly blocked site route direct.

`main` is force-pushed as a single commit: a pair of binaries rebuilt six times a
day would otherwise grow this repository without bound. Clone with `--depth 1`.

Built 2026-09-05 15:47 UTC.

## Licence

The rule data belongs to its upstream authors under their own terms. The build
script is MIT.
