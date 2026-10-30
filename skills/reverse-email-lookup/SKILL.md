---
name: reverse-email-lookup
description: Find out who is behind an email address, for one address or a list - name, job title, current employer, profile URL and career history. b2bsearch--reverse-email-lookup returns the person (their other emails and phones with contacts on); an email-to-profile lookup returns just the profile URL and a short card, and a name beside a work address raises the match rate; b2bsearch/email-to-company (through call-actor) turns personal Gmail, Outlook or Yahoo addresses into the current employer and job title. Use for "who is this email", "reverse email lookup", "find the profile for this email", "who signed up with this address", "which companies do these Gmail sign-ups work at", "email to company", "qualify these inbound leads", "add title and company to this email list".
---

# Reverse email lookup

Three tools, one input field (`emails`, up to 1,000 per run each). Pick by what the user
wants back and by what they want to pay for.

| The user wants | Tool | Charged per 1,000 |
|---|---|---|
| the person: name, title, employer, profile URL, career | `b2bsearch--reverse-email-lookup` | $3.20 found; $8 with a live contact |
| only the profile URL and a short card | `b2bsearch--email-to-linkedin` | $3.20 per profile URL found |
| where someone works now, from a personal address | `b2bsearch/email-to-company` | $3.80 per current employer found |

`email-to-linkedin` and `email-to-company` clean `Name <address>`, `mailto:` links and
capitals, refuse shared mailboxes such as `info@` as free `role_address` rows, and return an
address that several profiles share as a free `ambiguous` row; nothing is guessed.

## The person: `b2bsearch--reverse-email-lookup`

Input: `emails`, `contacts`, `mustHave`, `compact`. Work and personal addresses both
resolve.

Output: `profileUrl`, `fullName`, `headline`, `jobTitle`, `companyName`, `location`,
`countryCode`, `experience`, `education`, `skills`. With `contacts: true` the row adds the
person's other emails and phones (`email`, `emailType`, `employerMatch`, `workEmails`,
`personalEmails`, `phone`, `phones`); the address you looked up is never sold back, and the
$8 price applies only when another working contact is on record. Statuses: `found`,
`not_found`, `invalid`, `missing_required`; only `found` is charged.

Use `compact: true` when the rows go into the conversation.

## Only the profile URL: `b2bsearch--email-to-linkedin`

Input: `emails`. Put the name beside a work address to find more: `Jane Doe
<jane@acme.com>` or `jane@acme.com, Jane Doe`. The address is tried first; only when a work
address is not on record is the person looked up by name at the address's company.

Output: `linkedinUrl`, `fullName`, `headline`, `jobTitle`, `company`, `companyLinkedinUrl`,
`location`, `country`, `matchedVia` (`as_given`, `gmail_normalized` or `name_and_domain`),
`matchedEmail`. Statuses: `found`, `not_found`, `ambiguous`, `invalid`, `duplicate`,
`role_address`, `profile_removed`, `error`.

Hit rate, from the Actor README (2026-09-30): about 31% of public developer addresses and
about 26% of corporate addresses resolved from the address alone; the same corporate
addresses with the name beside each one resolved at about 65%. The README notes that a name
match can still be a namesake at the same company (about 2 in 100 such rows), which is why
those rows say `matchedVia: name_and_domain`; tell the user which rows were matched by name.

## Employer from a personal address: `b2bsearch/email-to-company`

Through `call-actor`. Input: `emails`; personal mailboxes are the point, business addresses
work too.

Output: `company`, `jobTitle`, `positionStartDate`, `companyLinkedinUrl`,
`otherCurrentCompanies`, `seniority`, `fullName`, `linkedinUrl`, `location`, `country`,
`industry`, `emailType`. Here `emailType` describes the input address: `personal` for a
consumer mailbox, `business` for anything else. `business` does not mean the address is at
the current employer.

A row is charged only when it names a current employer. Free statuses: `not_found`,
`no_current_employer`, `ambiguous`, `profile_removed`, `invalid`, `role_address`, `error`.

Hit rate, from the Actor README (2026-09-30, addresses published by software developers):
about 35% of free-mailbox addresses resolved to a person, and about 86% of those had a
current employer, so roughly 3 in 10 personal addresses return a company. A position counts
as current while the profile shows no end date; check `positionStartDate` before relying on
it.

The row carries no company website, headcount or funding.

## Rules

- One run per list, not one run per address.
- Report which rows matched by name (`matchedVia: name_and_domain`) and which addresses
  were shared (`ambiguous`), so the user does not treat a namesake as confirmed.
- The person behind an address is third-party data. B2B use only: do not look up a private
  individual who is not acting for a company.
