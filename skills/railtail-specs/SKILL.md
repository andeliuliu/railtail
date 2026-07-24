---
name: railtail-specs
description: >
  The over-TESTING counterpart to railtail-review: review this branch's spec
  changes (or a spec file you point at) and list which specs to CUT — specs for
  deleted code, coverage already proven cheaper elsewhere, tests of Rails/gem
  defaults, tautological mock-asserts-mock, controller specs a request spec
  covers, and "it no longer does X" specs. One line per finding: location, why
  it earns nothing, what (if anything) replaces it. Use when the user says "do I
  need these specs", "cut unnecessary specs", "are these over-tested", "which
  specs can I delete", "railtail specs", or invokes /railtail-specs. Lists
  findings, applies nothing.
---

Review specs for over-testing. A spec earns its place ONLY if it would fail when
a real behavior breaks AND that failure isn't already caught elsewhere. If
deleting it loses no failure signal, cut it. The suite's best outcome: fewer,
sharper specs that still go red when the code is wrong.

## Scope

Default to this branch's spec changes vs its parent. Resolve the base branch,
never assume it:

`git symbolic-ref --short refs/remotes/origin/HEAD`  # → e.g. origin/main

If that fails (no remote HEAD set), take the first of `develop`, `main`,
`master` that `git rev-parse --verify` finds. Then:

`git diff <base>...HEAD -- spec/`                     # spec changes in this branch
`git diff --name-only <base>...HEAD | grep _spec.rb`  # just the spec files touched

If the user names a base, use theirs. If the user points at a
spec file (or the specs covering a file they just changed), review those instead.

## Tags (what to cut, and why it earns nothing)

- `delete:` spec for code that no longer exists, an `xit`/`skip`/pending that never runs, or an "it no longer does X" spec asserting *removed* behavior. Replacement: nothing.
- `dup:` the same behavior already proven by a cheaper or more-meaningful spec at another layer (a validation tested in the model AND a request AND a controller spec; a 403 covered by the policy spec and re-checked in a request spec). Keep one — name it — cut the rest.
- `framework:` tests Rails / ActiveRecord / a gem's own defaults (bare `validate_presence_of`, "the association exists", a default scope) with no custom logic. Cut — don't test the framework.
- `mock:` tautological — stubs a collaborator, then asserts it was called; it tests the double, not behavior. Rewrite to assert the real resulting record/state, or cut.
- `controller:` a controller spec for behavior a request spec already covers (repo prefers request specs). Fold into the request spec and delete the file.
- `shrink:` same coverage, cheaper: `create` where `build_stubbed`/`build` proves it (no DB needed), redundant `let`, or near-identical examples that add no distinct case. Merge/downgrade.

## Format

`<file>:L<line>: <tag> <what>. <keep instead / nothing>.` Rank biggest cut first.

## Examples

✅ `spec/models/post_spec.rb:L12: framework: it { should validate_presence_of(:title) } — bare Rails validation, no custom logic. Cut.`

✅ `spec/requests/posts_spec.rb:L40: dup: re-asserts the 403 the policy spec already covers. Keep post_policy_spec, cut here.`

✅ `spec/services/order_service_spec.rb:L22: mock: stubs PricingService.call then expects it received. Asserts the double — assert the persisted order instead, or cut.`

✅ `spec/models/post_spec.rb:L8: delete: "no longer sends the legacy email" — asserts removed behavior. Cut.`

✅ `spec/controllers/posts_controller_spec.rb:L1: controller: covered by spec/requests/posts_spec.rb; repo prefers request specs. Fold + delete the file.`

✅ `spec/queries/index_query_spec.rb:L30: shrink: create(:order) where build_stubbed suffices — no DB hit needed. Downgrade.`

## Scoring

End with `net: -<N> examples, -<M> spec files possible.`

Nothing to cut: `Coverage is earning its keep. Ship.`

## Boundaries

Cut over-testing ONLY. NEVER cut the coverage of real behavior, an edge case, a
money/auth path, or a regression tied to a ticket — and never delete the LAST
spec that would fail if the logic breaks (the railtail minimum is sacred, not
bloat). When unsure whether a spec catches a real failure, KEEP it and say so —
a wrongly-deleted spec is a silent regression waiting to happen. Run survivors
via your project's test runner. Lists findings, applies nothing.
"stop railtail-specs" or "normal mode" to revert.
