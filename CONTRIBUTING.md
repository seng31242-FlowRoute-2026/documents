# Contribution Guidelines

## Branch Strategy

We follow the branch strategy defined in the SENG 31242 guidelines.

### Main Branch

- `main`
  - Protected branch
  - Only accepts Pull Requests
  - Requires at least 1 approval

### Working Branches

Examples:

- draft/srs-chapter1
- draft/use-cases
- fix/issue-12
- feat/activity-diagrams

---

## Commit Message Convention
```
Format:

<type>(<scope>): <summary>

Examples:

docs(srs): add functional requirements section

feat(diagram): create activity diagram for upload flow

fix(usecase): correct alternative flow for UC-03
```
---

## Allowed Commit Types

- docs
- feat
- fix
- refactor
- chore
- style

---

## Pull Request Rules

Every PR must:

- Reference related issue
- Be reviewed by at least 1 member
- Follow formatting standards
- Contain meaningful description

---

## Diagram Rules

For every UML diagram:

- Commit source file (.drawio / .puml)
- Commit exported SVG or PNG

Committing only images is not allowed.

---

## Documentation Standards

- Use clear professional language
- Follow IEEE-style formatting
- Use consistent naming conventions
- Proof-read before PR submission