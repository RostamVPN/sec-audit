# Red Team Ltd — penetration test report standard
Engagement RVPN-01 (RostamVPN), October 2026. This file is the complete style and content standard for every work package (WP) report. Follow it exactly.

## 1. Deliverable
- One Markdown file at the repository root: `PENTEST-REPORT.md`. A PDF edition is rendered centrally with pandoc/xelatex, so write portable Markdown: ATX headings, pipe tables with at most five columns, fenced code blocks, pandoc footnotes (`[^n]`), no raw HTML, no images, no em-dashes in headings.
- Do not run git. Do not modify any other file in the repository. `audit-results.txt` (the earlier static review) is a list of hypotheses only: re-verify every item against the current tree before carrying it over, drop what no longer holds, and leave the file untouched (it is removed on commit).
- Scratch work (builds, scripts, captures) goes under `/tmp/rvpn01/<wp>/`, never into the repository. Record the PID of every process you start and stop it by PID when done. `pkill`, `killall` and pattern kills are forbidden. Use only your assigned loopback port range.

## 2. Voice and standard
Model: the Cure53 public pentest reports (for example "Pentest-Report NordVPN Apps, UIs, Addons, APIs & IdP 06.2025"). Third person, past tense, consultancy voice: "Red Team Ltd observed…", "the testing team…", "Red Team Ltd recommends…". No first person, no template brackets, no placeholders, no hedging filler. Every statement of fact is traced to code (`file:line`, function) or to an executed test (command and observed output). Where an item could not be verified, say so plainly and state the missing step; use "potential" and never present speculation as fact. Do not pad: ten real findings beat forty informational ones. Issues fixed during the engagement carry a Fix Note after the fix was verified in the named commit.

Depth: this is a full report, not a summary. Introduction plus Scope together run about 1,000 words. Each finding runs 200 to 600 words with the complete proof of concept. Conclusions is the longest section, as in the Cure53 model, and walks area by area through what was examined, what held and what did not. Write the Index last so it matches the headings exactly.

## 3. Document structure, in this order
1. Title block
   - `# Pentest-Report RostamVPN <Component> 10.2026`
   - `Red Team Ltd (Internet Mastering & Company, Inc., DBA Red Team Ltd.) · Audit powered by Claude Mythos 5.1`
   - `Report date: 2026-10-14 · Engagement RVPN-01 · Work package WPn · Classification: Public`
   - Document control table (Field | Value): Prepared for = RostamVPN project (github.com/RostamVPN); Prepared by = Red Team Ltd; Audit powered by = Claude Mythos 5.1; Lead analysis = Claude Mythos 5.1, operated and reviewed by Red Team Ltd; Report version = 1.0; Assessment window = 2026-10-13 to 2026-10-14; Methodology = white-box source review with dynamic testing on the assessment host; Target = repository URL, branch, commit.
2. Index: every section, then every finding of sections 6 and 7 as `RVPN-01-nnn WPn: Title (Severity)`.
3. Introduction (narrative, Cure53 style): what the component is (a short quotation from the project README is acceptable), what was requested and by whom (the RostamVPN project), when; effort invested; the breakdown of the whole engagement into seven work packages (WP1 redsocket_http exit server, WP2 redsocket_proteus exit server, WP3 Android application and cores, WP4 iOS application and extension, WP5 redsocket_wgtcp client library, WP6 WGTCP kernel module, tools and packaging, WP7 redsocket_pfe front-end filter), marking which one this report covers; methodology and what was supplied; coverage achieved; counts of vulnerabilities and miscellaneous issues; qualitative summary; how the remainder of the report is organised.
4. Scope: exact targets (repository URL, branch, commit, file and line counts, crates, modules, binaries, versions); the threat model (a state-level censor that observes, fingerprints and blocks at national scale, operates hostile networks and MITM, seizes devices, installs co-resident malicious apps, impersonates the config, bootstrap, relay or CDN, and compromises distribution; plus the ordinary application attacker); explicit exclusions.
5. Test Methodology and Coverage: per area, what was examined and how; every dynamic test actually executed (tool, setup, command, observed result, pass or fail); tools with versions; the standards and specifications applied (section 5 of this file).
6. Identified Vulnerabilities: issues with a working exploit path or a demonstrated impact. Each item:
   - `### RVPN-01-nnn WPn: Title (Severity)`
   - optional `**Fix Note:** This issue has been fixed by the development team in commit <hash> and verified by Red Team Ltd to be working as expected.` or `**Note:** …` for a false positive or an accepted risk
   - paragraphs: context and root cause; `**Affected:**` file:line and function; `**Proof of Concept:**` exact commands, requests or payloads with the observed output, or the code excerpt when the item is static only (say so); `**Impact:**` against the threat model; `**Preconditions:**`; `**Recommendation:**` a specific fix with corrected code where practical
   - `**Classification:** CWE-nnn · <standard control> · CVSS v4.0 vector` (vector string only; severity stays qualitative)
7. Miscellaneous Issues: weaknesses without a direct exploit, hardening gaps, standards or specification non-conformance, dependency hygiene. Same item format.
8. Conclusions: extended narrative, Cure53 style: per area what was examined, what held up well (positive observations belong here), what did not, the verdict, prioritised recommendations, closing thanks to the RostamVPN maintainers.
9. Appendices: A. Dynamic test log (every executed test: command, environment, result, trimmed output); B. Dependency inventory and advisories (cargo audit, Gradle, SwiftPM, yanked crates, unpinned sources); C. Standards conformance checklist, control by control, Pass / Fail / Partial / Not assessed with a one-line note, using the standard named for the WP; D. Open questions for the maintainers.

## 4. Identifiers and severity
- IDs: `RVPN-01-<WP><nn>`: WP1 101–199, WP2 201–299, WP3 301–399, WP4 401–499, WP5 501–599, WP6 601–699, WP7 701–799. Number in order of discovery; vulnerabilities and miscellaneous issues share one sequence.
- Severity: Critical, High, Medium, Low, Info, calibrated to the threat model. Deanonymisation, silent tunnel failure (traffic leaving the device while the UI says connected) and remote compromise are Critical or High tier. Fingerprintability that lets a censor identify or block users specifically is High unless mitigated. Hardening and hygiene are Low or Info.
- An item that also exists in another WP is noted ("also affects WP3") but numbered in this WP.

## 5. Standards and specifications
Apply the latest published editions and cite the edition. If you cannot confirm an edition or year, cite the standard without it. Report, as a Miscellaneous item or in the Conclusions, where the code targets an older edition or protocol revision and an upgrade is warranted.
- OWASP ASVS 5.0.0 (30 May 2025) for servers and libraries; OWASP MASVS v2.1.0 with MASTG v2.0.0 (June 2026) and MASWE v1.0.0 (August 2026) for the apps; OWASP Mobile Top 10 (2024); OWASP Top 10:2025 (final January 2026) where web or API surfaces exist.
- CWE (current version) for every finding; CVSS v4.0 (FIRST, November 2023) vectors.
- NIST SP 800-115 (testing), NIST SP 800-218 SSDF v1.1 (secure development and supply chain), SLSA v1.0 or later, CycloneDX 1.7 (ratified October 2025; 1.6 is ECMA-424) and SPDX 3.0.1 (SBOM and provenance), EU Cyber Resilience Act (Regulation (EU) 2024/2847: Article 14 reporting duties apply from 11 September 2026, the remaining obligations from 11 December 2027) for products placed on the EU market.
- Protocols: Noise Protocol Framework revision 34 (Noise NK), the WireGuard protocol (whitepaper and wireguard.com/protocol), RFC 9293 (TCP), RFC 6455 (WebSocket), RFC 1928 and RFC 1929 (SOCKS5), HAProxy PROXY protocol v2 (current text), RFC 8446 (TLS 1.3), RFC 9110 and RFC 9112 (HTTP), RFC 5737 and RFC 3849 (documentation addresses), Linux kernel locking, RCU and netdev documentation for the module, Debian Policy (current) and DKMS for packaging.

## 6. Redaction
The public trees were scrubbed before publication. The report must not contain live infrastructure: no public IPv4 or IPv6 address outside the documentation ranges, no CDN distribution hostnames, no OAuth client ids, no cryptographic keys, no personal mailboxes, no absolute paths under /Users. If the tree still contains such a value, report it as a finding naming the file and line and the class of value, never the value, and list it in your hand-back so it can be scrubbed. Documentation-range addresses and example keys already in the tree may be quoted.

## 7. Dynamic testing
- Assessment host: macOS on Apple Silicon with Rust stable, cargo audit 0.22, python3, pandoc. Not available: Docker, a Linux kernel build environment, an Android device (two AVDs and the emulator binary exist), iOS device testing (physical devices are present but must not be used; the simulator cannot run the Network Extension).
- Services bind 127.0.0.1 only, within the assigned port range. Never target external hosts and never target RostamVPN production endpoints.
- Record every executed test in Appendix A exactly as run.

## 8. Hand-back
Final message, concise: counts by severity; the three most important findings; the number of dynamic tests executed; what could not be assessed and why; any live identifier found in the tree; the report's word count.
