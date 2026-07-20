# railtail

**Lazy senior-dev mode for Rails monoliths.** railtail forces the simplest solution
that actually works: reach for an existing ViewComponent or service before custom
code, an installed dependency before a new one, one line before fifty — and cut the
code and tests that don't earn their place. It's a Rails-flavored fork of
[ponytail](https://github.com/DietrichGebert/ponytail).

railtail is **opt-in** — it does not auto-activate. Invoke `/railtail` to turn it on.

## Skills

| Command | What it does |
|---------|--------------|
| `/railtail [lite\|full\|ultra]` | Lazy mode itself — the ladder: YAGNI → existing component → existing service → installed dep → stdlib → one line → minimum. Default level: `full`. |
| `/railtail-review` | Over-engineering review of a diff — a ranked list of what to delete / reuse / shrink. |
| `/railtail-specs` | The test counterpart — which specs to cut (redundant, framework-trivial, tautological mocks, "it no longer does X"). |
| `/railtail-help` | Quick-reference card. |

## Install

```
/plugin marketplace add andeliuliu/railtail
/plugin install railtail@railtail
```

Or add to `settings.json`:

```json
"extraKnownMarketplaces": { "railtail": { "source": { "source": "github", "repo": "andeliuliu/railtail" } } },
"enabledPlugins": { "railtail@railtail": true }
```

## Usage

```
/railtail            # turn on lazy mode (full)
/railtail ultra      # YAGNI extremist: deletion before addition
/railtail-review     # review this branch's diff for over-engineering
/railtail-specs      # review this branch's specs for over-testing
stop railtail        # turn it off
```

## Notes

- **Tuned for Rails 8 monoliths** (ViewComponent, Hotwire, service/query objects,
  RSpec). The examples reference a real app's components/services as illustration —
  swap in your own; the ladder and rules are what matter.
- **Assumes a `develop` base branch and `bin/test`** for the review skills — adjust
  to your repo's conventions where noted in each skill.

## Credits

Forked from [ponytail](https://github.com/DietrichGebert/ponytail) by Dietrich
Gebert (MIT). railtail keeps ponytail's laziness logic and re-tailors it to a Rails
monolith. See [LICENSE](LICENSE).
