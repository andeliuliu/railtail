---
name: railtail-help
description: >
  Quick-reference card for all railtail modes, skills, and commands.
  One-shot display, not a persistent mode. Trigger: /railtail-help,
  "railtail help", "what railtail commands", "how do I use railtail".
---

# Railtail Help

Display this reference card when invoked. One-shot, do NOT change mode,
write flag files, or persist anything.

Railtail is a Rails-tailored fork of ponytail. It does NOT auto-activate on
session start — invoke `/railtail` to turn it on.

## Levels

| Level | Trigger | What change |
|-------|---------|-------------|
| **Lite** | `/railtail lite` | Build what's asked, name the lazier alternative in one line. |
| **Full** | `/railtail` | The ladder enforced: YAGNI → existing component → existing service → installed dep → stdlib → one line → minimum. Default once invoked. |
| **Ultra** | `/railtail ultra` | YAGNI extremist. Deletion before addition. Challenges requirements before building. |

Level sticks until changed or session end.

## Skills

| Skill | Trigger | What it does |
|-------|---------|--------------|
| **railtail** | `/railtail` | Lazy mode itself. Simplest solution that works, tuned to a Rails monolith's ladder. |
| **railtail-review** | `/railtail-review` | Over-engineering review of this branch's diff vs parent (or a file/staged changes you point at): ranked list of what to delete/reuse/shrink. `L42: component: raw markup. render CardComponent.` |
| **railtail-specs** | `/railtail-specs` | Over-testing review — which specs to cut: redundant, framework-trivial, tautological-mock, dead, or "it no longer does X". |
| **railtail-help** | `/railtail-help` | This card. |

## The ladder (full mode)

1. Does this need to exist at all? (YAGNI)
2. Existing ViewComponent / Stimulus controller in `app/`?
3. Existing service / query / form / presenter / helper?
4. Already-installed dependency (Hotwire, Bootstrap 5, …)?
5. Ruby stdlib / Rails / platform native?
6. One line?
7. Only then: the minimum code that works.

## Deactivate

Say "stop railtail" or "normal mode". Resume anytime with `/railtail`.

## More

Source & issues: https://github.com/andeliuliu/railtail
Upstream original (generic, non-Rails): https://github.com/DietrichGebert/ponytail
