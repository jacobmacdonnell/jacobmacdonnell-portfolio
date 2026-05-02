# Agent Guidelines

## Project Context

This is Jacob Macdonnell's cybersecurity portfolio, built with Jekyll and the `jekyll-theme-chirpy` theme. The portfolio is focused on Security Operations, alert triage, incident response, SOC lab work, security engineering, and applied offensive-security labs.

The site should remain professional enough for recruiters and hiring managers while feeling more distinctive than a default blog theme.

## Design Consistency

Before making visual changes, read `DESIGN.md`.

Follow the "SOC analyst workbench" direction:

- Dark graphite surfaces
- Cyan, green, and amber operational accents
- Case-file style cards
- Compact telemetry/tag chips
- Practical, evidence-oriented language

Avoid:

- Gimmicky hacker visuals
- Excessive neon
- Matrix/glitch/skull motifs
- Generic resume-template sections
- Decorative cards inside cards

## Content Rules

Posts use front matter with this order:

```yaml
---
layout: post
title: "Source: Topic Name"
date: YYYY-MM-DD
categories: [Functional Domain, Activity]
tags: [Source, Tool, Concept]
pin: true
---
```

`pin` is optional and should come after `tags` when present.

Categories should use the approved taxonomy from `README.md`. Tags should describe sources, tools, telemetry, platforms, and concrete concepts.

## File And URL Rules

Post filenames determine generated URLs through the `_config.yml` permalink setting. If a post filename changes after publication, add a redirect in `_redirects`.

Jekyll-generated post slugs should be treated as lowercase URLs. Redirects in `_redirects` should use lowercase source and destination paths unless a verified build proves otherwise.

Do not modify vendored files under `assets/lib`.

Prefer adding local overrides such as:

- `_layouts/*.html`
- `assets/css/*.css`
- `_includes/*.html`
- documentation files at the repo root

## Homepage Rules

The custom homepage is implemented in `_layouts/home.html` and `assets/css/portfolio.css`.

Featured cards are controlled by `pin: true` in post front matter. Do not hardcode featured post titles unless there is a clear reason to bypass post metadata.

Keep homepage copy:

- concise
- recruiter-readable
- evidence-oriented
- specific to SOC, IR, detection, lab, or security engineering work

Avoid adding sections that duplicate the About page. The homepage should preview the work; the About page should carry the fuller resume/profile detail.

## Verification

Useful checks:

```powershell
rg -n "^(layout|title|date|categories|tags|pin):" _posts
rg -n "Google-Cyber-|PicoCTF|THM|EventID|Pentesting|Home Lab|categories: \[(Blue Team|Red Team|CTF Competitions)|CTF Challenges" _posts _tabs _config.yml
git diff --check
```

If Ruby/Bundler is available, run:

```powershell
bundle exec jekyll build
```

If it is not available, state that clearly and run the static checks that are available.

For visual work, also check:

- no horizontal overflow at mobile widths
- card text wraps cleanly
- focus states are visible
- motion is disabled under `prefers-reduced-motion`
