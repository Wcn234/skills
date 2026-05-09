---
name: rivta-blog-publisher
description: Generate Rivta-style blog HTML from provided Markdown or HTML content, preserving the user's original article text while applying the learned code-framework style, inserting provided image URLs and semantic internal/external links, outputting category and B2B SEO keywords, and checking a live Rivta blog URL for Google crawl, canonical, noindex, sitemap, image, and FAQ schema issues. Use when the user asks to write, rebuild, publish-check, or QA Rivta Factory blog code or asks to reuse the current Rivta blog workflow.
---

# Rivta Blog Publisher

## Core Rule

Preserve the user's article content. Do not rewrite, delete, add claims, or change meaning unless the user explicitly asks. Format, link, and structure the article around the provided content.

## Workflow

1. Read the reference framework first.
   - Default framework file: `code-framework.html` in the current workspace when present.
   - Learn the exact HTML style: `h1/h2/h3`, `p`, `br`, `hr`, `.vd-table`, `.internal-link`, author block, CTA, and bottom `<style>`.

2. Read the new article source.
   - Accept Markdown, HTML pasted in chat, or a file path.
   - If the source is a full HTML document, extract the article body and preserve visible content.
   - Keep original headings, paragraphs, tables, FAQ, and source references unless asked to change them.

3. Insert provided assets and links.
   - Use every user-provided image at least once when semantically possible.
   - Convert relative asset paths to the user-provided production paths.
   - If the source blog HTML already defines image layout styles or grouping, preserve that image layout when converting into the Rivta framework. Examples: icon groups, side-by-side image grids, small certification/logo tiles, contain-fit images, captions, and intentionally small image dimensions.
   - Only use the default learned framework image layout when the source has no specific image layout to preserve.
   - Add `class="internal-link"` to Rivta links.
   - Add `class="external-link" target="_blank" rel="nofollow noopener"` to non-Rivta external references.
   - Choose link placement by meaning, not by density.

4. Generate the final HTML file.
   - Save under the user's date folder when one exists, such as `0507/`.
   - Filename should be the article title, sanitized for Windows.
   - Use `apply_patch` for manual edits.
   - Keep the familiar Rivta author block from the framework unless the user gives a different author.

5. Include FAQ schema in the HTML output when appropriate.
   - If the article has visible FAQ content, output one matching `FAQPage` JSON-LD script together with the final HTML.
   - The schema questions and answers must match visible FAQ text.
   - The generated HTML file should contain the FAQ script before delivery; do not wait for a later user request to add it.

6. Validate locally.
   - Check that all provided image filenames appear.
   - Check that no old `../assets` paths remain.
   - Check that all `https://` anchors have either `internal-link` or `external-link`.
   - Check there is at most one `FAQPage` in the generated file.

7. Final response for generated blogs.
   - Provide the saved file path.
   - Output `Title`, `Meta Description`, and a short introduction.
   - If the source HTML already contains `<title>` and `<meta name="description">`, use those values unless the user asks for new SEO text.
   - If title/meta are missing, generate concise B2B SEO-friendly values from the article.
   - Provide one category from exactly this list:
     `Materials & Sustainability`, `Design & Product Insights`, `Business & Marketing`, `Rivta Insights & News`, `Sourcing & Manufacturing Guide`.
   - Provide 6 high-value B2B SEO keywords separated by English commas.
   - Mention any checks that could not be run.

## Live URL QA

When the user asks whether a published URL is fixed, crawlable, or safe for Google indexing, check the live page. Network checks require approval when sandboxed.

Check:
- HTTP status is `200`.
- final URL is the requested URL unless there is an intentional redirect.
- `<title>`, meta description, and first `h1` match the intended article.
- no `noindex`.
- canonical points to the current URL or the intended canonical.
- robots.txt does not block `/blog/`.
- sitemap contains the URL when possible.
- images resolve to production CDN or valid absolute/relative paths.
- `FAQPage` appears exactly once when FAQ schema is expected.
- FAQ schema questions are visible on the page body.

## Reference

Read `references/rivta-blog-workflow.md` when implementing a blog or debugging live structured data.
