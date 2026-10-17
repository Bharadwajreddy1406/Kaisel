# Working on Kaisel

- Read `README.md`, `docs/contract.md`, `docs/decisions.md`, and
  `docs/tracking/README.md` before changes.
- Keep progress, current milestones, next steps, and learning notes in
  `docs/tracking/`, rather than `README.md` or this file.
- Keep changes small. Do not create placeholder packages, speculative interfaces,
  or directories for features that do not exist.
- Record important architectural alternatives and trade-offs. Ask the user to
  decide unless the session already authorizes the choice. Keep draft decisions
  visibly separate from accepted ones.
- Use one Go module and prefer the standard library.
  Add tests beside the behavior they verify; do not add dummy passing tests.
- Test messaging guarantees and failure cases. Formatting, vet, and the race
  detector supplement behavioral tests; they do not establish correctness alone.
- Update the contract and decisions when behavior changes. Do not imply that
  acceptance means persistence, delivery, or successful processing.
