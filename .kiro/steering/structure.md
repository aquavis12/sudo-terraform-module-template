---
inclusion: always
---

# Structure

```
main.tf                  # Primary resources; primary resource named "this"
variables.tf             # Module API: every variable typed + described
outputs.tf               # Every output described (IDs, ARNs, names)
versions.tf              # terraform >= 1.3.0, aws >= 6.0.0
examples/minimal/        # Smallest working usage (own versions.tf)
examples/complete/       # Every feature exercised (own versions.tf)
scripts/hooks/           # Git hook scripts wired via pre-commit
.claude/skills/          # Canonical agent skills (.agents/skills symlinks here)
```

Conventions:

- snake_case for resources, variables, outputs.
- Variables and outputs never live in main.tf; larger modules split into
  per-concern files (iam.tf, locals.tf, data.tf).
- Feature toggles via nullable variables and create_*/enable_* booleans, not
  forks.
- A `tags` map(string) variable is merged onto every taggable resource.
