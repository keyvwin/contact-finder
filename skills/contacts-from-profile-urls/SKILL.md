---
name: contacts-from-profile-urls
description: Get the email address, the mobile number or the full career profile of people you have profile URLs for. The profile email finder returns the addresses on record (never pattern guesses), each labelled current employer, older company or personal; the profile phone lookup returns mobile numbers, mostly US; b2bsearch--profile-lookup returns the complete career record and, with contacts on, the emails and phones in the same row. Use for "find the email for these profile URLs", "find email from a profile URL", "get emails for this list of profiles", "phone number for this profile", "enrich these profile links", "full career history for these people", "who of these has a work email at their current company".
---

# Contacts from profile URLs

All three tools take `profileUrls`: full links, links without `https://`, mobile links and
bare handles (`jane-doe-example`). Spellings of the same profile are looked up once. Company
and school pages come back as free `invalid` rows.

## Email: `b2bsearch--linkedin-email-finder`

Input: `profileUrls` (up to 1,000 per run), `includePersonalEmails` (default `false`:
company addresses only; `true` adds gmail, outlook and ISP mailboxes).

Output per found row: `email`, `emailType`, `workEmails` (current-employer addresses
first), `emailCount`, `fullName`, `jobTitle`, `companyName`, `profileUrl`. `emailType` is:

- `work_current`: on the domain of the person's current employer;
- `work_other`: a company address that cannot be tied to the current employer, usually an
  earlier job; it may reach an inbox the person no longer reads;
- `personal`: only when `includePersonalEmails` is on.

Statuses: `found`, `no_email` (matched, nothing to sell; still names the person, and a
`_note` says when a personal mailbox is on record but was not asked for), `not_found`,
`profile_removed`, `invalid`, `error` (run the entry again). Only `found` is charged.

Price per 1,000 found: $8 for `work_current` or `personal`, $3 for `work_other`.

Hit rate, from the Actor README (30 September 2026, 200 people per country holding a
current B2B title):

| Country | Current employer's address | Only older company addresses | Personal mailbox (opt-in) |
|---|---|---|---|
| United States | 24% | 40% | 44% |
| France | 20% | 31% | 28% |
| India | 18% | 24% | 31% |
| United Kingdom | 16% | 38% | 23% |
| Germany | 16% | 36% | 20% |

The user's list will score differently; the README notes senior titles and larger
employers usually score higher. A `no_email` row can go on to the
`email-from-name-and-domain` skill with the name and employer it carries.

This tool does not check deliverability. Do not describe its addresses as verified.

## Phone: `b2bsearch--linkedin-to-phone`

Mobile numbers, mostly US. See the `phone-numbers` skill for input, hit rates and output.

## Full profile, or email and phone in one row: `b2bsearch--profile-lookup`

Input: `profileUrls` (up to 1,000 per run; `urls` and `publicIdentifiers` are read too),
`contacts`, `mustHave`, `compact`.

Without `contacts` the row is the career record: `fullName`, `headline`, `jobTitle`,
`companyName`, `location`, `countryCode`, `profileUrl`, `experience`, `education`,
`skills`, and more. With `contacts: true` the row adds `email`, `emailType` (`work` /
`personal`), `employerMatch` (the address is on the current employer's domain), `workEmails`,
`personalEmails`, `emailCount`, `phone`, `phones` (objects with `number`, `sharedBy`,
`companyLine`) and X, Facebook and GitHub links. `_tier` says which price the row was
charged at.

Price per 1,000 found: $3.20 for the profile; $8 with `contacts: true` when the person has a
contact that reaches them today (a personal mailbox, an address at the current employer, a
phone that is not a switchboard, or a social link). Everyone else ships at $3.20, old work
addresses included. The README measured the $8 tier applying to 53% of people in the US, 42%
in India, 40% in France, 33% in the UK and 32% in Germany (30 September 2026).

`mustHave: ["currentWorkEmail"]` or `["phone"]` delivers and charges only people who have
it. `compact: true` returns about 2 KB per person instead of 10+ KB at the same price; use it
whenever rows go into the conversation.

## Which one

| The user wants | Tool |
|---|---|
| an email per person, cheapest | `linkedin-email-finder` |
| only people with an address at the current employer | `profile-lookup` with `contacts: true`, `mustHave: ["currentWorkEmail"]` |
| a mobile number per person | `linkedin-to-phone` |
| email, phone and career together | `profile-lookup` with `contacts: true` |
| career history, no contacts | `profile-lookup` |
| more than 1,000 URLs | the `bulk-enrichment` skill |
