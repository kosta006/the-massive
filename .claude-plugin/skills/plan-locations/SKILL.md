---
name: plan-locations
description: >
  This skill should be used when the user needs a list of search locations
  for a Massive business search, for example "search all of South East
  Queensland", "every capital city in Australia", "the suburbs around
  Parramatta", "the 50 biggest towns in Victoria", "plan my locations",
  or gives a region instead of specific places. It turns a region or loose
  list into up to 100 specific, verifiable locations and confirms the list
  with the user.
metadata:
  version: "0.1.0"
---

# Plan Search Locations

A Massive search accepts 1–100 locations in a single country. Each location must be specific enough to verify, e.g. "Southport, Queensland, Australia". Use this skill to turn whatever the user describes into a clean, confirmed list.

## 1. Pin down the brief

Establish, asking only for what is missing:
- **Country** (one per search).
- **Area**: a region, state, radius around a place, or a list of names.
- **Granularity**: major cities only, cities and large towns, or individual suburbs. If the area is large and the user hasn't said, recommend cities and large towns, since suburb-level coverage of a big region quickly exceeds 100.

## 2. Build the list

- Format every entry as `Place, State/Region, Country`, using full state or region names (e.g. "Newcastle, New South Wales, Australia"). Use the same pattern for other countries ("Austin, Texas, United States"; "Leeds, England, United Kingdom").
- Use only real places. When unsure a place exists or which region it belongs to, check with web search if available, or leave it out and say so. Never pad the list with guessed names.
- Disambiguate duplicates by region (there are several "Richmond"s). Remove exact duplicates.
- Keep all entries inside the chosen country.
- Cap at **100**. If the area needs more, say how many it would take and offer options: raise granularity (fewer, larger places), split into multiple searches, or prioritise by population. Remind the user that each search is paid separately and only one unpaid search is allowed per email at a time.

## 3. Confirm

Show the full numbered list with a count, e.g.:

```
42 locations in South East Queensland, Australia:
1. Brisbane, Queensland, Australia
2. Gold Coast, Queensland, Australia
...
```

Ask the user to add, remove or swap any entries. Repeat until they approve.

## 4. Hand off

Once approved, carry the list straight into the **find-leads** skill (it still needs the keyword and email, and its own final confirmation before anything is submitted). Do not call `create-search-request` from this skill.
