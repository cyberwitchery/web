# divinate

divinate collects and preserves technical evidence for security review and compliance, with provenance and history. it answers technical compliance questions about a repository with evidence that anyone can check, and says plainly when the evidence cannot answer. it is built by cyberwitchery.

divinate is in early development. it is open source and runs today, but its sources, outputs and interfaces are still changing.

- source and documentation: https://github.com/cyberwitchery/divinate
- discuss an application: contact@cyberwitchery.com

## what it answers

- is every change to `main` reviewed before it is merged?
- is ci required before merge?
- which dependencies did this release add, and did they pass our policy?

questions like these come up in audits, security questionnaires, and certifications such as iso 27001 or soc 2. they usually get answered by clicking through settings pages, taking screenshots, and hoping nothing changed in between. divinate is for whoever has to answer them: an engineer, a security or compliance lead, a cto, or an auditor checking someone else's answer.

## five results, no scores

divinate reads the records that can answer a question from github, azure devops and your build tools, keeps them byte for byte, and answers with one of five results:

- **supported**: yes. the evidence establishes it, and the answer lists that evidence.
- **contradicted**: no. the answer names the counterexample, such as a direct push or a failed check.
- **insufficient evidence**: can't tell. nothing contradicts it, but something required is missing, and the answer says what.
- **stale**: the evidence was suitable but is too old to describe today.
- **not automatable**: the question needs human judgement, and divinate says so instead of guessing.

there are no scores, percentages or confidence levels. a result is a decision under explicit rules, with its reasons attached.

## finding nothing is not proof

to answer "did every change to `main` get reviewed?", divinate does not read the branch settings and infer the rest. it lists every change made to `main` during the period from the provider's own record, finds the pull request behind each one, and checks that someone other than the author approved it before it was merged. a direct push or an unapproved merge makes the answer contradicted, and names it.

divinate only answers "every" or "none" after it has shown that it saw the whole population. if the list of changes cannot be shown to be complete, because the provider keeps only a year of history, part of a response was unreadable, or the credential lacked permission, the answer is insufficient evidence and the gap is named.

configuration and history are separate questions. "review is configured" is answered from today's settings, "review happened" from the history of changes, and one never stands in for the other. a question like "were the reviews adequate?" is split into the parts records can settle and the part they cannot, and that part stays with a person.

## an answer, as you get it

for a repository with no review history collected yet, the exported answer to the review question reads:

```markdown
## every change integrated into main during 2026-09-12T01:17:42+02:00 .. 2026-10-31T13:36:32.601944665Z received at least one approving review before integration

result: **insufficient evidence**

- gap: no collection attempt matched this population and scope; uncovered intervals: 2026-09-12T01:17:42+02:00 to 2026-10-31T13:36:32.601944665Z
  - missing coverage: pull request reviews, repository mutations
- limitation: an approval recorded before integration does not establish that every provider-specific stale-review or vote-reset condition remained satisfied
```

the question, the result, what is missing, and what even a positive answer would not establish appear together. every answer carries an id that `divinate provenance` traces back to the exact retained responses it was derived from.

## working with it from an agent

divinate has an agent skill that lets an agent set up divinate, collect evidence, and answer compliance questions without overstating what the evidence shows. copy the `skills/divinate` directory from the repository into your agent's skills directory, for example `~/.claude/skills/divinate` for claude code. source: https://github.com/cyberwitchery/divinate/blob/main/skills/divinate/SKILL.md

## history

no collection is overwritten. each one is kept, so a review can compare the state of a repository over time and see what changed between one collection and the next. when evidence is missing or inaccessible, that is recorded too.

## anyone can check the answer

everything divinate used is kept in `.evidence/`, addressed by its sha-256 content hash. `divinate verify` checks the whole chain from raw responses to answers offline, without network access, credentials or the provider. `divinate provenance` shows exactly which records an answer rests on. hand the `.evidence/` directory to an auditor and they can check the answers themselves.

content addressing proves the records were not changed after they were captured. it cannot prove that the provider reported the truth.

## sources and outputs

current sources: github checks and branch protection, azure devops branch policies, and software bills of materials for releases. outputs: a dossier, a markdown summary of the current evidence, its coverage and its gaps, and an export of the answers for inclusion in a wider assessment.

## packs and the commercial layer

questions and evidence sources come from packs: external programs that speak a public, versioned protocol. the github and azure devops packs ship with divinate. the core owns acquisition, credentials, coverage and every write to the evidence store; a pack gets no credentials and no network access.

software change governance is our maintained commercial pack, built on the same public protocol. it defines stable controls for configured and observed review, direct integration that bypassed pull requests, and configured and observed build validation, on github and azure devops, with a mapping to iso 27001:2022 and a summary across many repositories. customers pay for maintained control definitions, mappings, compatibility and support. evidence, provenance and verification stay open source.

## scope

divinate runs locally against one repository at a time. it is not a scanner, a hosted service, a dashboard, a compliance catalog or a policy language, and it does not decide whether you are compliant. it answers the technical questions underneath that decision. the core is apache-2.0; external packs carry their own licences.

## getting started

divinate builds from source with rust 1.95 or newer. from the repository you need to answer for, `divinate init` writes a configuration, `divinate collect` gathers evidence and answers, and `divinate status` shows what is known and what is missing. the repository answers its own questions with divinate.

if you run security reviews or compliance and want to know whether divinate fits, we'd like to hear what you examine: contact@cyberwitchery.com.
