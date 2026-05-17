# Agent Skill Paper Update Notes

This repo tracks recent papers about agent skills. When updating `README.md`, use the workflow below to avoid the mistakes seen in the May 2026 refresh.

## Scope

- Focus on papers where "skill" means a reusable agent capability, such as skill libraries, skill banks, skill registries, skill retrieval, skill routing, skill evolution, skill synthesis, skill auditing, skill safety, or benchmark/evaluation of agent skills.
- Include domain papers only when the skill mechanism is explicit and central, not just a generic robot motor primitive or a human skill study.
- Treat general agent/tool/memory/workflow papers as candidates only if the abstract ties them to reusable skill artifacts or skill-facing interfaces.

## Search Keywords

Use both title and abstract matching. Strong keywords:

- `agent skill`, `agent skills`, `LLM agent skill`, `AI agent skill`
- `skill library`, `skill bank`, `skill registry`, `skill ecosystem`, `skill repository`
- `skill learning`, `skill evolution`, `self-evolving skill`, `skill synthesis`, `skill induction`, `skill distillation`
- `skill retrieval`, `skill routing`, `skill orchestration`, `skill graph`, `skill lifecycle`
- `skill safety`, `skill attack`, `malicious skill`, `skill auditing`, `skill verification`, `skill benchmark`
- `OpenClaw`, `ClawHub`, `SKILL.md`, `computer-use agent skill`, `GUI agent skill`

Weak keywords that need manual filtering:

- `agent`, `agentic`, `tool`, `workflow`, `memory`, `robot`, `action`, `computer use`
- `skill` when it means human productivity, model capability, generic RL motor skill, or clinical/coding ability without reusable agent artifacts.
- `Claw`/`OpenClaw` when the paper is only a generic agent benchmark or platform paper. Include it only if the abstract makes skills, skill trees, skill orchestration, or skill ecosystems central.

## arXiv Search Process

1. Search recent arXiv categories separately instead of one large all-category request:
   - `cs.AI`, `cs.CL`, `cs.LG`, `cs.MA`, `cs.IR`, `cs.SE`, `cs.CR`, `cs.HC`, `cs.RO`, `cs.CV`
2. Prefer `https://arxiv.org/list/<cat>/recent?skip=0&show=1000` when the arXiv API is rate-limited.
3. Save each category page to a temp file, then parse locally. This avoids timeouts and makes retries cheap.
4. Use the newest arXiv id already in `README.md` as the lower bound, then inspect all newer candidate titles and abstracts.
5. Do not trust title-only matching. Open the arXiv abstract page for every candidate before adding it.
6. If daily mirror pages return 404 or stale data, fall back to arXiv category recent pages directly.

## Code Link Verification

Always update the `Code` column from evidence, not guesswork.

Check in this order:

1. arXiv abstract page links: look for `Code`, `GitHub`, `Project page`, `Website`, Hugging Face, or anonymous review repositories.
2. arXiv HTML full text:
   - Try `https://arxiv.org/html/<id>` before downloading PDFs.
   - Search the HTML text for `github.com`, `Code:`, `project`, `repository`, `code is available`, and `open-source release`.
   - Treat HTML links as candidates only; inspect nearby text because references and arXiv page chrome also contain GitHub links.
3. PDF text:
   - Download the PDF to a temporary file first.
   - Run `pdftotext -nopgbrk`.
   - Search extracted text for `github.com`, `gitlab`, `huggingface.co`, `anonymous.4open.science`, `project page`, `code is available`, and `repository`.
4. Web/GitHub search:
   - Exact paper title + `github`
   - arXiv id + `github`
   - Method name + first author + `github`
   - If exact long-title search returns nothing, retry with the method name only, then verify manually against the paper title/authors.
5. Verify candidate repos:
   - Open the repo or project page.
   - Confirm the README, project page, or code references the same paper/method.
   - Check that the URL is public and not a 404. Prefer `curl -L -I <url>` or a normal GET if HEAD is unreliable.
   - For GitHub repos, `https://api.github.com/repos/<owner>/<repo>` is a quick public/404 check, and `.../readme` is useful when the repo description is empty.
   - A GitHub repo can be valid even when `/readme` returns 404. If arXiv explicitly names the repo as code/data and the repo itself is public, inspect the tree or accept the URL with that evidence.

Use `[Code](url)` only for verified public code or an official project repository. Use `—` when no public code is found, when a project page says "Coming Soon", or when the only evidence is an unrelated/stale repository.

## Bugs And Pitfalls

- `rg.exe` may fail with "Access is denied" on this Windows machine. Fall back to `Select-String`, `Get-ChildItem`, or Python parsing.
- Long `curl`/PDF download commands can survive an interrupted turn. After an interruption, check `Get-Process curl,pdftotext,python -ErrorAction SilentlyContinue` and stop stale download processes before continuing.
- PowerShell variables can retain stale values after a failed `Get-Content`. Initialize `$text = ''` before reading each extracted PDF text file, and skip URL extraction when the text file is missing.
- Partial PDF downloads may be larger than 10 KB but still invalid. Download to `*.tmp.pdf`, run `pdftotext`, and only move/accept the PDF if text extraction succeeds and produces non-trivial text.
- Set `PYTHONIOENCODING=utf-8` before Python scripts that print arXiv metadata; otherwise Windows GBK output can crash on author names or math symbols.
- Some arXiv pages include generic Hugging Face help links. Ignore `https://huggingface.co/huggingface` and `https://huggingface.co/docs/hub/spaces` unless the paper itself points to a specific dataset/model/space.
- arXiv HTML pages include unrelated GitHub links from references and the arXiv/LaTeXML footer, such as `github.com/arXiv/html_feedback`, `github.com/brucemiller/LaTeXML`, and cited baseline projects. Do not use those as the paper's code link.
- arXiv search can match a quoted query because the term appears only in references or background. For inclusion, require the paper's own title or abstract to make the skill mechanism central.
- A paper can say "open-source" or "open-source release" without exposing a public URL. Leave the `Code` column as `—` until the actual public repository is found and verified.
- A code URL listed in arXiv can still be private or 404. Verify it before adding it; if it 404s, leave `—` and do not keep the dead link.
- PDF text extraction can expose broken or line-wrapped URLs. Normalize trailing punctuation and manually verify the final URL.
- A repo name matching a method is not enough. Confirm it belongs to the authors or explicitly references the paper.

## README Update Rules

- Keep the existing table format:
  `| Title & Authors | Introduction | Paper | Code |`
- Put each paper under the closest existing theme:
  - Skill Learning / Self-Improvement
  - Skill-Oriented Reasoning / World Modeling
  - Skill Routing / Orchestration / Ecosystems
  - Skill Benchmarks / Evaluation
  - Security / Robustness
  - Survey / Taxonomy / Theory
  - Domain / Application Skill Systems
- Maintain newest-first ordering within each section when possible.
- Keep introductions short, factual, and publishable. Emphasize the skill mechanism and why it belongs in the list.
- Use `Paper` links to arXiv abstract pages, not PDFs.
- Use `—` for unknown or unavailable code.

## Validation Before Push

Run these checks before committing:

```powershell
git diff --check
git status -sb
```

Also run a small table validation script to check:

- every table row has four columns,
- every arXiv paper id is unique,
- new arXiv ids are present,
- the `Code` column is either `[Code](...)` or `—`.

Stage only intended files, then commit and push:

```powershell
git add README.md AGENTS.md
git commit -m "Update latest agent skill papers"
git push origin main
```
