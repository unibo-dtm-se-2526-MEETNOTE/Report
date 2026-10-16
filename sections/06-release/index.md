---
title: Release
has_children: false
nav_order: 7
---

# Release

MeetNote produces one main artifact: a Python package named `meetnote`. The
package is published to PyPI so that it can be installed with `pip`.

The release process is automated using GitHub Actions and semantic-release.
When changes are merged into the main branch, the CI/CD workflow runs the
project checks and tests. If the checks pass, the deploy workflow uses
semantic-release to determine whether a new version should be created.

## Released artifacts

The project produces the following artifacts:

- A Python source distribution.
- A Python wheel package.
- A GitHub release containing the release notes and version tag.
- A package published to PyPI.

The latest releases produced by the project are:

| Version | Date | Main change |
|---|---|---|
| 1.0.0 | 2026-10-13 | Initial stable release, including the Kivy interface, transcription workflow, Gemini action-item extraction, Kanban board, and release automation |
| 1.1.0 | 2026-10-13 | Added meeting-summary generation using Gemini |
| 1.1.1 | 2026-10-13 | Fixed validation of the Gemini API key before extractor initialization |

The releases are available in the GitHub repository:

[https://github.com/unibo-dtm-se-2526-MEETNOTE/Artifact/releases](https://github.com/unibo-dtm-se-2526-MEETNOTE/Artifact/releases)

## Release process

The release process is automated through the `deploy.yml` GitHub Actions
workflow. This workflow can be called by the main CI/CD workflow or run
manually through `workflow_dispatch`.

The release workflow performs the following main steps:

1. Checks out the repository with its full Git history.
2. Fetches all existing Git tags.
3. Installs Python dependencies using Poetry.
4. Installs Node.js, which is required by semantic-release.
5. Runs semantic-release.
6. Creates a new Git tag and GitHub release when a new version is needed.
7. Publishes the Python package to PyPI.

Semantic-release analyzes the commit history and decides whether the version
should be updated. It uses the Conventional Commits messages written during
development to determine the type of release.

The workflow uses the following secrets:

- `GH_TOKEN`: used by semantic-release to create GitHub releases and tags.
- `PYPI_TOKEN`: used to authenticate and publish the package to PyPI.

These values are stored as GitHub Actions secrets and are not written directly
in the workflow file or source code.

The workflow is configured to run a dry-run release when it is not executed on
the `master` or `main` branch, or when it is processing the initial commit.
This prevents accidental publications during development.

## Choice of the license

The project is distributed under the Apache License 2.0.

This license was chosen because it is a permissive open-source license. It
allows other people to use, modify, and distribute the software while requiring
attribution and including the license text. It also provides an explicit patent
grant, which makes it suitable for an open-source software project.

The same license applies to the source code and to the released Python package.

## Choice of the versioning schema

MeetNote uses Semantic Versioning (SemVer). A version number has the following
format:

```text
MAJOR.MINOR.PATCH
```

For example, version `1.1.1` means:

- Major version: `1`
- Minor version: `1`
- Patch version: `1`

The version number is updated automatically by semantic-release according to
the commit messages:

- A commit starting with `fix:` increases the patch version.
- A commit starting with `feat:` increases the minor version.
- A commit containing a breaking change increases the major version.

For example, release `1.1.0` was created after adding Gemini-based meeting
summaries, which was a new backward-compatible feature. Release `1.1.1` was
created after fixing the validation of the Gemini API key, which was a bug fix.

## Creating a new release

A new release is created through the following workflow:

1. Create a feature or fix branch.
2. Implement the change.
3. Write a Conventional Commit message, such as `feat: add a new feature` or
   `fix: correct an error`.
4. Open a pull request and merge it into `master`.
5. Wait for the CI/CD workflow to complete.
6. The deploy workflow runs semantic-release.
7. If semantic-release determines that a new version is necessary, it updates
   the version, creates a Git tag, publishes a GitHub release, and releases the
   Python package to PyPI.

The user does not need to manually increment the version number or create a
release tag. This