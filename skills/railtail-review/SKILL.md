---
name: railtail-review
description: >
  Code review focused exclusively on over-engineering in a Rails monolith.
  Scans this branch's diff vs its parent (or a file/staged changes you point at)
  and returns a ranked list of what to delete, simplify, or replace with an
  existing ViewComponent / service / installed dependency / stdlib. One line per
  finding: location, what to cut, what replaces it. Use when the user says
  "review for over-engineering", "audit this branch", "what can we/I delete",
  "find bloat", "is this over-engineered", "simplify review", or invokes
  /railtail-review. Complements correctness-focused review — this one only hunts
  complexity. Lists findings, applies nothing.
---

Review this branch's diff for unnecessary complexity, ranked biggest cut first.
One line per finding: location, what to cut, what replaces it. The diff's best
outcome is getting shorter.

## Scope

Default to this branch's diff vs its parent — not the whole repo, not just
uncommitted work. Resolve the base branch, never assume it:

`git symbolic-ref --short refs/remotes/origin/HEAD`  # → e.g. origin/main

If that fails (no remote HEAD set), take the first of `develop`, `main`,
`master` that `git rev-parse --verify` finds. Then:

`git diff <base>...HEAD`              # the branch's full diff
`git diff --name-only <base>...HEAD`  # just the files it touched

If the user names a base, use theirs. If the user points
at specific staged/working changes or a single file, review that instead. Read a
changed file in full when a finding needs context, but every finding must live
inside the reviewed diff — don't flag pre-existing code it didn't touch.

## Tags (mirror railtail's ladder, highest rung first)

- `delete:` dead code, unused flexibility, speculative feature. Replacement: nothing.
- `component:` raw ERB markup or new JS an existing ViewComponent / Stimulus controller already renders. Name it. (Rung 2)
- `reuse:` hand-rolled business/query/view logic an existing `app/services`, `app/queries`, `app/forms`, `app/presenters`, or `app/helpers` module already does. Name it. (Rung 3)
- `dep:` custom code doing what an installed dependency ships (Hotwire/Turbo, your CSS framework, an installed JS lib). Name the feature. (Rung 4)
- `stdlib:` hand-rolled thing Ruby stdlib / Rails / ActiveSupport ships. Name the method. (Rung 5)
- `yagni:` abstraction with one implementation, config nobody sets, layer with one caller, new route that could branch an existing RESTful action.
- `shrink:` same logic, fewer lines. Show the shorter form.

## Hunt

Gems/npm packages the stdlib or platform already ships; duplicated ERB blocks
that should be one ViewComponent; controller logic that belongs in
`app/queries`/`app/services`; services hand-rolling `def self.call` instead of
inheriting `ApplicationService`; new routes that could branch an existing
RESTful action by `type`/`use_case`; legacy-framework markup where the app's
current CSS framework exists; single-implementation interfaces, delegate-only
wrappers, dead flags and config, hand-rolled stdlib.

## Format

`L<line>: <tag> <what>. <replacement>.`, or `<file>:L<line>: ...` for
multi-file diffs. Rank findings biggest cut first.

## Examples

❌ "This formatting block might be more complex than necessary, have you
considered whether a shared component would be cleaner here?"

✅ `_row.html.erb:L18: component: raw <span> + number_to_currency. render MoneyComponent.new(amount:).`

✅ `reports_controller.rb:L40-72: reuse: page/sort logic inline in the action. ReportsQuery already resolves index state.`

✅ `foo_service.rb:L3: yagni: hand-rolled def self.call. Inherit ApplicationService, drop it.`

✅ `app.js:L4: dep: axios poll loop for a status refresh. Turbo Stream or @rails/request.js, 0 new deps.`

✅ `report.rb:L30-44: stdlib: manual loop builds a hash. index_by / each_with_object, 1 line.`

✅ `routes.rb:L88: yagni: new :archive route. Branch the existing update action by use_case.`

## Scoring

End with the only metric that matters: `net: -<N> lines, -<M> deps possible.`

If there is nothing to cut, say `Lean already. Ship.` and stop.

## Boundaries

Scope: over-engineering and complexity only. Correctness bugs, security holes,
and performance are explicitly out of scope. Route them to a normal review
pass, not this one. A single request/model spec run via the repo's own runner is
the railtail minimum, not bloat, never flag it for deletion. Lists findings,
applies nothing.
"stop railtail-review" or "normal mode": revert to verbose review style.
