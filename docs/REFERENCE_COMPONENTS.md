# Reference component register

Reviewed: 2026-09-20. This is an intake register, not an installed dependency list.

| Source | Evidence and potential use | Adoption status |
| --- | --- | --- |
| [rampatra/wedding-website](https://github.com/rampatra/wedding-website) | README describes responsive wedding pages, Google Sheets RSVP and calendar support; GitHub identifies GPL-3.0 licensing | UX/reference only; no code or media copied |
| [GitHub wedding-platform topic](https://github.com/topics/wedding-platform) | Discovery starting point; a topic does not verify the stack, licence or quality of any repository | Candidate search only |
| EazyWed / parinay-wedding-app | Names supplied without exact repository identities | Unverified; not adopted |

## Component intake requirements

Record the exact repository, pinned revision, licence and notices, maintenance status, dependency risks, supported runtime, data access, integration contract and test evidence before adoption. Review media licences separately from code licences. Preserve required notices for any adopted component.

The upstream rampatra README advertises free hosting and supplies an example shared invite code. These are upstream demo characteristics, not WHYNOT production security or cost guarantees. WHYNOT requires scoped invitation/session controls.

## Additional candidates verified from upstream READMEs

| Repository | Documented capability | WHYNOT decision |
| --- | --- | --- |
| [fredbenenson/git-hitched](https://github.com/fredbenenson/git-hitched) | Rails/PostgreSQL, household and per-event RSVP, seating, hotel checkout/refunds and admin tools | Strong workflow reference. README says MIT but GitHub licence detection reports Other; inspect actual licence before copying. No runtime/security audit performed. |
| [MattiasHenders/wedding-template](https://github.com/MattiasHenders/wedding-template) | React, Airtable/Fillout RSVP, registry, maps and responsive layout | Visual reference. README claims MIT; GitHub metadata has no detected licence. Verify licence file before reuse. |

Git-hitched documents a shared site gate and email-based invite lookup; these are not proof of multi-tenant isolation. WHYNOT requires authenticated household access and scoped staff identities.

MattiasHenders documents NEXT_PUBLIC_AIRTABLE_TOKEN and NEXT_PUBLIC_PASSWORD configuration. Do not adopt that secret-handling pattern: server credentials and authentication secrets must remain server-side. Client-visible configuration is not a private guest-data boundary.

Topic pages classify projects; they do not certify a stack, security, payment eligibility or production completeness.

## Destination logistics candidates

README and metadata review, 2026-09-20; none installed or performance-tested.

| Repository | Verified upstream description | Adoption assessment |
| --- | --- | --- |
| [JacobStephens2/wedding-platform](https://github.com/JacobStephens2/wedding-platform) | PHP/MySQL; travel content covers parking, transport and hotel blocks; group RSVP and admin seating | Workflow reference. GitHub detects MIT. README does not prove live travel inventory or specialist logistics engines. |
| [Emanuele-Sgroi/My-Wedding-Invitation-Website](https://github.com/Emanuele-Sgroi/My-Wedding-Invitation-Website) | Next.js/Firebase; multilingual invitation, logistics content and family RSVP relationships | Reference only; custom licence requires review. Documented NEXT_PUBLIC_ADMIN_ACCESS_PASSWORD must not be reused as a security boundary. |
| [sotomaque/wedding-website](https://github.com/sotomaque/wedding-website) | Turborepo, Next.js, Prisma/Supabase, Clerk; per-wedding administration and trip planner | Closest documented stack candidate; no licence detected in repository metadata. Verify permission, code and tenant isolation before copying. README claims are not independently validated test results. |

None of these README reviews establishes a working real-time flight feed, high-load capacity, worldwide payment eligibility or safe concurrent inventory allocation. WHYNOT requirements are specified independently in [destination logistics](DESTINATION_LOGISTICS.md).
