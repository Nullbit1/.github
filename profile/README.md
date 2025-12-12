# Nullbit1

Small security engineering and malware research group.
We build practical tools around hypervisors, OS internals, telemetry, malware labs and cloud posture instead of toy scripts.

Two people behind Nullbit1:

* @wythwool
* @froghtwaserror

We use a lot of languages when it makes sense for the problem. Favorites:

* Assembly
* C and C++
* C Sharp
* Go
* Java

Plus Python, Rust and whatever else is needed around them.

---

## What we actually do

Nullbit1 is focused on one thing: seeing what your systems are really doing and turning that into signals you can act on.

Rough breakdown:

* Runtime telemetry from hypervisors and OS internals
* A minimal XDR hub for home labs and small teams
* Local dynamic analysis for malware samples
* Cloud and IaC misconfiguration checks before things explode

Everything is small, readable and self contained. If you open the code, you should understand what it does in one sitting.

---

## Projects

### AegisHV

**Repository:** [`AegisHV`](https://github.com/Nullbit1/AegisHV)

Type 1 style EDR that lives on the KVM host and watches virtual machines by tailing `kvm_exit` tracepoints.

Core idea:

* Linux host with KVM and tracefs enabled
* No agents or kernel modules inside the guests
* The daemon tails `trace_pipe`, parses `kvm_exit` lines and classifies events

What it does in practice:

* Follows KVM exits from `tracefs` and parses them for x86 and arm layouts
* Decodes EPT or NPT violations into read, write and execute bits
* Builds a simple severity model around that:

  * write plus exec on the same page is critical
  * pure exec violations are high
  * write only is medium
  * read only is low
* Tracks recent writes per guest physical address and raises a W^X style alert if a page turns from write to exec in a short time window
* Pushes events to:

  * stderr in human readable form
  * optional JSONL file
  * Prometheus metrics on `/metrics`
  * `/healthz` endpoint for basic liveness checks
* Can replay old log files through the same parser and classifier
* Has a snapshot mode that prints a one shot JSON report about KVM and tracefs on the box

It is written in Rust, shipped as a single static binary and keeps the runtime footprint small.

Use it if you want hypervisor side visibility for VMs without putting anything inside the guests.

---

### SentraCore (nullbit xdr hub)

**Repository:** [`nullbit-xdr-hub`](https://github.com/Nullbit1/nullbit-xdr-hub)

SentraCore is a minimal XDR or poor man’s SIEM. It is built for a single organisation or lab and accepts security events over HTTP, stores them in PostgreSQL and raises incidents when several suspicious events line up on the same host.

What it gives you:

* HTTP ingestion endpoint for JSON events at `POST /api/v1/ingest/events`
* Normalised event model with:

  * source
  * host id
  * timestamp
  * kind
  * severity
  * tags
  * free form fields map
* Events and incidents stored in PostgreSQL with sane indexes
* YAML driven correlation rules with sliding windows over recent history
* Incident life cycle with `open`, `triaged` and `closed` states
* Simple RBAC with three roles: `admin`, `analyst`, `read_only`
* Login with JWT tokens for humans and tools
* Small JSON API that you can put a UI or dashboards on top of

It is intentionally not a full multi tenant SIEM and not a log shipper. It is a focused hub for homelabs and small teams that already have some sensors and want a single place where they meet and form incidents.

Typical sensors that fit this model:

* FIM agents
* PowerShell hunters
* kernel and eBPF loggers
* yara scanners
* hypervisor tools like AegisHV

---

### SentraLab

**Repository:** [`SentraLab`](https://github.com/Nullbit1/SentraLab)

SentraLab is a small dynamic analysis orchestrator for malware labs.

It does one job: take a sample from the host, boot a pre configured Windows VM snapshot, run the sample through a tiny agent, collect artefacts on the host and in the guest and produce a report per sample in JSON and HTML.

The flow:

* Keeps a list of VM profiles that describe how to reach each analysis VM
* Stores jobs in a local SQLite queue
* For each job SentraLab:

  * reverts a VM to a known libvirt snapshot
  * optionally starts `tcpdump` on a host interface
  * copies the sample into the guest over SSH
  * launches it via `sentralab-agent.exe` in the guest
  * waits for the agent to finish and pulls back JSONL logs
  * optionally dumps guest memory with `virsh dump`
  * builds JSON and HTML reports
  * marks the job as success or failure

Host side assumptions:

* Python 3
* Linux host
* QEMU or KVM with libvirt
* Windows guests reachable over SSH
* Optional tools for packet capture and memory dump on the host

On the guest side there is a single tiny Windows agent in C plus plus that runs the sample, logs behaviour to JSONL and exits.

SentraLab exposes:

* internal modules for config, database, VM control, artefacts and reporting
* a CLI entry point called `sentralab`
* a minimal FastAPI app for read only access to jobs and states

This is for people who prefer one box, a couple of templates and plain code over heavy frameworks.

---

### SentraCloudGuard

**Repository:** [`SentraCloudGuard`](https://github.com/Nullbit1/SentraCloudGuard)

SentraCloudGuard is a focused cloud posture scanner. It is built to catch dangerous misconfigurations early in both live AWS accounts and Terraform plans.

It currently knows how to:

* talk to AWS directly using the official Go SDK
* read Terraform JSON plans and run the same rules over what you are about to apply

Two modes:

* `live` reads config from your AWS account
* `plan` reads Terraform plan output

Every scan produces:

* one JSON report for automation
* one HTML report for humans that you can drop into a wiki or ticket or store in CI logs

Baseline checks for AWS right now:

* S3 buckets

  * publicly readable
  * publicly writable
  * world listable buckets inferred from ACLs like `AllUsers`, `AuthenticatedUsers`, `public-read`, `public-read-write` and similar
* Security groups

  * inbound rules from `0.0.0.0/0` that expose SSH on 22
  * inbound rules from `0.0.0.0/0` that expose RDP on 3389
* RDS

  * instances marked `publicly_accessible = true`
  * engine and version recorded in the report for easier reasoning

Internals:

* Go 1.21 plus
* simple YAML based rule engine over a normalised resource model
* reports as plain JSON and static HTML without JavaScript

This is meant to be small and direct. You should be able to read the code and know exactly which misconfigs it will flag.

---

## How pieces fit together

Think of Nullbit1 as a small stack.

* **Collection**

  * Hypervisor side events from AegisHV
  * Endpoint, kernel, eBPF and PowerShell telemetry from your own tools
  * Cloud posture from SentraCloudGuard
* **Aggregation and correlation**

  * SentraCore as the minimal XDR hub that accepts events over HTTP and raises incidents when patterns match
* **Deep dive**

  * SentraLab when you want to actually run a suspicious binary in a sandboxed Windows VM and see what it does

Nothing here tries to be a full product line. Each piece is small, composable and can be wired into whatever stack you already run.

---

## Tech stack and approach

Languages we use a lot:

* Assembly for low level pieces and glue around hardware
* C and C++ where control and predictability matter
* C Sharp for some future Windows heavy tooling
* Go for network services, CLIs and cloud facing tools
* Java where the ecosystem or integration requires it
* Python for orchestration and quick glue
* Rust where safety and performance together make sense

Platforms and APIs we care about:

* Linux, KVM, tracefs, libvirt
* Windows internals enough to write sane agents
* PostgreSQL and SQLite as the main storage backends
* AWS SDK and Terraform plans for cloud posture

Style:

* small binaries and focused services
* minimal dependencies
* explicit configuration
* clear data models
* no magic layers that hide what is actually going on

---

## Using the organisation

If you want:

* Hypervisor level detection for VMs
  → start with **AegisHV**

* A small XDR hub to glue your sensors together
  → look at **nullbit-xdr-hub** (SentraCore)

* A local dynamic analysis lab you can understand and tweak
  → try **SentraLab**

* Simple but effective checks for cloud misconfigurations and Terraform plans
  → run **SentraCloudGuard**

We keep everything open and readable. Fork it, wire it into your lab, rip out parts you like, or build your own tools on top.

---

## Contact and contributions

Right now the organisation is just two engineers shipping code in their free time. There is no support team behind this.

If you find a bug or want a feature:

* open an issue in the relevant repository
* if you send a pull request, keep it small and focused

We read everything even if we do not reply instantly.
