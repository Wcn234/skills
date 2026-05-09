# Rivta Blog Publisher

Codex skill for generating and QA'ing Rivta-style blog HTML.

This skill helps convert Markdown or HTML article drafts into Rivta blog HTML while preserving the source article content. It also adds Rivta-style links, image handling, FAQ schema when appropriate, and live-page QA checks for crawlability, canonical tags, noindex, sitemap presence, image paths, and FAQ schema consistency.

## What It Does

- Preserves the original article text, headings, tables, FAQ content, and source references.
- Applies the Rivta blog HTML framework and formatting conventions.
- Adds internal and external link classes according to Rivta rules.
- Uses provided images and keeps intentional source image layouts when present.
- Generates FAQPage JSON-LD only when matching FAQ content is visible.
- Produces title, meta description, category, introduction, and B2B SEO keywords.
- Checks published blog URLs for indexing and structured-data issues.

## Repository Structure

```text
rivta-blog-publisher/
  SKILL.md
  agents/
    openai.yaml
  references/
    rivta-blog-workflow.md
```

## Installation

Copy this folder into your Codex skills directory.

Windows:

```powershell
Copy-Item -Recurse -Force .\rivta-blog-publisher "$env:USERPROFILE\.codex\skills\rivta-blog-publisher"
```

macOS or Linux:

```bash
cp -R ./rivta-blog-publisher ~/.codex/skills/rivta-blog-publisher
```

Restart Codex after installing or updating the skill so it can reload the skill metadata.

## Usage

In Codex, ask for the skill by name:

```text
Use $rivta-blog-publisher to generate a Rivta-style blog HTML file from this article.
```

For live URL checks:

```text
Use $rivta-blog-publisher to check whether this published blog URL is crawlable and has correct FAQ schema: https://rivta-factory.com/blog/example.html
```

## Required Context

For best output, provide:

- The source article as Markdown, HTML, or a file path.
- Any production image URLs that should be used.
- The current Rivta `code-framework.html` file when available.
- The intended published URL when asking for live SEO or schema QA.

## Notes

This skill is brand-specific. It includes Rivta blog formatting, category, link, author-block, and SEO conventions. If you want to reuse it for another brand, update `SKILL.md`, `agents/openai.yaml`, and `references/rivta-blog-workflow.md`.

## License

MIT. See `LICENSE`.
