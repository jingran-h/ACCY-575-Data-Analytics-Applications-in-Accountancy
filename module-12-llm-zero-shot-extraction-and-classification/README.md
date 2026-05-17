# Module 12 — LLM Zero-Shot Extraction & Classification

> **Hands-on:** [walkthrough.md](walkthrough.md) — using a large pretrained LLM via API to label and extract from your textual data with no training data of your own.

## Motivation

For a lot of accounting text tasks — classify a risk factor, extract a forecast number, detect litigation language, summarize an audit committee disclosure — you used to need a labeled training set and a custom model. With current pretrained LLMs (Claude, GPT, Gemini) you don't: a well-written prompt with zero training examples gets within a few points of a fine-tuned model on most of these tasks, in the time it takes to make an API call.

The catch is that the quality of what you get is gated entirely by your prompt and your evaluation. The skills this module trains — write a clear extraction spec, force structured output, hand-label a small evaluation set, measure agreement — are the only things separating "the LLM said so" from a defensible analytical result.

## Goals

- Pick a labeling or extraction task on your MD&A text — classification, field extraction, or both.
- Write a prompt that produces structured JSON output the downstream code can consume directly.
- Hand-label a 50–100 item evaluation set before trusting the LLM at scale, then measure agreement.
- Recognize LLM-specific failure modes: hallucinated fields, refusals, format drift, prompt injection from the document.

## Going Deeper *(optional)*

*The lecture covers what you need. These are here if you want to dig further.*

- [Tool use and structured outputs](https://docs.claude.com/en/docs/build-with-claude/tool-use) — Anthropic. How to force schema-conformant responses from the API.
- [Structured outputs](https://platform.openai.com/docs/guides/structured-outputs) — OpenAI. Same concept, different vendor.

## Materials

*Slides, in-class exercises, and recordings posted here as the module approaches.*
