---
name: bulk-enrichment
description: Enrich a CSV of people, up to 50,000 rows per run, by whatever key each row carries - email, profile URL, GitHub / X / Facebook handle, or full name with a company domain. b2bsearch--bulk-people-enrichment returns one enriched row per CSV row, with emails and phones when contacts is on; b2bsearch/waterfall-lead-enrichment (through call-actor) also takes rows that carry only a company website and returns the company record for them, with contacts on by default. Use for "enrich this CSV", "bulk enrichment", "add emails and phones to this spreadsheet", "contact enrichment for our CRM export", "I have a mix of emails, profile URLs and names", "enrich these contacts and accounts in one file", or any list over 1,000 entries.
---

# Bulk enrichment

Two tools read a CSV and answer every row by the strongest key it carries: email address,
then profile URL, then social handle (GitHub, X, Facebook), then full name with the company
domain. The output has one row per input row, tagged with its CSV record number in
`_input.row` (the header is record 1), so it joins back onto the user's file.

| The file | Tool |
|---|---|
| people only, any mix of keys | `b2bsearch--bulk-people-enrichment` |
| people, plus rows that carry only a company website | `b2bsearch/waterfall-lead-enrichment` |
| under 1,000 rows of one key type, one field wanted | the narrow tool for that key (see `contact-router`) |

Before sending a big file, check the Free plan limits in `contact-router`: they cap each run
far below 50,000 rows.

## Getting the file in

`csv` takes pasted rows with a header line, or a link to a CSV the run can download. Paste
small files; for anything beyond a few hundred rows ask the user for a link instead of
pushing the file through the conversation.

Recognised columns:

- `bulk-people-enrichment`: `email`, `profile_url`, `github`, `twitter`, `facebook`,
  `full_name` (or `first_name` + `last_name`), `domain`.
- `waterfall-lead-enrichment`: the same, plus `linkedin_url` for the profile URL and
  `website` or `company_website` for the domain. Other columns are ignored.

Rename the user's headers to these before the run if they differ. A bare name with no
domain resolves only when exactly one profile carries it; namesakes come back as free
`ambiguous` rows.

## `b2bsearch--bulk-people-enrichment`

Input: `csv`, `contacts` (default `false`), `mustHave`, `compact`.

Output: the profile (`fullName`, `jobTitle`, `companyName`, `profileUrl`, `location`,
career, education); with `contacts: true` also `email`, `emailType` (`work` / `personal`),
`employerMatch`, `workEmails`, `personalEmails`, `phone`, `phones`, and `_tier` for the price
charged. A row looked up by email never gets that same address sold back.

Free rows: `not_found`, `ambiguous`, `invalid`, `profile_removed`, `missing_required`; a
person the file lists twice is looked up once, and the repeat is a free row naming the paid
one.

Price per 1,000 resolved rows: $3.20; $8 with `contacts: true` when a contact that reaches
the person today is on record.

Long runs checkpoint and resume after a platform server move, a row is charged only after
it is in the dataset, and ten consecutive service errors stop the run early.

## `b2bsearch/waterfall-lead-enrichment`

Through `call-actor`. Input: `csv`, `contacts` (default `true` here), `mustHave` (does not
affect company rows), `compact`. A cell that is empty or unusable is skipped and the next
key is tried; a row with nothing usable is a free `invalid` row.

A person row carries the profile and, with contacts, the email and phone that reach the
person today. A company row (only a website) carries `domain`, `companyName`,
`linkedinUrl`, `employeeCount`, `employeeRange`, `industry`, `country`, `hqLocality`,
`founded`, `hasFunding`, `lastRoundType`, `lastRoundDate`.

Free rows: `not_found`, `ambiguous` (a shared name, or a website several companies claim),
`invalid`, `profile_removed`, `duplicate`, `missing_required`.

Price per 1,000: $3.20 per person; $8 per person with an email or phone that reaches them
today (a person whose only addresses are at earlier employers is charged at $3.20); $5.50
per company ($4.40 on the Business plan and up).

## Running a big file

1. Count the rows and the key columns present. State the ceiling: person rows × $0.008
   with contacts (× $0.0032 without), plus company rows × $0.0055 on the waterfall.
2. Run the first 100 rows, read the found share by `_status`, and tell the user what the
   whole file is likely to cost. Misses are free, so the test costs only what it finds.
3. Run the rest. Through `call-actor`, set `callOptions.maxTotalChargeUsd` to the agreed
   ceiling.
4. Fetch rows in pages with `get-dataset-items` (`limit`, `offset`, `fields`), with
   `compact: true` on the run when rows go into the conversation.
5. Report counts by `_status`, emails labelled work or personal, and the dataset id so the
   user can export CSV or Excel from Apify Console.
