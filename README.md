# Contact Finder for Cursor, Claude Code and Grok Bot

<img src="assets/logo.png" width="64" height="64" alt="Contact Finder logo">

Contact Finder is an email finder, phone number finder and mobile number lookup for the
people you already have. Find email from a profile URL, run a reverse email lookup to see
who is behind an address and where they work, use the work email finder with a name and a
company domain, or hand over a whole CSV for contact enrichment and bulk enrichment. Your
agent runs the b2bsearch Actors on Apify and pays per found row through your Apify
account; misses are free.

| You have | You get | Tool |
|---|---|---|
| profile URLs | the email addresses on record, labelled current employer, older company or personal | `b2bsearch--linkedin-email-finder` |
| profile URLs, emails or `Jane Smith, acme.com` lines | mobile numbers, mostly US | `b2bsearch--linkedin-to-phone` |
| profile URLs | the full career profile; emails and phones with `contacts: true` | `b2bsearch--profile-lookup` |
| email addresses | the person: name, job title, employer, profile URL, career | `b2bsearch--reverse-email-lookup` |
| email addresses | the profile URL with name and current role | `b2bsearch--email-to-linkedin` |
| name + company domain | ranked work email candidates, optional check of the best one | `b2bsearch--work-email-finder` |
| name + company domain | the profile URL and profile | `b2bsearch--name-to-profile` |
| a CSV of up to 50,000 people | one enriched row per CSV row | `b2bsearch--bulk-people-enrichment` |
| personal email addresses | current employer and job title | `call-actor` → `b2bsearch/email-to-company` |
| email addresses | mobile number | `call-actor` → `b2bsearch/email-to-phone` |
| name + company domain | mobile number | `call-actor` → `b2bsearch/name-to-phone` |
| a GitHub, X or Facebook handle | the person; emails and phones with `contacts: true` | `call-actor` → `b2bsearch/social-handle-lookup` |
| X handles | email address | `call-actor` → `b2bsearch/twitter-to-email` |
| Facebook usernames | email address | `call-actor` → `b2bsearch/facebook-to-email` |
| a CSV that mixes people and bare company domains | people rows and company rows | `call-actor` → `b2bsearch/waterfall-lead-enrichment` |

Seven skills teach the agent the method: route each ask to one tool, state the cost before
the run, label every email as work or personal, report what was not found and why. See
[skills/](skills/).

<!-- recipes:start -->
## Commands and ready-made tasks

Type `/` in Cursor or Claude Code (Claude Code shows them with the plugin prefix, such as
`/contact-finder:call-list`). Each command runs the free count or preview first, says the
most the run can cost, waits for your yes and then runs with a spend cap.

| Command | What it does |
|---|---|
| `/call-list` | Build a cold calling list with verified mobile numbers. |
| `/crm-bounce-check` | Find the CRM contacts whose email will bounce because they changed jobs. |
| `/qualify-signup` | Qualify inbound sign-ups by email, Gmail included. |

The `ready-made-tasks` skill holds 8 tested inputs, each a public Apify task page:

- [10. Profile URLs to mobile numbers](https://apify.com/b2bsearch/linkedin-to-phone/examples/linkedin-urls-to-mobile-numbers)
- [11. CRM contacts with an email and no number](https://apify.com/b2bsearch/linkedin-to-phone/examples/crm-emails-to-mobile-numbers)
- [12. Profile URLs to work emails](https://apify.com/b2bsearch/linkedin-email-finder/examples/linkedin-urls-to-work-emails)
- [13. Find the CRM contacts who will bounce](https://apify.com/b2bsearch/linkedin-email-finder/examples/crm-contacts-with-past-employer-emails)
- [14. Personal emails for recruiting](https://apify.com/b2bsearch/linkedin-email-finder/examples/personal-emails-for-recruiting)
- [28. Qualify inbound sign-ups by email](https://apify.com/b2bsearch/reverse-email-lookup/examples/qualify-inbound-signups-by-email)
- [29. Where do your customer contacts work now](https://apify.com/b2bsearch/profile-lookup/examples/where-do-my-contacts-work-now)
- [30. A CRM export with a bit of everything](https://apify.com/b2bsearch/waterfall-lead-enrichment/examples/crm-export-to-emails-and-phones)

Generated from [recipes/tasks.json](https://github.com/b2bsearch/b2bsearch-plugin/blob/main/recipes/tasks.json).
<!-- recipes:end -->

## Install

### Cursor

[![Add Contact Finder to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/install-mcp?name=contact-finder&config=eyJ1cmwiOiJodHRwczovL21jcC5hcGlmeS5jb20vP3Rvb2xzPWNhbGwtYWN0b3IsZmV0Y2gtYWN0b3ItZGV0YWlscyxiMmJzZWFyY2gvbGlua2VkaW4tZW1haWwtZmluZGVyLGIyYnNlYXJjaC9saW5rZWRpbi10by1waG9uZSxiMmJzZWFyY2gvcHJvZmlsZS1sb29rdXAsYjJic2VhcmNoL3JldmVyc2UtZW1haWwtbG9va3VwLGIyYnNlYXJjaC9lbWFpbC10by1saW5rZWRpbixiMmJzZWFyY2gvd29yay1lbWFpbC1maW5kZXIsYjJic2VhcmNoL25hbWUtdG8tcHJvZmlsZSxiMmJzZWFyY2gvYnVsay1wZW9wbGUtZW5yaWNobWVudCZjbGllbnQ9Y29udGFjdC1maW5kZXIifQ==)

Import this repository as a plugin (**Dashboard → Plugins & MCPs → Add Marketplace →
Import from Repo**), or copy the folder to `~/.cursor/plugins/local/contact-finder` and
reload the window. Connect the `contact-finder` MCP server when Cursor asks; the browser
opens the Apify sign-in.

### Claude Code

```bash
claude plugin marketplace add b2bsearch/contact-finder
claude plugin install contact-finder@contact-finder
```

Run `/mcp` to sign in to the `contact-finder` server.

### Grok Bot

Connect the official Apify plugin (**Settings → Plugins → Apify → Add**) and sign in with
Apify. Then ask for these Actors by id, for example "run `b2bsearch/reverse-email-lookup`
on these addresses". The Actor ids in the table above are what to ask for.

### Any other MCP client

The server is Apify's hosted MCP with the Contact Finder tools pre-selected:

```
https://mcp.apify.com/?tools=call-actor,fetch-actor-details,b2bsearch/linkedin-email-finder,b2bsearch/linkedin-to-phone,b2bsearch/profile-lookup,b2bsearch/reverse-email-lookup,b2bsearch/email-to-linkedin,b2bsearch/work-email-finder,b2bsearch/name-to-profile,b2bsearch/bulk-people-enrichment&client=contact-finder
```

Streamable HTTP, OAuth 2.1 with dynamic client registration; an `Authorization: Bearer
<apify token>` header works where OAuth is not available.

## Try it

- "Here are 40 profile URLs from our webinar list. Find an email for each and tell me which
  ones are at their current employer."
- "Who is behind the address on this inbound demo request, and where do they work now?"
- "These 300 sign-ups used Gmail and Outlook. Which companies do they work at, and what are
  their job titles?"
- "Find the work email of Jane Smith at acme.com and check the best candidate."
- "Get mobile numbers for these 25 US sales directors." (profile URLs attached)
- "Find the profile URLs for these 12 names; they all work at acme.com."
- "These 30 GitHub usernames contributed to our repo. Who are they, where do they work, and
  what are their work emails?"
- "Enrich this CSV of 8,000 rows: some have emails, some profile URLs, some only a name and
  a company website."

## Pricing and the Free plan

Every tool charges per found row; a miss, an invalid entry and a person without the
requested field are free rows that say why. Per 1,000 found rows, read from the Apify
Store on 2026-10-30 (the tool description's price applies if it differs):

| Tool | Charged when | Free plan | Business plan and up |
|---|---|---|---|
| `linkedin-email-finder` | an address at the current employer or a personal one / only an older company address | $8 / $3 | same |
| `linkedin-to-phone`, `email-to-phone`, `name-to-phone` | a mobile number is found | $12 | $9 |
| `profile-lookup`, `reverse-email-lookup`, `name-to-profile` | a person is found / with a live email or phone (`contacts: true`) | $3.20 / $8 | $2.56 / $6.40 |
| `email-to-linkedin` | a profile URL is found | $3.20 | $2.56 |
| `work-email-finder` | a name produces candidates / the top candidate is checked | $8 / +$6 | same |
| `email-to-company` | a current employer is found | $3.80 | same |
| `social-handle-lookup`, `bulk-people-enrichment` | a person is found / with a live email or phone | $3.20 / $8 | same |
| `twitter-to-email`, `facebook-to-email` | an email is found | $8 | same |
| `waterfall-lead-enrichment` | a person / with an email or phone / a company | $3.20 / $8 / $5.50 | $3.20 / $8 / $4.40 |

Each run also carries Apify's start fee of $0.0002. On the Free Apify plan each Actor gives
an account 500 rows a month (found rows and misses alike) and up to 100 rows per run; any
paid Apify plan lifts the limits.

## Data and rules

The answers come from a professional-profile database, not a live scrape; a person who
changed jobs without updating their profile still shows the old employer. The plugin is for B2B outreach, account
research and CRM hygiene, not consumer lists or private individuals. You are responsible
for a lawful basis and for the rules that apply to your outreach, including GDPR and
CAN-SPAM. Apify terms and privacy apply to the account you connect:
[General terms](https://docs.apify.com/legal/general-terms-and-conditions) ·
[Privacy policy](https://docs.apify.com/legal/privacy-policy).

## Disclosure

Maintained by the author of the b2bsearch Actors on Apify; no referral parameters.

## More b2bsearch plugins

Same data and the same Apify account, split by job:

- [Full ICP Search](https://github.com/b2bsearch/full-icp-search): size an ideal customer profile for free, then people, leads, decision makers, companies and job postings
- [B2B Leads Finder](https://github.com/b2bsearch/b2b-leads-finder): lead lists with emails and phones, saved-search import, weekly top-ups
- [Hiring Signals](https://github.com/b2bsearch/hiring-signals): who is hiring for a role, postings that mention a technology, decision makers at hiring companies
- [Account Signals](https://github.com/b2bsearch/account-signals): new hires and job changes at target accounts, champion tracking, lookalike companies

All of them install from one Claude Code marketplace: `claude plugin marketplace add b2bsearch/b2bsearch-plugin`.

## License

MIT, see [LICENSE](LICENSE). Issues and requests: the GitHub issue tracker of this
repository. Actors and pricing: [apify.com/b2bsearch](https://apify.com/b2bsearch).
