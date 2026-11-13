# alembic

alembic translates infrastructure data between systems through a shared model. it moves network data from the systems that hold it to the systems that need it: you describe your network once, in a vendor-neutral data model you define, and alembic reads from your inventory, reshapes the data into that model, and writes it out, into another inventory or as the files and changes your fabric and operations tools run on.

alembic is written in rust. the core is open source under apache-2.0. the commercial layer, alembic-ops, carries the same model into the fabric and into operations. it is built and supported by cyberwitchery.

- source and documentation: https://github.com/cyberwitchery/alembic
- discuss a deployment: https://cal.com/cyberwitchery or contact@cyberwitchery.com

## the problem

a device is a record in netbox, a host in ansible, a target in prometheus, a name in dns. most network teams already run these systems. what is missing is the code between them, usually scripts written pair by pair and rewritten whenever one side changes. alembic owns that translation: you define one model of your infrastructure, and every system connects to it instead of to each other.

## what teams use it for

- **moving between inventory platforms**: migrate from nautobot to netbox or from netbox to infrahub, or consolidate several instances into one, without losing relationships along the way.
- **keeping systems in sync**: run inventories side by side and let them exchange data through one model instead of pairwise scripts.
- **driving fabric and ops from data**: generate the inputs for config, monitoring, dns and lab tooling from the inventory you have, instead of keeping them up to date by hand.

## the model

every object in the model has a stable id, a type, a human-readable key, and attributes. relationships point at ids, never at a backend's own record ids, so renaming a device is an update and not a delete followed by a create. alembic remembers, per backend, which record belongs to which object, in a state file or in postgresql.

getting data into the model is a map: a written-down transformation from a source system's records to your model's types, validated against those types. a migration from nautobot to netbox, where the two disagree on locations, sites and status, is one such map.

## plan, then apply

import, map and plan only read. alembic compares the model with each destination and produces a deterministic plan of creates, updates and deletes. apply is the only step that writes, and it writes in dependency order. alembic manages only the fields your model declares and leaves everything else in a backend alone. when a plan binds a declared object to a record that already exists, the plan says so. an apply that stops halfway resumes from its journal.

## a small example

an inventory declares objects in your model. this excerpt, from the example inventory in the repository, declares a site and a device in it; the device points at the site, role and device type by uid:

```yaml
objects:
  - uid: "a4d6a0c3-4e73-4a76-b216-4d38f8c55f3d"
    type: dcim.site
    key:
      slug: "fra1"
    attrs:
      name: "FRA1"
      slug: "fra1"
      status: "active"

  - uid: "7b8f7a92-8fd0-4667-9a4b-9f3b5c9a4b1a"
    type: dcim.device
    key:
      name: "leaf01"
    attrs:
      name: "leaf01"
      site: "a4d6a0c3-4e73-4a76-b216-4d38f8c55f3d"
      role: "b1c2d3e4-5f60-4a7b-8c9d-0e1f2a3b4c5d"
      device_type: "c2d3e4f5-6071-4b8c-9d0e-1f2a3b4c5d6e"
      status: "active"
```

validate it, plan against a backend, and apply the reviewed plan:

```bash
alembic validate -f examples/inventory.yaml

NETBOX_URL=https://netbox.example.com NETBOX_TOKEN=... \
  alembic plan --backend netbox -f examples/inventory.yaml -o plan.json

NETBOX_URL=https://netbox.example.com NETBOX_TOKEN=... \
  alembic apply --backend netbox -p plan.json
```

the same inventory plans against nautobot or infrahub by changing `--backend`.

## working with it from an agent

alembic ships an agent skill inside the binary. it states the operating contract the command line does not: what each command touches, the identity rules, which flags are assertions, and worked workflows. `alembic skill show alembic` prints it, and `alembic skill install alembic` writes it to `.agents/skills/alembic/SKILL.md`, stamped with the version that wrote it. source: https://github.com/cyberwitchery/alembic/blob/main/crates/alembic-cli/skills/alembic/SKILL.md

## the open core

the open core holds the model, the engine, and connectors for the main inventory systems: netbox, nautobot, infrahub, any rest api, and peeringdb (read only).

connectors for anything else are standalone programs that alembic runs and talks to in json over stdin and stdout. there are sdks for rust and python, and a conformance runner to test a connector against the protocol. we build connectors for you, or you build your own.

## alembic-ops, the commercial layer

alembic-ops takes the same model into the fabric and operations, so one source of truth feeds every tool that needs a view of the network. most connectors write the files a tool reads; some also change a running system directly. supported operations differ by connector.

- **config**: an ansible inventory, an arista avd project (inventory, group and host variables), a nornir inventory, an openssh client config.
- **validation**: batfish, through the avd project.
- **lab**: a containerlab topology, or a running containerlab or eve-ng lab built and updated from the model.
- **monitoring**: prometheus scrape targets and scrape config.
- **naming**: a dns zone file, or a live dns server updated through zone transfers and dynamic updates, signed with tsig.
- **diagrams**: graphviz dot and mermaid topology diagrams, regenerated on every run instead of drawn by hand.
- **routing registry**: ripe database objects, read, created, updated and deleted over its rest api.

one fabric in netbox becomes all of these files from a single model. we run that end to end in ci: https://cyberwitchery.com/log/one-model-every-tool.html

alembic-ops also includes alembic-controller, which posts each plan to slack with approve and deny buttons and applies only a plan someone approved.

## inside netbox and nautobot

open-source plugins run alembic from inside the source of truth (netbox 4.6 or later, nautobot 3.2 or later). a plan runs in the background, someone reviews it in the ui, someone other than the requester approves it, and apply checks that the plan is still current before writing exactly what was approved. source: https://github.com/cyberwitchery/alembic-sot-plugins

## when it fits

alembic fits when the same network data has to reach more than one system, when you move between inventories, or when you want every change to the network's data to go through a reviewable plan. it is probably the wrong tool for a one-off load into a single backend (a script or csv import is less machinery), or for ephemeral or event-driven infrastructure (alembic reconciles batches against a desired state; it is not a real-time sync daemon).

## how it compares

- **terraform with the netbox provider** covers the apply step for one backend, with hand-written resources. alembic builds the desired state from your source of truth and applies it to several backends.
- **nautobot ssot and netbox diode** cover ingestion into one system. alembic does the same load through a backend-neutral model, with an explicit plan, and can target several backends.
- **infrahub's own sync** keeps infrahub at the centre. alembic keeps the model outside any single backend, so infrahub is one destination among several.
- **ansible with the netbox collection** connects source data to a backend task by task. alembic does it with a typed model, validation, and a deterministic plan.
- **pynetbox and custom scripts** are the same glue, hand-rolled. alembic provides the transformation, validation, stable ids across renames, and reproducible plans.

## pricing

- **open source**: free. the model, the engine, and connectors for the main inventory systems, under apache-2.0.
- **connectors**: from €6,000 per year. fabric and operations connectors under an annual licence, priced by the connectors you need.
- **integration**: €1,400 per day, at consulting rates. most initial deployments land between €10,000 and €20,000, including integration.

## getting started

prebuilt static binaries for linux and macos are attached to every release at https://github.com/cyberwitchery/alembic/releases. from source: `cargo install alembic-cli`. the source, documentation and examples are public; install a release and point it at your lab.

to plan a deployment, tell us what you run. we confirm connector availability and scope the integration: https://cal.com/cyberwitchery or contact@cyberwitchery.com.
