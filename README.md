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
| `/railtail-pr-commenter [PR] [--top-k K]` | Discover once, verify candidates, then post up to K suggestions (default 10) in one PR comment. |
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
/railtail-pr-commenter               # verify this branch's PR suggestions; comment top 10
/railtail-pr-commenter 123 --top-k 5  # comment at most 5 suggestions on PR #123
/railtail-pr-commenter --dry-run     # preview the final comment without posting
/railtail-specs      # review this branch's specs for over-testing
stop railtail        # turn it off
```

While it's on, every response opens with `🚂 railtail · full` (or `lite`/`ultra`) —
no label means it's off.

`railtail-review` and `railtail-specs` use one discovery pass followed by targeted
verification that tries to disprove each proposed cut. They report only verified
suggestions, checking actual callers/replacements for code and preserved failure
signals for specs. They do not repeat full reviews until the list stabilizes.

`railtail-pr-commenter` (also referred to as `railtail-PR-commenter`) uses the same
workflow on a PR. It works down the ranked candidates until K suggestions are
verified or candidates run out, then posts one comment. K defaults to 10; rejected
candidates do not consume slots. It posts nothing when no suggestions survive.
GitHub access via a connector or authenticated `gh` is required; invoking the
commenter authorizes its final comment unless `--dry-run` is supplied.

## Notes

- **Tuned for Rails 8 monoliths** (ViewComponent, Hotwire, service/query objects,
  RSpec). The examples reference a real app's components/services as illustration —
  swap in your own; the ladder and rules are what matter.
- **No setup.** The review skills resolve your base branch from
  `origin/HEAD` (falling back to `develop`/`main`/`master`) and use whatever test
  runner your repo already has — nothing to hand-edit after installing.
- **Parallel when it earns its keep.** Tasks or reviews spanning 12 or more
  separable files may use up to three subagents. One coordinator keeps shared
  context, removes duplicate work/findings, and verifies the final result.

## Credits

Forked from [ponytail](https://github.com/DietrichGebert/ponytail) by Dietrich
Gebert (MIT). railtail keeps ponytail's laziness logic and re-tailors it to a Rails
monolith. See [LICENSE](LICENSE).
