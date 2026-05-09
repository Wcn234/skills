# Rivta Blog Workflow Reference

## Framework Conventions

Use the style learned from `code-framework.html`:

- `h1` centered, bold, large.
- `h2` and `h3` bold, simple, Helvetica/Arial.
- Body paragraphs use `p`, `font-size: 1.2em`, `line-height: 1.85`, `text-align: justify`.
- Section breaks use `<br /><hr />`.
- Tables use `<table class="vd-table">`.
- Internal links use `class="internal-link"` with green `#2A6B4C`.
- External links use `class="external-link"` with a distinct but restrained style, plus `target="_blank" rel="nofollow noopener"`.
- Images are centered with:
  `<div style="text-align: center; margin: 20px 0;"><img ... style="width: 100%; height: auto; display: inline-block;" /></div>`
- If the source HTML has explicit image layout, preserve that layout inside the Rivta framework instead of forcing full-width centered images. Keep source patterns such as side-by-side grids, icon-card groups, small certificate/logo tiles, contain-fit images, captions, and intentionally small image dimensions.
- Use the default centered full-width image wrapper only when the source HTML does not provide a specific image layout.
- Small image groups may use `.image-grid` and `.image-card` if needed, but keep styling consistent with the framework.
- Keep the existing Jolian author block unless the user gives a new author.

## Content Preservation

The user may provide Markdown or HTML. Preserve the visible article content:

- Do not rewrite the title or subheadings unless asked.
- Do not remove paragraphs, tables, FAQ, source lists, buyer notes, or CTA text.
- Do not add a conclusion if the source did not have one, unless requested.
- Adding links, classes, image wrappers, schema, and style is allowed.
- Moving content into the framework structure is allowed if the visible text stays intact.

## Link Rules

Internal Rivta links:

```html
<a href="https://rivta-factory.com/..." class="internal-link">Anchor text</a>
```

External reference links:

```html
<a href="https://external.example/..." class="external-link" target="_blank" rel="nofollow noopener">Anchor text</a>
```

Use semantic placement:

- Materials pages near material names.
- Product category pages near product type mentions.
- Certificates page near GRS, BSCI, OEKO-TEX, audit, or compliance mentions.
- Contact page in RFQ, quote, sourcing, or CTA sections.
- Avoid forcing too many links into one paragraph.

## FAQ Schema Rules

Good FAQ setup:

- Page visibly shows FAQ questions and answers.
- HTML output includes one `FAQPage` JSON-LD script when visible FAQ content exists.
- JSON-LD questions and answers match visible FAQ content.
- Add the FAQ script during initial HTML generation, not as a separate follow-up step.

Problem signs:

- `FAQPage` appears more than once in live source.
- Generic template FAQ questions are not visible in page body.
- FAQ topic does not match title, H1, or article topic.
- Question count in JSON-LD is higher than visible FAQ count.

## Local Validation Commands

Adapt paths as needed.

Check missing image names:

```powershell
$names = @('banner.png','image.jpg')
$content = Get-Content -LiteralPath '.\0507\article.html' -Raw
$names | ForEach-Object { [PSCustomObject]@{Image=$_; Present=($content -like "*$_*") } }
```

Check old asset paths and unclassified links:

```powershell
Select-String -LiteralPath '.\0507\article.html' -Pattern '\.\./assets'
Select-String -LiteralPath '.\0507\article.html' -Pattern '<a href="https://' |
  Where-Object { $_.Line -notmatch 'class="(internal-link|external-link)"' }
```

Check FAQ count:

```powershell
Select-String -LiteralPath '.\0507\article.html' -Pattern 'FAQPage'
```

## Live URL QA Commands

Use PowerShell `Invoke-WebRequest`. If network fails because of sandboxing, rerun with escalation.

Summary check:

```powershell
$ProgressPreference='SilentlyContinue'
$url='https://rivta-factory.com/blog/example.html'
$res=Invoke-WebRequest -Uri $url -UseBasicParsing -MaximumRedirection 5
$html=$res.Content
$bodyNoScripts=[regex]::Replace($html,'<script.*?</script>','','IgnoreCase,Singleline')
[PSCustomObject]@{
  StatusCode=$res.StatusCode
  FinalUrl=$res.BaseResponse.ResponseUri.AbsoluteUri
  Title=([regex]::Match($html,'<title[^>]*>(.*?)</title>','IgnoreCase,Singleline').Groups[1].Value.Trim())
  Noindex=($html -match 'noindex')
  Canonical=([regex]::Match($html,'<link[^>]+rel=["'']canonical["''][^>]*>','IgnoreCase').Value)
  LDJsonScripts=([regex]::Matches($html,'<script[^>]+type=["'']application/ld\+json["''][^>]*>','IgnoreCase').Count)
  FAQPageOccurrences=([regex]::Matches($html,'FAQPage','IgnoreCase').Count)
  QuestionTypeOccurrences=([regex]::Matches($html,'"@type"\s*:\s*"Question"','IgnoreCase').Count)
}
```

List FAQ script question names:

```powershell
$scripts=[regex]::Matches($html,'<script[^>]+type=["'']application/ld\+json["''][^>]*>(.*?)</script>','IgnoreCase,Singleline')
$i=0
foreach($m in $scripts){
  $i++
  $txt=$m.Groups[1].Value
  if($txt -match 'FAQPage'){
    "Script $i"
    [regex]::Matches($txt,'"name"\s*:\s*"([^"]+)"') | ForEach-Object { $_.Groups[1].Value }
  }
}
```

Check robots and sitemap:

```powershell
(Invoke-WebRequest -Uri 'https://rivta-factory.com/robots.txt' -UseBasicParsing).Content
$target='https://rivta-factory.com/blog/example.html'
$sitemaps=@(
  'https://rivta-factory.com/sitemap.xml',
  'https://rivta-factory.com/en-rivta-factory-com-sitemap.xml',
  'https://rivta-factory.com/rivta-factory-com-sitemap.xml'
)
foreach($s in $sitemaps){
  $c=(Invoke-WebRequest -Uri $s -UseBasicParsing).Content
  [PSCustomObject]@{Sitemap=$s; ContainsTarget=($c -like "*$target*")}
}
```

## Final Answer Pattern

For generated HTML:

- Link to the saved file.
- State what was preserved and what was added.
- Output `Title`.
- Output `Meta Description`.
- Output a short introduction for the article.
- Use existing source `<title>` and `<meta name="description">` when provided; generate them when missing.
- Output category.
- Output 6 comma-separated B2B SEO keywords.

For live QA:

- Lead with whether the URL is crawlable/indexable.
- List blocking issues first.
- If Google still shows old content, say it is likely cached and recommend Search Console URL Inspection -> Request Indexing.
