---
name: email-from-name-and-domain
description: Find a work email address from a person's name and their company domain, and find a person's profile URL from a name and a domain. b2bsearch--work-email-finder returns ranked work email candidates with the evidence for each and an optional deliverability check of the top candidate; b2bsearch--name-to-profile resolves "Jane Smith, acme.com" to exactly one profile (namesakes are refused for free) and, with contacts on, returns the emails and phones on record. Use for "find Jane Smith's email at acme.com", "work email finder", "email from name and company", "what is the email of the head of sales at stripe.com, her name is X", "find the profile of John Doe at acme.com", "turn this list of names and companies into emails", "check the best email candidate".
---

# Email from a name and a company domain

Two different answers, and the user should know which one they get:

- **An address on record**: `b2bsearch--name-to-profile` with `contacts: true` finds the
  person and returns the addresses in their record. Nothing is generated.
- **Candidates**: `b2bsearch--work-email-finder` returns ranked work email candidates for
  the name at the domain, with evidence per candidate. A candidate is not an address on
  record; say "candidate" when reporting it.

When the user wants the most reliable address, try `name-to-profile` with `contacts: true`
and `mustHave: ["currentWorkEmail"]` first, then send the names it did not resolve to
`work-email-finder`. When the user just wants an address fast, `work-email-finder` alone is
one call.

## Work email candidates: `b2bsearch--work-email-finder`

Input, one of:

- `lookups`: `[{ "domain": "acme.com", "full_name": "Jane Smith" }]`, or `first_name` plus
  `last_name` instead of `full_name`;
- `csv`: pasted rows or a link to a publicly downloadable CSV;
- `csvFile`: an uploaded file, or a link to a file in the user's own Apify storage.

A CSV needs a domain column (`domain`, `company_domain` or `website`) and a name column
(`full_name`, or `first_name` + `last_name`). Up to 1,000 lookups per run.

`verifyTopCandidate: true` checks the best candidate against its mail domain and address
records and stamps the verdict on the row: `valid`, `invalid`, catch-all or `unknown`.

Output: `email` and `email_validity` (the best candidate), `candidates` (ranked, addresses
on the given domain first, role mailboxes such as `info@` left out; each with `validity` and
the receiving mail `gateway`), `candidate_count`, and with the check
`verification.status`, `verification.domain_catchall`, `verification.gateway`.

Reading it:

- Each candidate's `validity` is `valid` only when confirmed and `unknown` otherwise;
  `email_validity` is that flag for the best candidate.
- With `verifyTopCandidate`, `verification.status` carries the check's verdict.
- On a catch-all domain (`verification.domain_catchall: true`) the domain accepts any
  address, so `valid` does not prove the inbox exists. Say so.
- Call an address verified only when one of these fields says `valid`; otherwise it is an
  unverified candidate.

Price per 1,000: $8 per name that produces candidates; $6 more per top candidate checked
(charged only when the check answered). A name with nothing found is free; the same person
twice in one run is looked up and charged once.

## Profile from a name: `b2bsearch--name-to-profile`

Input: `names`, one person per line with the employer's domain: `Jane Smith, acme.com` or
`Jane Smith @ acme.com`. Each line can name its own company. `companyDomain` fills in lines
without a domain, for a list of people at one employer. Also `contacts`, `mustHave`,
`compact`.

A bare name resolves only when exactly one profile in the database carries it. The README
reports 9 of 9 well-known people resolved with the employer's domain and 1 of 10 as bare
names, so always pass the domain.

Output: `profileUrl`, `fullName`, `headline`, `jobTitle`, `companyName`, `location`,
`experience`, `education`; with `contacts: true` also `email`, `emailType` (`work` /
`personal`), `employerMatch`, `workEmails`, `personalEmails`, `phone`, `phones`. Statuses:
`found`, `ambiguous` (namesakes, free, with the holder count in `_nameMatchCount`),
`not_found`, `invalid`.

Price per 1,000 found: $3.20 for the profile; $8 with `contacts: true` when a contact that
reaches the person today is on record. The same name twice is charged once.

## Rules

- Never build an address yourself from a name and a domain. Use the tool, and label its
  output as a candidate.
- Tell the user which addresses are on record, which are candidates, and which were checked
  and with what verdict.
- For a phone number from a name and a domain, see the `phone-numbers` skill.
