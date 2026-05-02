# Portfolio Design System

## Direction

This portfolio should feel like a polished security operations workbench: modern, credible, and slightly technical without becoming a novelty hacker theme. The visual language should support hiring/recruiting review first, then reward deeper technical readers with case-file details, tool tags, and investigation structure.

The intended impression is:

- Professional SOC analyst portfolio
- Evidence-driven case documentation
- Modern analyst console
- Practical lab and incident response capability

Avoid:

- Matrix rain, skulls, excessive neon, glitch effects, or gimmicky terminal walls
- Marketing-style hero sections that hide the work
- Generic resume templates with bland cards and no security-specific texture
- Overly dense dashboards that make the content hard to scan

## Visual Identity

Use a dark graphite foundation with restrained operational accents.

Core colors:

- Background: `#070b10`
- Surface: `#0d141c`
- Elevated surface: `#111b24`
- Border: `#243241`
- Primary text: `#eef5f8`
- Secondary text: `#9fb0bf`
- Muted text: `#6f8190`
- Signal cyan: `#48d5ff`
- Signal green: `#5df2a1`
- Alert amber: `#ffcf66`
- Risk red: `#ff6b6b`

Color usage:

- Cyan is for navigation, links, and active states.
- Green is for readiness, validated skills, and positive operational status.
- Amber is for featured items, pinned work, and investigation highlights.
- Red should be rare and reserved for risk/severity accents.

Do not let any single accent dominate the page. The page should read as dark graphite with signal accents, not a one-color theme.

## Typography

Use the existing theme fonts unless a future change introduces a full typography pass. Keep display sizes controlled and functional:

- Hero heading: direct and large, but not oversized.
- Section headings: compact, uppercase micro-label plus clear title.
- Cards: title first, short evidence summary second, metadata last.
- Body copy: plain, readable, and outcome-focused.

Avoid generic phrases such as "passionate learner" and "incredible journey." Prefer concrete statements about investigations, tools, evidence, and outcomes.

## Layout Principles

The homepage is the primary portfolio surface. It should act as a curated briefing, not a chronological blog dump.

Homepage order:

1. Hero / positioning
2. Operational stats
3. Featured case files
4. Focus areas
5. Tooling stack
6. Latest writeups

Cards should have:

- Maximum 8px border radius
- Subtle borders, not heavy shadows
- Clear metadata and category labels
- Compact tag chips
- No nested cards

Spacing should feel deliberate and scannable. Avoid oversized empty hero areas; visitors should see the next section on first load.

## Content Model

Use these portfolio primitives consistently:

- `Case file`: Investigation, playbook, reference, lab, or course writeup
- `Signal`: A key capability, artifact, or evidence type
- `Telemetry chip`: A tag/tool/technology label
- `Operational area`: Category pair such as `Security Operations / Alert Triage`

Post cards should emphasize:

- What kind of work it is
- What security domain it supports
- Which tools/telemetry were used
- Why it matters operationally

## Interaction

Interactions should be subtle:

- Hover lifts may move by 2-4px only.
- Borders can brighten on hover.
- Links can shift to cyan or green.
- Avoid loud animations, flicker, glitch, or continuous motion.
- Every clickable element needs a visible keyboard focus state.
- Respect reduced-motion preferences; motion should be enhancement, not a requirement.

## Accessibility

Maintain strong contrast on dark backgrounds. Do not rely on color alone for meaning. Keep chip text readable at small sizes, and ensure cards remain usable on mobile.

Responsive behavior:

- Desktop: multi-column dashboard layout
- Tablet: two-column where space permits
- Mobile: single-column, no horizontal overflow

Minimum checks:

- Cards remain readable at 390px wide.
- Long category/tag names wrap without pushing layout horizontally.
- Buttons have visible hover and keyboard focus states.
- The homepage does not require animation or color alone to communicate meaning.

## Homepage Maintenance

The homepage is implemented in `_layouts/home.html` and styled by `assets/css/portfolio.css`.

Current dynamic data:

- Featured case files come from posts with `pin: true`.
- Recent writeups come from the latest six posts.
- Case file counts are generated from `site.posts`.
- Incident response, alert triage, and SOC lab counts are generated from post categories.

Current curated/hardcoded content:

- Hero positioning statement
- Primary action labels
- Focus area descriptions
- Tooling stack chips

When editing curated content, keep language concrete and evidence-oriented. Prefer "Windows event analysis," "scope expansion," and "packet capture filtering" over vague claims like "cybersecurity passion" or "continuous learning."

## Implementation Notes

Current implementation keeps Jekyll/Chirpy as the base and overrides only the homepage plus scoped portfolio CSS. Future design work should preserve the post archive, categories, tags, and generated URLs unless redirects are added.

When adding or editing UI:

- Prefer semantic HTML.
- Keep CSS scoped under portfolio-specific class names.
- Do not modify vendored files in `assets/lib`.
- Use existing Font Awesome icons only when they clarify scanning.
- Keep visual changes compatible with generated Jekyll content.
- Keep generated post URL behavior in mind: `_config.yml` currently uses `/posts/:title/`.
- If changing a filename after publication, add a lowercase redirect in `_redirects`.
