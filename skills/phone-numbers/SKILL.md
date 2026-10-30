---
name: phone-numbers
description: Find mobile phone numbers for people by profile URL, by email address, by name and company domain, or by GitHub / X / Facebook handle. One phone lookup takes profile URLs, emails and "Jane Smith, acme.com" lines in one list (up to 20,000 per run); b2bsearch/email-to-phone and b2bsearch/name-to-phone are the narrow routes through call-actor; b2bsearch/social-handle-lookup with contacts covers handles. Numbers are mostly US; the skill states the README hit rates and the cost before the run, and how namesakes are handled. Use for "phone number finder", "mobile number lookup", "find the cell phone for these profiles", "direct dials for this list", "phone number from an email", "find a phone number by name and company", "who on this list can I call".
---

# Phone numbers

The phone tools return mobile numbers that the Actor READMEs describe as verified when they
were collected: direct dials, never a company switchboard. The lookup is US-centric: 93% of
the numbers on record are US (+1) numbers. Tell the user before a run on a mostly non-US
list that few numbers will come back; the bill will be near zero, since misses are free.

## Routes

| The user has | Tool | Input | Per run |
|---|---|---|---|
| profile URLs (or a mixed list) | `b2bsearch--linkedin-to-phone` | `profileUrls`: profile URLs or handles, email addresses, or `Jane Smith, acme.com` lines, mixed freely | up to 20,000 |
| email addresses | `b2bsearch/email-to-phone` | `emails` | up to 20,000 |
| names with a company domain | `b2bsearch/name-to-phone` | `names` (`Jane Smith, acme.com` or `Jane Smith @ acme.com`), `companyDomain` for lines without one | up to 20,000 |
| GitHub, X or Facebook handles | `b2bsearch/social-handle-lookup` | `network` (`github`, `twitter` or `facebook`), `handles`, `contacts: true`, `mustHave: ["phone"]` | |

`linkedin-to-phone` alone covers a list that mixes all three key types; the narrow tools
are for a list of one kind. A name without a domain and a shared mailbox (`info@`) are
refused as free rows that say what to send instead.

## What comes back

The three phone tools return one row per entry: `primaryPhone` (E.164), `phones` (every
mobile number on record), `phoneCount`, `linkedinUrl`, `fullName`, `jobTitle`, `company`,
`location`, `country`. One found row is one charge however many numbers it carries.

Free statuses: `no_phone` (the person is known, no number on record), `not_found`,
`profile_removed`, `invalid`, `paused`, `error`; `email-to-phone` adds `ambiguous` (several
people share the address) and `role_address`.

`social-handle-lookup` returns the full profile with `phone` and `phones` (objects with
`number`, `sharedBy`, `companyLine`) plus the person's emails and social links.

## Hit rates, from the Actor READMEs

- **By profile URL** (829 live profiles, 2026-09-30): about 25% of US decision makers
  (founders and C-level), about 8% of other US profiles, 0–2% outside the US.
- **By email** (2026-09-30): about 31% of public developer addresses and about 25% of
  corporate engineer addresses resolved to a person; about 13–17% of the people resolved
  had a number.
- **By name and domain** (365 US decision makers, director and above at companies of 11 to
  5,000 people, 2026-10-01): 91% matched one person and 22% came back with a number, all US.

The user's list will do better or worse; quote these as the README's measurements, not as
a promise.

## Namesakes

On `name-to-phone`, and on `Jane Smith, acme.com` lines in `linkedin-to-phone`, two current
employees with the same name at one company return the first one who has a number, charged
like any found row, and the row says how many carry the name (`_matchCandidates` and
`_matchNote` on `name-to-phone`).
The README puts true namesakes at about 3% of names at US companies with 1,000+ employees.
Point these rows out and suggest checking `linkedinUrl` or the job title.

## Cost

Per 1,000 numbers found: $12 on the Free plan, $11 on Starter, $10 on Scale, $9 on Business
and up (read from the Apify Store on 2026-10-30; the tool description's price applies if it
differs). `social-handle-lookup` charges $8 per 1,000 people delivered with a live contact,
$3.20 per profile otherwise. State the ceiling (entries × $0.012) and the expected count
from the hit rates above before the run.

## Rules

- B2B use only.
- The README of `linkedin-to-phone` notes that in the US calls and texts fall under the
  TCPA and the Do Not Call rules: check numbers against the National Do Not Call Registry
  and get the consent the law requires before using an autodialer, a prerecorded voice or
  text messages. Pass this on when the user plans calls or texts.
