# CLAUDE.md

Project-scoped context for Claude Code (and the root `CLAUDE.md` symlink makes
this load in every session). The README is end-user documentation for module
consumers; this file is for an AI assistant working **on** the module codebase.

## What this repository is

A Terraform module for AWS, generated from
[sudo-terraform-module-template](https://github.com/sudo-terraform-aws-modules/sudo-terraform-module-template)
and maintained by SUDO Consultants. One module, one focused AWS capability.

## Git workflow (non-negotiable)

- Never commit to `main` or `master`. Create a feature branch for every task.
  Local guards: `no-commit-to-branch` (commit time) and the pre-push hook in
  `scripts/hooks/` (push time).
- Commit messages follow Conventional Commits. Allowed types: `feat`, `fix`,
  `docs`, `chore`, `refactor`, `test`, `ci`, `perf`, `build`,
  `revert`. The commit-msg hook rejects anything else.
- Do not amend or force-push shared branches; add new commits instead.
- Never edit files under `.terraform/` or commit `*.tfstate`.

## File layout

```
main.tf            # Primary module resources (primary resource is named "this")
variables.tf       # Module API - every variable typed + described
outputs.tf         # Every output described; expose IDs/ARNs/names of created resources
versions.tf        # terraform >= 1.3.0, aws provider >= 6.0.0
examples/minimal/  # Smallest working usage
examples/complete/ # Every feature exercised
```

Each example directory carries its own `versions.tf`. Larger modules may add
`locals.tf`, `data.tf`, or per-concern files (e.g. `iam.tf`); never fold
variables or outputs into `main.tf`.

## Module conventions

- `snake_case` for resources, variables, outputs (tflint
  `terraform_naming_convention` enforces this).
- Every variable has `type` and `description`; add `default` and `validation`
  where sensible. Every output has `description`. CI fails otherwise.
- Always expose a `tags` variable (`map(string)`, default `{}`) and merge it
  onto every taggable resource.
- Prefer feature flags (`create_*` / `enable_*` booleans, nullable variables)
  over forcing consumers to fork.
- Comments use `#`, never `//`. Explain *why*, and reference checkov IDs when
  suppressing or designing around a check.
- Hardened defaults: encryption at rest on, public access blocked, least
  privilege IAM. Checkov runs with `soft_fail: false` on the module root.

## How to validate (run before declaring work done)

```
make fmt        # terraform fmt -recursive
make lint       # tflint --config=.tflint.hcl
make checkov    # checkov security scan
make validate   # terraform init -backend=false && terraform validate
make docs       # regenerate README terraform-docs section (do not hand-edit it)
make pre-commit # all pre-commit hooks against all files
```

The README's Requirements/Providers/Inputs/Outputs tables between the
`BEGIN_TF_DOCS`/`END_TF_DOCS` markers are generated - change `variables.tf` /
`outputs.tf` and run `make docs` instead of editing them.

## Contributing checklist

1. Branch from `main`.
2. Make the change; update or add `examples/` when the module API changes.
3. `make fmt lint checkov` clean, `make docs` regenerated.
4. Conventional commit message.
5. Open a PR; CI runs pre-commit and checkov and must pass.

## Skills

Project-specific skill overrides live in `.claude/skills/` (canonical
location). `.agents/skills` is a symlink to the same directory so other agent
runtimes resolve identical content - maintain skills in `.claude/skills/` only.
