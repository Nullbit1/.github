# Security Policy

Nullbit1 builds and hosts security tools. We take vulnerabilities seriously, especially anything that could:

- compromise users running our tools;
- be abused to hide malware instead of detecting it;
- leak sensitive data or telemetry.

## Supported versions

As a small org, we usually only support:

- the latest `main` branch;
- the most recent tagged release of each actively maintained project.

Older releases might receive fixes if the impact is high and the patch is trivial, but there are no guarantees.

Always check the individual repo README / releases page for more precise information.

## Reporting a vulnerability

Please use **private channels** instead of public issues when reporting security problems.

Preferred options:

1. Use GitHub's built-in "Report a vulnerability" / security advisory flow for the affected repository (if enabled).
2. If that is not available, open a very short issue titled `Security report – please contact me`, without details, and mention how we can reach you (email, Matrix, etc.). We will move the conversation to a private channel.

When you contact us, include:

- which repo and commit / release is affected;
- a clear description of the issue;
- minimal PoC or reproduction steps;
- what the impact is in realistic terms.

Do **not** include sensitive data, real secrets or live victim information.

## Disclosure process

Rough outline:

1. We confirm we received your report.
2. We reproduce and triage severity.
3. We work on a fix and coordinate a release.
4. We publish an advisory with acknowledgements (if you want to be named).

We aim to act quickly, but this is a spare-time org; response times can vary.

## Safe harbour

Security research done in good faith against Nullbit1 projects is welcome.

As long as you:

- do not exploit issues against people who did not consent;
- do not try to access infrastructure that is obviously not yours;
- respect rate limits and ToS of third-party services,

…the chances of us being unhappy with you are near zero. Talk to us first if you are unsure.
