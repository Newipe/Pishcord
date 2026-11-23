# Contributing to Pishcord

Thanks for helping improve Pishcord. This guide covers the issue tracker and the basics of contributing code. For build requirements and detailed commands, see [Building from Source](README.md#building-from-source).

## Issue tracker scope

The Pishcord issue tracker is for **Pishcord-specific work**:

- Bugs caused by Pishcord's own code, features, integrations, or packaging
- New features and improvements to Pishcord
- Documentation corrections that affect this repository

It is **not** a general support channel and is **not** the place to report:

- Problems that reproduce in upstream Vesktop and are unrelated to Pishcord changes
- Discord account, server, voice, or client problems that are not caused by Pishcord
- Third-party Vencord plugins, themes, or user scripts
- Plugin requests or questions about using Pishcord

Those belong to the project that owns them. If you are not sure whether Pishcord is responsible, explain why you think it is in the report; maintainers can redirect it if needed.

## Reporting a bug

Open a new issue and choose **Pishcord Bug Report**. Include:

- The affected Pishcord feature
- The Pishcord version and operating system
- How Pishcord was installed
- Clear steps to reproduce the problem
- Expected and actual behavior
- Relevant logs or screenshots, if available

Use the shortest reliable reproduction you can. One issue per distinct problem is easier to track than a combined report.

**Remove personal information before posting.** Do not publish authentication tokens, passwords, private profile URLs, proxy credentials, or other secrets. Review pasted logs and screenshots for account names, server addresses, and IP addresses as well.

## Requesting features and improvements

- Use **Pishcord Feature Request** for functionality Pishcord does not have yet.
- Use **Pishcord Feature Improvement** when an existing Pishcord feature should behave better.

Search existing issues first. Describe the problem or use case you want to solve, not only a preferred implementation, and keep the request within Pishcord's own scope. Requests for unrelated upstream client behavior or Vencord plugins will not be accepted here.

## Contributing code

1. Fork the repository and create a branch for your change.
2. Install dependencies with `pnpm install`.
3. Make a focused change and avoid unrelated refactors.
4. Run `pnpm test` for linting and type checks, and `pnpm build` to verify the application builds.
5. Open a pull request describing what changed and why.

For running the app locally, packaging targets, and platform requirements, follow the [building instructions](README.md#building-from-source). Do not include credentials, private profile URLs, or generated build output in a pull request.
