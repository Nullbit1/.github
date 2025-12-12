# Governance

Nullbit1 is a small, practical org. There is no heavy-weight bureaucracy here, but there is still some structure so that things do not turn into chaos.

## Roles

### Org owners

Org owners control:

- repository creation and archival;
- team and access management;
- high-level project direction.

Right now this is a very small group of people, effectively the original creator of the org and close collaborators.

### Maintainers

Each repository has one or more maintainers. They:

- review and merge pull requests;
- cut releases;
- triage issues and security reports.

Maintainers are listed in the repo README or CODEOWNERS where appropriate.

### Contributors

Everyone who opens issues, sends PRs, writes docs or otherwise helps.

There is no formal membership process: if you consistently contribute good work, you are a contributor.

## How decisions are made

- Day-to-day technical decisions are made by maintainers of each repo.
- Security-sensitive changes may require review from another maintainer or org owner.
- Org-wide changes (new policies, moving / renaming repos, changing default branches) are discussed in issues in the `.github` repo.

We strongly prefer written discussion in issues / PRs over private chats, so that decisions are easy to track later.

## Becoming a maintainer

There is no automatic path, but typical signs that someone is ready:

- they have sent multiple high-quality PRs;
- they know the project's scope and constraints;
- they care about maintenance, not only adding shiny features.

If that sounds like you, open an issue in the repo and propose becoming a maintainer. Do not take it personally if the answer is "not yet".

## Changes to this document

If you think this governance model should change or be clarified, open a PR against the `.github` repo and let's argue about it there.
