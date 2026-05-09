# Codex Skills

A collection repository for reusable Codex skills.

This repository is intended to hold multiple skills over time. Each skill lives in its own folder under `skills/`, and `rivta-blog-publisher` is the first skill currently included.

## Skills

| Skill | Description |
| --- | --- |
| `rivta-blog-publisher` | Generate and QA Rivta-style blog HTML, including image handling, internal/external link classes, FAQ schema, and live SEO checks. |

## Repository Structure

```text
codex-skills/
  README.md
  LICENSE
  skills/
    README.md
    rivta-blog-publisher/
      SKILL.md
      agents/
      references/
```

## Install One Skill

Copy the skill folder into your local Codex skills directory.

Windows:

```powershell
Copy-Item -Recurse -Force .\skills\rivta-blog-publisher "$env:USERPROFILE\.codex\skills\rivta-blog-publisher"
```

macOS or Linux:

```bash
cp -R ./skills/rivta-blog-publisher ~/.codex/skills/rivta-blog-publisher
```

Restart Codex after installing or updating a skill so Codex can reload the metadata.

## Add Another Skill

Add each skill as its own folder under `skills/`:

```text
skills/
  your-skill-name/
    SKILL.md
    agents/
    references/
    scripts/
    assets/
```

Only `SKILL.md` is required. The other folders are optional and should be included only when the skill needs them.

After adding a skill, update the table in this README and `skills/README.md`.

## License

MIT. See `LICENSE`.
