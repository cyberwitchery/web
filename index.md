# cyberwitchery

cyberwitchery GmbH is a small software lab in erlangen, germany. we build hard software, and help teams build theirs: we build the tools for network automation and software assurance, and we put them to work with your team. the software comes first; consulting, integration, talks and workshops are built around it.

- book a call: https://cal.com/cyberwitchery
- contact: contact@cyberwitchery.com
- github: https://github.com/cyberwitchery

## products

- **alembic** (https://cyberwitchery.com/alembic.md) moves network data between the systems that hold it and the systems that need it, through a vendor-neutral data model you define. the core is open source; a commercial layer carries the same model into the fabric and operations.
- **divinate** (https://cyberwitchery.com/divinate.md) answers technical compliance questions about a repository with evidence anyone can check. it is in early development. the core is open source; commercial packs add maintained controls and framework mappings.

## how we work

- **the hard part is the edges.** most infrastructure already has its systems: an inventory, a config tool, monitoring, dns. what breaks is the connection between them, so that is where we work.
- **data model first.** you describe your data once, in a model you own, and an engine moves it between systems, instead of scripts written pair by pair.
- **nothing is written before you see the plan.** our tools read, then show what they would change. writing is a separate, explicit step.
- **the bug is in the program, and the fix belongs there too.** in security work we prefer small changes that can be verified in the code over controls wrapped around it.

## consulting

hire us to design, build, or review. we use our own tools where they fit and work with what you already run where they don't. tell us what you run and what you need, and we reply with a scope and an estimate.

- **network automation & integration**: data models, inventory migrations (for example nautobot to netbox), and integrations between inventory, fabric, and operational tooling such as config, monitoring, dns and lab systems. often built on alembic, never dependent on it.
- **security architecture & review**: architecture and threat-model reviews, application security strategy, secure coding and hardening, and an isms and secure development lifecycle your teams can work with.
- **technical due diligence**: independent assessments of software, architecture, engineering practices, and delivery risk for investors and acquirers.
- **systems & developer tools**: languages, compilers, developer tooling, and systems software: design, implementation, and review.

rates: consulting €1,400 per day, open-source work €1,200 per day, remote delivery 20% less. an initial alembic deployment usually lands between €10,000 and €20,000, including integration.

## talks & hands-on workshops

each topic runs as a talk, a half-day, or a hands-on full-day workshop, on site or remote. a workshop's core is a half day of about three hours, and extends to a full day.

networking:

- **data-model-first network automation**: why automation should start from a model of the world, not scripts.
- **from source of truth to fabric**: build a working pipeline from inventory to device configuration.
- **safe network config change**: diff and review config changes before they ship.

security:

- **practical threat modelling**: lightweight, repeatable threat modelling your team can run in sprint cadence.
- **isms & ssdlc you can work with**: turn "we should do security" into a minimal isms and an ssdlc that holds up in practice.
- **secure coding workshop**: review and write security-critical code with confidence. the bugs that matter, and the patterns that prevent them.

rates: talk of 45–60 minutes €1,900–2,400, talk of 75–90 minutes €2,400–2,800, workshop of 2–4 hours €2,500–3,000, workshop of 6–8 hours €4,200–4,800. remote delivery 20% less.

## open source

besides the alembic and divinate cores, we maintain:

- **netbox.rs, nautobot.rs, infrahub.rs**: rust clients for netbox, nautobot and infrahub, each with a cli.
- **infrahub-erd**: renders an infrahub schema as an entity-relationship diagram.
- **alembic-sot-plugins**: netbox and nautobot plugins that run alembic from inside the source of truth, with review and approval in the ui.
- **netform**: lossless parsing and structural diffing of network device configs.
- **contextual-encoder**: output encoding per context, for xss defense.
- **sbom-diff**: compares cyclonedx and spdx sboms, with licence gating in ci.
- **unsafe-budget**: limits unsafe code in rust and go projects.

all of them are at https://github.com/cyberwitchery.

## writing and research

- **the log** (https://cyberwitchery.com/log/): notes on what we build, how it works, and what we learn doing it.
- **papers** (https://cyberwitchery.com/papers.html): lab notes from cyberwitchery, short write-ups of problems we find interesting, published as a paper series. each note is a pdf.

## people

- **veit heller** works across programming languages, security, and network automation. he's built production systems, led engineering teams, and advised investors on technical due diligence. website: https://veitheller.de, blog: https://blog.veitheller.de
- **erik svedäng** is a programmer and designer whose work ranges from programming languages and developer tooling to games and experimental software. at cyberwitchery, he works on alembic. website: https://eriksvedang.com

## reporting a vulnerability

for a public repository, use github's private vulnerability reporting in that repository. for commercial products, email contact@cyberwitchery.com. we acknowledge reports within 5 business days and ask for 60 days before anything goes public. policy: https://github.com/cyberwitchery/.github/blob/main/SECURITY.md

## company

cyberwitchery GmbH, moltkestrasse 5, 91054 erlangen, germany. registered at amtsgericht fürth, HRB 23058. managing director: veit heller.
