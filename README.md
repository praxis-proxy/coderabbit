# coderabbit

Praxis CodeRabbit Configuration

This repository contains the organization-wide [CodeRabbit
configuration](https://docs.coderabbit.ai/configuration/central-configuration)
for the `praxis-proxy` GitHub organization.

Rules and instructions defined here apply to every Praxis repository.
Repository-specific overrides can be added in each repo's own
`.coderabbit.yaml`.

## How it works

CodeRabbit reads [.coderabbit.yaml](.coderabbit.yaml) from the default
branch of this repository and applies it to any repository in the
organization that does not have its own `.coderabbit.yaml`.

If a repository needs repo-specific settings, it must set `inheritance:
true` in its `.coderabbit.yaml` to merge with the org-wide defaults —
otherwise the repo-level config fully replaces them.

```yaml
# praxis-proxy/<repo>/.coderabbit.yaml
inheritance: true

reviews:
  path_instructions:
    - path: "filters/src/**"
      instructions: |
        ...
```

Merge behavior: objects deep-merge, scalars are overridden by the
repository value, and arrays put repository items first and then append
the org-wide items that do not collide (deduplicated by `path`, `name`,
`label`, `id`, or `key`). A repository that defines `path_instructions`
for `**/*.rs` therefore replaces the org-wide Rust block rather than
adding to it.

The CodeRabbit GitHub App must be installed on this repository, or it
cannot read the configuration.

## What belongs here

Only guidance that holds for every Praxis repository:

- Review policy — `assertive` profile, no auto-approval or gating, and
  `finishing_touches` disabled so CodeRabbit never authors code.
- Tool de-duplication — Clippy, actionlint, shellcheck, and
  `github-checks` are disabled because repository CI already runs them,
  Clippy with `-D warnings`.
- Shared conventions — the global review instruction plus the `**/*.rs`,
  `**/Cargo.toml`, and `.github/workflows/**` blocks, drawn from the
  conventions documented in
  [praxis-proxy/conventions](https://github.com/praxis-proxy/conventions)
  and each repository's `CONTRIBUTING.md`, `AGENTS.md`, and `docs/`.
- Knowledge base — those same convention documents, treated as the
  source of truth for review guidance, plus links to
  [praxis-proxy/praxis](https://github.com/praxis-proxy/praxis) and
  [praxis-proxy/conventions](https://github.com/praxis-proxy/conventions)
  so reviews can resolve core-crate APIs that live outside the
  repository under review.

Anything tied to one repository's layout — crate paths, fixture trees,
generated files, provider-specific test requirements — belongs in that
repository's own `.coderabbit.yaml` with `inheritance: true`.

## Changing the configuration

CodeRabbit is advisory in every Praxis repository. It does not approve
or block a PR, and it is configured not to author code — the project
does not accept code from a bot or tool, and your `Signed-off-by`
asserts that you reviewed and understand every line you submit.

Findings still deserve a reply. Fix them or explain why they do not
apply, the same as any other review comment. A finding that contradicts
a documented convention is a configuration bug: open an issue here so
the review instructions can be corrected.

Changes take effect once merged to `main`, because CodeRabbit reads the
configuration from the default branch. Validate against the published
schema before opening a PR:

```sh
curl -sSL https://coderabbit.ai/integrations/schema.v2.json -o /tmp/schema.v2.json
check-jsonschema --schemafile /tmp/schema.v2.json .coderabbit.yaml
```

Note that the schema does not set `additionalProperties: false` under
`reviews`, so a misspelled key there passes validation silently. Check
new keys against the schema's `properties` tree by hand.
