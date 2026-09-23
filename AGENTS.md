# Agent Rules

- OpenSpec is the source of truth for feature development. Before substantial
  implementation, locate the corresponding active, reviewed OpenSpec change.
- Do not implement new capabilities directly from chat when no specification
  exists. Follow `NO SPEC -> NO IMPLEMENTATION`.
- Use only synthetic fixtures. Never add real corporate OpenAM configurations.
- Do not invent unknown OpenAM syntax, block structure, canonical paths, or
  override semantics.
- Prefer deterministic algorithms whenever the problem can be formalized.
- Use an LLM only through a specified interface for ambiguous matching; it is
  not a parser or primary diff engine.
- Never expose secrets in reports, tests, fixtures, or logs.
- After implementation, run the relevant automated tests.
