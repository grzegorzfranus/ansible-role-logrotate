# LLM Rule: Authoring Release Notes (Enterprise Grade)

This standard defines a universal, repository-agnostic format for professional software release notes. It follows semantic versioning, requires real dates, and mandates a consistent section order. Emojis are allowed only in section headings to aid scannability.

---

### Global rules
- English-only; concise, neutral, and precise. Active voice; avoid marketing language.  
- Semantic versions only: `X.Y.Z` (no `v` prefix). Examples: `1.2.3`, `2.0.0`.  
- Dates must be real and in ISO format: `YYYY-MM-DD`. Do not use placeholders (e.g., "TBD").  
- Emojis may appear in headings only; never in body text or bullets.  
- Use backticks for file paths, configuration keys, CLI flags, and code identifiers.  
- If information is unknown, explicitly state "None" or "Not applicable"; do not fabricate.  
- If any Breaking Changes exist, include both Upgrade and Rollback sections with actionable steps.  

---

### Required metadata (top block)
- Version: `X.Y.Z`  
- Date: `YYYY-MM-DD`  
- Release type: `Major | Minor | Patch` (derived from content)  
- Scope (optional): component(s) or product area (e.g., `API`, `CLI`, `UI`)  

---

### Section order (strict)
1) Title block (version, date, type, optional scope)  
2) ## 🧭 Executive summary — 2–4 sentences: purpose, value, risk areas  
3) ## 🌟 Highlights — 3–7 bullets (most impactful items)  
4) ## ⚠️ Breaking changes — impact and action required  
5) ## ⏳ Deprecations — timelines and planned removal version  
6) ## ❌ Removed — features/options removed; migration notes  
7) ## 🛡 Security — vulnerabilities fixed, hardenings (link CVEs/advisories)  
8) ## ✅ Added — new features/capabilities  
9) ## 🔄 Changed — behavior/defaults/API/UX/performance  
10) ## 🔧 Fixed — defects resolved (group by area if helpful)  
11) ## 🔗 Compatibility — supported platforms/versions/tooling  
12) ## 📈 Upgrade guide — step-by-step migration (required if breaking)  
13) ## ↩️ Rollback — safe revert steps and post-rollback validation  
14) ## ℹ️ Known issues — with mitigations/workarounds; write "None" if empty  
15) ## 📚 Documentation & References — notable docs changes and links to issues/PRs  

Always include sections in this order. If a section has no content, add a single line `None` (except "Upgrade guide" and "Rollback", which may be omitted only if there are no breaking changes).  

---

### Style and content rules
- Be factual, measurable, and testable. Prefer concrete numbers and exact names.  
- Keep bullets short; one idea per bullet. Avoid nested bullets unless essential.  
- Use consistent terminology and tense; past tense for completed work, imperative for instructions.  
- Mention user-visible impact first; include operator/administrator guidance when relevant.  
- For security, include identifiers (e.g., `CVE-YYYY-XXXX`), severity, and scope.  
- For deprecations, specify removal version and recommended alternatives.  
- For compatibility, list minimum supported versions explicitly (e.g., `Kubernetes >= 1.28`).  

---

### Deriving release type
- **Major**: any breaking change in public surface or explicit deprecation reaching removal.  
- **Minor**: backward-compatible features or non-breaking behavior changes.  
- **Patch**: bug fixes and small, backward-compatible adjustments only.  

---

### Acceptance checklist
- Title block present with semantic version and ISO date.  
- Sections included in the exact order; headings may include emojis, body text does not.  
- Breaking changes accompanied by Upgrade guide and Rollback.  
- Known issues present (or explicitly `None`).  
- Documentation & References included for material changes (issues/PRs/docs).  
- Language is concise, neutral, and free of marketing terms.  

---

<!-- TEMPLATE START (copy-paste and fill) -->
## [X.Y.Z] - YYYY-MM-DD — Short title  
Release type: Major | Minor | Patch  
Scope: <optional component(s)>

## 🧭 Executive summary  
<2–4 sentences explaining purpose, user value, and notable risks>  

## 🌟 Highlights  
- <Top 3–7 bullets for quick review>  

## ⚠️ Breaking changes  
- <Impact and who is affected>  
- <Action required at high level>  

## ⏳ Deprecations  
- <What is deprecated, alternative, and planned removal version>  

## ❌ Removed  
- <What was removed and how to migrate>  

## 🛡 Security  
- <CVE-YYYY-XXXX or advisory link, severity, affected components>  

## ✅ Added  
- <New features/capabilities>  

## 🔄 Changed  
- <Behavior/defaults/API/UX/performance changes>  

## 🔧 Fixed  
- <Notable bug fixes>  

## 🔗 Compatibility  
- <Supported platforms/versions/tooling; unchanged if none>  

## 📈 Upgrade guide  
1. <Step>  
2. <Step>  

## ↩️ Rollback  
1. <How to revert safely>  
2. <Validation after rollback>  

## ℹ️ Known issues  
- None  

## 📚 Documentation & References  
- <Links to updated docs, issues, PRs, roadmap>  
<!-- TEMPLATE END -->
