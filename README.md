# BETA Firm Analyst: an Evidence-First Research Agent for Kenyan Manufacturing

A research agent that identifies Kenya-based manufacturing firms aligned with the government's Bottom-Up Economic Transformation Agenda (BETA), verifies them against public sources, scores them against a published framework, and reports every claim with its source, date, and confidence level.

This project is built around one idea: **an AI research report is only as useful as its traceability.** The first version of this agent produced a polished, plausible-looking report with no citations. This repository documents how that report was tested against a stricter standard, and how the agent is being rebuilt so every figure can be checked.

> **Status: in progress.** The v1 report in this repo is an unverified baseline. It is not a verified finding about any firm. See [Current status](#current-status).

---

## Table of contents

- [Why this project](#why-this-project)
- [Objective and core purpose](#objective-and-core-purpose)
- [What the agent is designed to do](#what-the-agent-is-designed-to-do)
- [What the agent must not do](#what-the-agent-must-not-do)
- [Version history](#version-history)
- [Baseline findings (v1, unverified)](#baseline-findings-v1-unverified)
- [What the v1 baseline got wrong](#what-the-v1-baseline-got-wrong)
- [Architecture (planned)](#architecture-planned)
- [Evaluation plan](#evaluation-plan)
- [Current status](#current-status)
- [Roadmap](#roadmap)
- [Repository structure](#repository-structure)
- [Limitations](#limitations)
- [Responsible use](#responsible-use)
- [Author's notes on the process](#authors-notes-on-the-process)

---

## Why this project

Kenya's BETA prioritises value addition, jobs, and inclusion across seven priority value chains. Policymakers, development partners, and researchers who want to understand which firms fit that agenda face a slow manual task: finding candidate firms, confirming they exist and are registered, checking what they actually do, and judging how much evidence supports each claim.

An AI agent can speed up this work, but only if it is built to refuse unsupported claims. Most AI-generated research reports fail quietly: they sound authoritative, mix verified and invented facts, and give the reader no way to tell which is which. This project tries to make that failure visible and measurable.

## Objective and core purpose

From the v2 agent specification (`prompts/v2_firm_analyst_spec.md`):

**Core purpose.** To research, verify, and assess firms based in Kenya that operate in line with BETA, and to report how closely each firm aligns with BETA using public evidence. Where evidence exists, the agent also reports whether the firm shows signs of being willing or ready to internationalise. Internationalisation is an optional signal, not a requirement.

**Output.** A verified, evidence-backed shortlist and scorecard with 3–5 key insights, packaged as a leadership-ready brief, with confidence levels, limitations, and risks documented for every finding.

**Core capabilities:**

1. **Understand and interpret** a research objective, BETA focus area, firm criteria, and constraints.
2. **Discover and verify** a long-list of candidate firms against official or registry sources.
3. **Assess alignment** against a 7-criterion BETA framework, using evidence only.
4. **Assess internationalisation signals** separately from alignment.
5. **Synthesise and communicate** findings as insights, a scorecard, figures, and a brief.
6. **Evaluate and strengthen** by documenting confidence, limitations, and risks for each finding.

## What the agent is designed to do

- Screen Kenya-based firms across the BETA pillars and priority value chains.
- Confirm each firm's identity through a Tier 1 source (registry, regulator list, NSE listing, or official directory) before scoring it.
- Score each verified firm on seven criteria, 0 to 3 each, for a maximum of 21.
- Classify firms as Strong, Moderate, Weak, or Not Assessable, using rules that prevent sector membership alone from producing a high score.
- Assign each insight a confidence level, using caps that prevent self-reported claims from being presented as verified.
- Report internationalisation signals separately, and keep domestic-only firms in the shortlist.
- Show the screening funnel so readers can see how many firms were found, verified, excluded, and scored.

## What the agent must not do

These limits come from the specification and are central to the project:

- Give financial, investment, legal, tax, or regulatory advice, or any valuation or credit assessment.
- Recommend that anyone invest in, lend to, buy from, or partner with a specific firm. Shortlists are research outputs, not endorsements.
- Use non-public data such as private financials or internal firm documents.
- Present marketing claims or press releases as verified facts.
- Include a firm that cannot be confirmed as Kenya-based.
- Make negative claims about a firm without a credible named source.
- Offer political opinion on BETA. The agent assesses alignment with the stated framework, not the merit of the policy.
- Present forecasts or export potential as certainties.

## Version history

| Version | Input | Output | Known properties |
|---|---|---|---|
| **v1** | A single-paragraph prompt (`prompts/v1_prompt.md`) | A 6-page report with a 1–5 scale and a maximum score of 35 (`outputs/v1_report_unverified.md`) | No citations, no evidence IDs, no tiers, no funnel, no confidence rules. Several internal inconsistencies. |
| **v2** | A structured specification with rules for verification, scoring, evidence, and confidence (`prompts/v2_firm_analyst_spec.md`) | Planned: a Research Brief with an evidence log, scorecard, and validator | Under construction |

The v1 prompt named the seven criteria and the seven value chains, but gave no scale, no source standard, and no output rules. The model filled those gaps itself, and the result looked finished. That gap is the reason v2 exists.

## Baseline findings (v1, unverified)

The v1 report ranked ten firms across seven value chains. **These findings are a baseline for comparison. They are not verified, and none should be cited as fact.** The scores use v1's own 1–5 scale and cannot be compared with v2 scores.

| Rank | Firm | Value chain | v1 score (/35) | v1 tier |
|---|---|---|---|---|
| 1 | KTDA | Tea | 34 | Tier 1 |
| 2 | Bidco Africa | Edible oils | 33 | Tier 1 |
| 3 | Brookside Dairy | Dairy | 32 | Tier 1 |
| 4 | Williamson Tea | Tea | 29 | Tier 2 |
| 5 | New KCC | Dairy | 29 | Tier 2 |
| 6 | Githunguri Dairy | Dairy | 27 | Tier 2 |
| 7 | East African Sea Food | Blue economy | 27 | Tier 2 |
| 8 | Leather Industries of Kenya | Leather | 25 | Tier 3 |
| 9 | Capwell Industries | Rice | 24 | Tier 3 |
| 10 | Rivatex East Africa | Cotton/textiles | 23 | Tier 3 |

**What the baseline suggests, if it holds up under verification:**

- Tea and dairy firms in the baseline are larger and more established, with cooperative or smallholder sourcing models. Leather, rice, cotton, and blue economy firms score lower, which the report attributes to weaker upstream supply organisation and limited export reach.
- Cooperative and smallholder-linked models are the most visible routes to grassroots inclusion in the baseline.

**What the baseline does not show:** that any firm is well aligned with BETA, that any figure is accurate, or that any firm is a good partner. The baseline has no source for most of its figures, and the spec's visibility-bias warning applies: large, online firms are easier to find than informal MSMEs, so this list reflects who is visible, not who is most aligned.

## What the v1 baseline got wrong

These are the first test cases for v2. Each is a concrete failure, documented in the v1 report itself:

1. **Inconsistent counts.** Bidco Africa's export reach is given as "more than 15 African countries" in one section and "14" in another.
2. **Tier conflict.** East African Sea Food is labelled Tier 3 in the Executive Summary and Tier 2 in the ranked table.
3. **Out-of-scope firm.** Bata Kenya is described as NSE-listed and is discussed in the formality section, but it is not one of the ten assessed firms.
4. **No sources.** Figures such as 200,000 Brookside smallholder suppliers, 5,000+ Bidco employees, and KES 160–170 billion in tea export earnings carry no citation, date, or sample size. Under the v2 spec, none of these would be usable.
5. **Empty references section.** The report had a References heading with nothing under it.
6. **Unsourced scale.** The 1–5 scale and the maximum of 35 are not defined anywhere the reader can check.
7. **Chain list not verified.** The seven value chains come from the prompt. The v2 spec notes that the latest Budget Policy Statement names nine chains, and the list must be confirmed from that source.

## Architecture (planned)

The design principle: **the language model does research and extraction; deterministic code enforces the rules.** Classifications, confidence caps, funnel arithmetic, and the pre-send checklist should never depend on the model's judgment, because they must be reproducible and testable.

```
research brief
     │
     ▼
 intake ──► search (logged, rate-limited) ──► candidate long-list
                                                  │
                                                  ▼
                                   entity verification gate  ──► excluded (reason logged)
                                                  │
                                                  ▼
                              evidence records (fixed schema)
                                                  │
                                                  ▼
            scoring + classification + confidence caps  (code, unit-tested)
                                                  │
                                                  ▼
                       figures (built only from scored data)
                                                  │
                                                  ▼
          report renderer ──► validator (13-point checklist, code)
                                                  │
                                                  ▼
                              brief.md + evidence log + run log
```

Key components:

- **Evidence schema.** Every evidence item has an ID, label (internal or external), firm, figure, source, tier (1–4), date, geography, sample size, and method. Items missing a required field are rejected.
- **Verification gate.** A firm passes only if it has a Tier 1 Kenya-based confirmation, a consistent name, and evidence of activity within 24 months.
- **Confidence caps.** Rules such as "an insight resting mainly on self-reported sources is capped at Low" are implemented in code and covered by tests.
- **Validator.** Checks that every number in the report traces to an evidence ID, every figure plots numbers from the scorecard, and the funnel reconciles (long-list = verified + excluded).

## Evaluation plan

Evaluation is the core of the project. Planned measures:

| Measure | Definition | v1 baseline | v2 target |
|---|---|---|---|
| Traceable figures | Figures with source, tier, date, geography, and n | 0 of the figures in the report | Report which fraction, and explain every gap |
| Funnel reconciliation | Long-list = verified + excluded | Not shown | Passes on every run |
| Entity verification | Scored firms that passed the gate | Not shown | 100% of scored firms |
| Confidence cap compliance | Insights that respect Section 12.2 caps | Not applicable | 100% on test cases |
| Banned language | Output containing directive or endorsement phrases | Not measured | Zero hits |
| Internal consistency | Contradictions between sections | At least 3 found | Zero |

**Test set.** A small labelled set of firms with known answers, including: a verified firm, a firm that fails entity verification, a firm with only self-reported evidence, and a group small enough to trigger the sample-size cap. Each test case has an expected outcome that the code checks.

**Reproducibility.** Every run stores its search queries, retrieved sources with retrieval dates, and model version, so results can be rerun and compared.

## Current status

| Item | Status |
|---|---|
| v1 prompt and baseline report, preserved | Done |
| v2 specification, preserved | Done |
| README and documentation | This document |
| Scoring and classification code with tests | Not started |
| Confidence caps with tests | Not started |
| Entity verification gate | Not started |
| Baseline checker: count unsourced figures in v1 | Not started |
| Agent layer (model plus search) | Not started |
| Validator | Not started |
| v2 evaluation run | Not started |

No v2 report has been produced yet. Until one is, the baseline findings above are the only results, and they are unverified.

## Roadmap

1. **Baseline checker.** Write a script that scans the v1 report and counts figures, and flags which ones lack a source. This produces the first measured result.
2. **Rules as code.** Implement scoring, classification, evidence strength, and confidence caps from the spec, with a test for each rule.
3. **Evidence store and verification gate.** Store evidence as structured records and block unverified firms from scoring.
4. **Agent layer.** The model runs searches and fills evidence records in the fixed schema. Code rejects any output that doesn't match.
5. **Validator and report renderer.** Enforce the 13-point pre-send checklist and build figures from scored data only.
6. **Evaluation.** Run the test set, record results in `outputs/`, and compare them with the v1 baseline.
7. **Write-up.** A short case study on what changed between v1 and v2, what the evaluation showed, and what it did not.

## Repository structure

```
beta-firm-analyst/
├── README.md
├── prompts/
│   ├── v1_prompt.md                  # original prompt, verbatim
│   └── v2_firm_analyst_spec.md       # structured specification
├── outputs/
│   └── v1_report_unverified.md       # v1 output, converted to Markdown; unverified
├── docs/                             # methodology and design decisions (to come)
├── src/                              # planned: scoring, verification, validator
├── tests/                            # planned: one or more tests per rule
└── evals/                            # planned: labelled test cases
```

## Limitations

- **Visibility bias.** Large, formal, and online firms are easier to find than informal MSMEs and smallholder-linked enterprises, yet BETA targets the bottom of the economic pyramid. Any shortlist from this project reflects visibility as well as alignment.
- **Limited public data.** Employment, sourcing, and export figures for private firms are rarely published, and self-reported impact figures are often unaudited.
- **Name variation.** Firms appear under different names across registries, websites, and media.
- **Policy changes.** BETA priorities can be updated in later Budget Policy Statements. The chain list must be checked against the latest document.
- **Model limits.** Language models can produce confident but wrong text. The architecture reduces this risk through source requirements and validation, but cannot remove it. Every output needs human review.
- **Snapshot in time.** Web sources change. Every result is valid only as of its retrieval date.

## Responsible use

This project produces research support. It is not an endorsement of any firm, and it is not investment, legal, or policy advice. Firms may correct or update their information, and the agent is designed to say so when evidence is missing or disputed. Anyone using a shortlist should verify it directly with the firm and the relevant authority before acting.

## Author's notes on the process

This project started as a single prompt. The first output looked like a finished consulting report, which is what made it worth investigating: a convincing format can hide missing evidence. Rather than present that output as a result, the project keeps it as a baseline, documents its failures, and rebuilds the process so that each claim can be checked.

The main lesson so far: the quality of an AI research report depends less on how well it is written than on whether the reader can trace each fact back to a source. The rest of this project is an attempt to make that traceability enforceable.

---

*Sources for the framework and objectives: the v2 specification in `prompts/`. Baseline findings: `outputs/v1_report_unverified.md`, which has no verified sources and should be treated as a draft.*
