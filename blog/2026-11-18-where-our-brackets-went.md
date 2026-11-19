---
title: "DITA-OT, Markdown, and a vector database: 3 lessons from preparing docs for AI"
authors: diana
tags: [ai-ready-content, rag, dita]
---

When another writer and I prepared SR Linux documentation for a CLI-based AI assistant for the simulator, the plan sounded simple: convert our DITA source to Markdown with DITA-OT, then load it into a vector database.

Then we tested the answers, and four problems showed up right away: missing brackets in code samples, cut-off sections, answers that weren't based on our docs, and features that sounded real but didn't exist.

{/* truncate */}

## What the converted Markdown really looked like

DITA-OT's Markdown was full of things that help a publishing tool and confuse an AI:

- Escaped characters, such as `\(`, `\)`, and `\_`
- Generated IDs, such as `{#section_y3p_lyw_cxb .section}`
- Deep heading levels that no longer matched the content

We never pinned down a single cause, so we fixed both the conversion cleanup and the chunking.

So "convert and load" turned into a checklist. After every conversion, we removed the escape characters and generated IDs, flattened the headings, separated concepts from examples, checked the example data, and only then loaded the content and tested it.

## Chunking by meaning, not by size

A token-size check flagged every section over 1,500 tokens. We didn't cut those sections at the limit. We split them along their own structure, kept the content as it was, and gave each new chunk a title that says what it answers.

BGP graceful restart, for example, became three chunks:

- BGP graceful restart
- Enabling graceful restart and BGP session re-establishment
- Using graceful restart with In-Service Software Upgrade (ISSU)

We also introduced every example with `The following example…`, so each one stayed attached to the concept it illustrates.

## The pages I almost cut

At first I thought the product overview topics were redundant, since the routing guide covered the same features in depth. I was wrong. The overview listed the supported RFCs and gave a quick summary of every BGP feature, which is exactly the kind of question people ask an assistant. We kept them.

## Even the example data matters

AI assistants repeat what they read, including example values. We changed AS numbers in examples to the ranges reserved for documentation in [RFC 5398](https://www.rfc-editor.org/rfc/rfc5398). Changing IP addresses took longer, because it needs analysis and help from subject matter experts.

## What I'd tell anyone preparing docs for AI

Making docs AI-ready isn't an export script. It's owning the structure before the content reaches the database, and testing where retrieval breaks before your users find out.

Are you chunking from flattened Markdown, or straight from the XML?