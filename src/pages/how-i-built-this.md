---
title: How I built this
description: How Diana John built this portfolio site as a docs-as-code pipeline, and the judgment calls along the way.
---

# How I built this

Most of my best documentation work lives inside a company, behind confidentiality agreements. This site is my answer to that problem. It shows both my writing and the docs-as-code pipeline behind it, built the same way I would build a documentation site at work: in Git, with automated checks on every change, and with nothing published until I decide it's ready.

The idea of showing the pipeline next to the content came from my mentor, Diana Payton, and her [showcase site](https://oddlittlebird.github.io/showcase-site/).

## What I built

- **Site:** Docusaurus, using the classic template in plain JavaScript, written in Markdown.
- **Source control:** a public GitHub repository. I commit and push with GitHub Desktop, and every change goes through a pull request.
- **Checks on every pull request:**
  - Vale for style, based on the Google developer documentation style guide.
  - My own Vale rule that flags telecom terms written the wrong way, such as 5G, 3GPP, and gNB.
  - codespell for spelling.
  - Lychee for broken links.
- **Protection:** a ruleset on the main branch blocks force pushes and deletion.
- **Publishing:** GitHub Pages. I publish by hand from the Actions tab, so merging a change never updates the live site by surprise.
- **Collaborator:** Claude Code, which explained each step, drafted configuration and text, and checked my work. I reviewed, tested, and committed every change myself.

I built the site twice. The first time, Claude Code did most of the setup. Then I set that version aside and rebuilt the site from an empty folder, one step at a time, so I understood every file.

## What I learned

The tools are easy to copy. The interesting part was the moments where something looked right and wasn't.

### A green check that checked nothing

My first pull request passed all three checks. Then I found out that my Vale configuration file was empty. Vale had no rules, so it had nothing to report. I filled in the configuration and broke a line on purpose to prove the check worked. My own telecom rule caught the mistake. A passing check tells you the tool found nothing. It doesn't tell you the tool looked.

### An AI answer that was confidently wrong

When my first blog post failed Vale, Claude Code told me that only errors fail the check and warnings don't. The next run failed with zero errors. The setting in my workflow fails on any finding, including warnings about first-person words, which every blog post uses. I now run Vale on blog posts with errors only and keep the stricter settings for the rest of the site.

### A build that passed with the wrong link

My Portfolio link opened the homepage. The build succeeded, because the link wasn't broken, only wrong. The page name matched the folder the whole site lives in, so the link pointed to the site's front door. I only caught it by clicking through the pages myself.

### A red check I merged anyway

I changed a blog post title and forgot that a colon in a title needs quotation marks. Vale flagged the file, and I merged the pull request anyway, because nothing stopped me. Next on my list is a rule that blocks merging until every check passes.

## What's next

- Block merging until all checks pass.
- Add an llms.txt file so AI tools can find my content.
- Generate reference documentation from an OpenAPI specification.
- Test the site against the Agent-Friendly Documentation Spec.

## Further reading

- [Troubleshooting guide](https://github.com/dianacjohn/portfolio/blob/main/TROUBLESHOOTING.md): every problem I hit while building this site, and how I fixed it.
- [My first blog post](https://dianacjohn.github.io/portfolio/blog/2026/11/18/where-our-brackets-went): what I learned preparing DITA content for an AI assistant.
