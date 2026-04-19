# gamify-with-octalysis

An open, practitioner-friendly reference for the **Octalysis Gamification Framework** by [Yu-kai Chou](https://yukaichou.com). A single-page visual guide, a set of [Claude Skills](https://docs.claude.com) for applying the framework in real design work, and companion articles.

This project is an **independent educational summary**. It is not endorsed by or affiliated with Yu-kai Chou or the Octalysis Group. All framework concepts belong to Yu-kai. See [ATTRIBUTION.md](./ATTRIBUTION.md) for full credit and linking guidance.

> **Status:** The site, skills, and article drafts described below are being scaffolded. Remove this notice when all content is in place.

---

## What's inside

```
gamify-with-octalysis/
├── site/                         → the static GitHub Pages site
│   └── index.html                → single-file visual reference
├── skills/                       → Claude Skills for applying Octalysis
│   ├── octalysis-audit/          → score a product across all 8 drives
│   ├── gamification-design-review/ → review a feature before shipping
│   ├── user-journey-motivation-map/ → map drives to journey phases
│   └── ethical-gamification-check/ → flag Black Hat overuse & dark patterns
├── articles/                     → Substack drafts and companion essays
├── ATTRIBUTION.md                → credit, linking, licensing notes
├── CONTRIBUTING.md               → how to suggest examples, fixes, translations
├── LICENSE                       → MIT
└── README.md
```

## Live site

Once deployed to GitHub Pages, the site will live at:

```
https://LiliJulian.github.io/gamify-with-octalysis/
```

To preview locally, any static file server works:

```bash
cd site
python3 -m http.server 8080
# open http://localhost:8080
```

No build step, no dependencies — it's a single HTML file.

## The eight Core Drives

| # | Drive | Hat | Brain |
|---|-------|-----|-------|
| CD1 | 🌟 Epic Meaning & Calling | White | Right |
| CD2 | 🏆 Development & Accomplishment | White | Left |
| CD3 | 🎨 Empowerment of Creativity & Feedback | White | Right |
| CD4 | 💎 Ownership & Possession | White | Left |
| CD5 | 👥 Social Influence & Relatedness | White | Right |
| CD6 | ⏳ Scarcity & Impatience | Black | Left |
| CD7 | 🎲 Unpredictability & Curiosity | Black | Right |
| CD8 | ⚠️ Loss & Avoidance | Black | Left |

The visual reference in `site/index.html` goes deeper on each drive with real product examples, techniques, and designer tips.

## The Skills

Four Claude Skills that turn the framework into day-to-day design tools. Each one lives in `skills/<name>/SKILL.md` and can be dropped into any Claude setup that supports skills.

- **`octalysis-audit`** — score a product 0–10 across all 8 drives, draw the resulting octagon, flag Black Hat overuse and White Hat gaps.
- **`gamification-design-review`** — review a feature or flow through the motivation lens before it ships.
- **`user-journey-motivation-map`** — map which drives should dominate at each phase: Discovery, Onboarding, Habit, Mastery, Endgame.
- **`ethical-gamification-check`** — scan for dark patterns, manufactured scarcity, and drives that will feel coercive to users.

## Articles

Plain-language explainers meant for Substack or blog syndication:

- **What is the Octalysis Framework?** — an introduction for designers and PMs.
- **White Hat vs. Black Hat gamification** — when each is appropriate.
- **Auditing a product with Octalysis** — walked-through example.

Drafts live in `articles/`.

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md). Welcome contributions:

- New real-world product examples for a Core Drive
- Corrections or clearer wording
- Translations of the site or articles
- Additional skill recipes

## License

- **Code** (HTML/CSS/JS): [MIT License](./LICENSE)
- **Content** (skill markdown, article drafts, site copy): free to reuse with attribution — see [ATTRIBUTION.md](./ATTRIBUTION.md)

All Octalysis Framework concepts, language, and structure belong to Yu-kai Chou and are used here with attribution for educational purposes. If you are Yu-kai or a representative and would like anything changed, please open an issue.

## Credits

- **Framework**: [Yu-kai Chou](https://yukaichou.com), *Actionable Gamification* (2015)
- **Deeper learning**: [Octalysis Group](https://octalysisgroup.com) — certifications, consulting, workshops
- **This reference**: an open-source community summary
