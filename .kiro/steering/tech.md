---
inclusion: always
---

# Tech

- Terraform >= 1.3.0, hashicorp/aws provider >= 6.0.0.
- Quality gates (all CI-enforced, run locally with `make`):
  - terraform fmt (`make fmt`)
  - tflint with .tflint.hcl - typed/documented variables and outputs,
    naming convention, standard module structure (`make lint`)
  - checkov with checkov.yaml, soft_fail false on module root (`make checkov`)
  - terraform-docs injects README tables (`make docs`)
- Git discipline via pre-commit framework:
  - commit-time: no-commit-to-branch blocks commits on main/master
  - commit-msg: Conventional Commits (feat, fix, docs, chore, refactor, test,
    ci, perf, build, revert)
  - pre-push: scripts/hooks/pre-push-protect-branches.sh blocks pushes to
    main/master
- `make setup` installs the toolchain; `make pre-commit` runs the full suite.
- Security posture: encryption at rest by default, no public access defaults,
  least-privilege IAM; checkov suppressions require a documented skip-check
  in checkov.yaml.
