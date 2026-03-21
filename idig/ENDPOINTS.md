# iDig API — Endpoint Reference

Base URL: `https://api.softricks.net/idig`

## DNS Lookup

- **GET `/`** — Core DNS lookup. Query any record type (A, NS, SOA, DS, TXT, CNAME, CAA, SRV, TLSA, HTTPS, SVCB, ANY, ALL) for a domain. Supports nameserver override and authoritative queries.

## DNS Security

- **GET `/dnssec/validate`** — Validate the DNSSEC chain of trust for a domain using `delv`. Reports secure/insecure/bogus status with detailed reason codes and remediation steps.
- **GET `/dnssec/health`** — DNSSEC key management report. Checks algorithm strength, key sizes, signature expiry, DS/DNSKEY consistency, and NSEC/NSEC3 zone walking exposure. Includes a full **chain of trust** (Root → TLD → Domain) with DNSKEY and DS records at each level, per-link integrity flags, and broken-link identification.
- **GET `/dane/validate`** — DANE/TLSA validation. Compares published TLSA records against the live TLS certificate to verify certificate pinning.

## Zone & Propagation

- **GET `/zone/consistency`** — Compare DNS zone data across all authoritative nameservers. Detects SOA serial mismatches, missing records, and stale secondaries.
- **GET `/zone/axfr`** — AXFR zone transfer vulnerability check. Tests each authoritative NS for public zone transfers that expose the entire DNS zone.
- **GET `/propagation`** — DNS propagation check across 8 global public resolvers (Google, Cloudflare, Quad9, OpenDNS, etc.). Detects inconsistent answers during DNS migrations.
- **GET `/ttl/check`** — TTL advisory and migration readiness. Flags TTLs that are too high for fast failover or too low for cache efficiency.

## Email Security

- **GET `/email/security`** — Full email authentication audit: SPF record analysis, DKIM key discovery across common and provider-specific selectors, DMARC policy check, and BIMI brand logo verification. Returns an A–F grade.
- **GET `/mx/check`** — MX record health check. Validates MX hostnames resolve, detects mail provider, checks for IP-based MX (RFC violation), CNAME on MX, and single-point-of-failure.

## Domain Intelligence

- **GET `/whois`** — Parsed WHOIS/RDAP data. Returns registrar, dates (created/expires/updated), nameservers, DNSSEC status, and registrant info. Uses RDAP as primary source with WHOIS fallback.
- **GET `/domain/status`** — EPP status flags and registrar lock check. Reports transfer lock, delete lock, update lock, and other registry-level holds.
- **GET `/geo`** — IP geolocation. Resolves domain to IPs and returns country, city, ISP, ASN, and hosting provider detection for each IP.
- **GET `/blacklist/check`** — DNSBL blacklist check. Queries 12+ major DNS blacklists (Spamhaus, Barracuda, SORBS, etc.) to see if a domain's IPs are listed.

## Infrastructure

- **GET `/ssl/check`** — SSL/TLS certificate check. Returns issuer, validity dates, SANs, chain depth, and days until expiry.
- **GET `/http/check`** — HTTP/HTTPS reachability. Tests both protocols, checks for HTTP-to-HTTPS redirect, and reports response codes and headers.
- **GET `/subdomains`** — Subdomain discovery. Probes ~70 common names in parallel and queries crt.sh Certificate Transparency logs. Returns resolved subdomains with IPs and CNAMEs.

## Diagnostics

- **GET `/resolve/check`** — Domain resolution health. Tests A/AAAA resolution, checks for NXDOMAIN, SERVFAIL, and timeout conditions.
- **GET `/diagnose`** — All-in-one diagnostic. Combines resolution checks and DNSSEC validation to answer: Are there resolution errors? Is DNSSEC validated? What should be fixed?

## Batch & Async

- **POST `/batch`** — Submit multi-domain, multi-check jobs asynchronously. Supports all 19 checks across up to 100 domains (plan-dependent). Returns a job ID for polling.
- **GET `/batch/{job_id}`** — Poll batch job results. Returns status (queued/processing/complete/partial) and results as they become available.

## Account

- **POST `/token`** — Create a new API token. Self-service for `api` and `mobile` scopes; `agent`/`mcp` scopes require admin secret.
- **GET `/usage`** — Check token usage, plan, monthly quota, and remaining requests.
- **GET `/health`** — Simple health check. Returns `{"ok": true}`.

## Admin (requires issuer secret)

- **GET `/admin/tokens`** — List all tokens with plan, scope, and usage data.
- **GET `/admin/super-usage`** — View tracked usage for super tokens.
- **PATCH `/admin/token`** — Update a token's plan, scope, allowed origins, or disabled status.
- **GET `/admin/stats`** — Aggregate usage statistics: total tokens, breakdown by plan/scope, top users.
