---
title: CI/CD
has_children: false
nav_order: 9
---

# CI/CD

MeetNote uses GitHub Actions to automate quality checks, testing, and releases.
The CI/CD workflow helps ensure that changes do not break the application and
that new versions can be released in a consistent and repeatable way.

The project has two main workflows:

- `check.yml`, which performs continuous integration and starts the deployment
  workflow.
- `deploy.yml`, which performs the release and publication process.

## Continuous integration

The `check.yml` workflow runs when code is pushed to the repository, when a
pull request is opened or updated, or when the workflow is started manually.

It is not executed for pushes to Dependabot or Renovate branches, and it ignores
changes to files that do not affect the application code, such as `README.md`,
`LICENSE`, `.gitignore`, and Renovate configuration files.

The workflow contains three main jobs:

1. **Preliminary Checks**
2. **Tests**
3. **Deploy**

### Preliminary checks

The first job installs Poetry, restores the development environment, and
performs static quality checks.

It runs the following commands:

```bash
poetry run poe compile
poetry run poe static-checks
poetry run poe format-check
```

These commands verify that:

- The Python code can be compiled.
- The code passes Ruff static checks.
- The code follows the project’s formatting rules.
- The code passes mypy static type checking.

After these checks, the workflow runs the automated test suite with coverage:

```bash
poetry run poe coverage
poetry run poe coverage-report
poetry run poe coverage-html
```

The workflow then uploads the generated HTML coverage report as a GitHub
Actions artifact. This allows developers to inspect which parts of the code are
covered by tests.

### Automated tests

The second job runs only after the preliminary checks succeed. It tests the
application on multiple operating systems and Python versions.

The workflow uses a test matrix with:

- Ubuntu, Windows, and macOS.
- Python versions 3.9, 3.10, 3.11, 3.12, and 3.13.

For every combination, the workflow:

1. Sets up the selected Python version.
2. Checks out the repository.
3. Installs Poetry.
4. Installs the project dependencies.
5. Runs the test suite with:

```bash
poetry run poe test
```

Testing the application on multiple operating systems and Python versions
reduces the risk of introducing environment-specific errors.

## Continuous delivery

The `deploy.yml` workflow is called by the CI/CD workflow after the test job
completes successfully. It can also be started manually.

The deployment workflow is responsible for creating a new release and
publishing the Python package.

It performs the following steps:

1. Checks out the repository with its full Git history.
2. Fetches all Git tags.
3. Installs Poetry and project dependencies.
4. Installs Node.js, which is required by semantic-release.
5. Runs semantic-release.
6. Creates a Git tag and GitHub release when a new version is needed.
7. Publishes the Python package to PyPI.

The release process uses semantic-release to analyze the commit history. It
decides whether a new version is needed and whether the new version should be a
major, minor, or patch release.

The workflow is configured to perform a dry run when it is not running on the
`master` or `main` branch, or when it is processing the initial commit. This
prevents accidental package publication during development.

## GitHub Actions secrets

The deployment workflow uses GitHub Actions secrets to store sensitive values
securely.

The following secrets are used:

| Secret | Purpose |
|---|---|
| `GH_TOKEN` | Used by semantic-release to create GitHub tags and releases |
| `PYPI_TOKEN` | Used to authenticate with PyPI and publish the Python package |

These secrets are not written directly in the workflow file or in the source
code. GitHub Actions provides them to the workflow at runtime.

The workflow also defines the necessary permissions:

- `contents: write`, needed to create tags and GitHub releases.
- `packages: write`, needed for package-related operations.

## Workflow summary

The complete CI/CD process can be summarized as follows:

```text
Push or pull request
        |
        v
Preliminary checks
- Compile code
- Ruff checks
- Format check
- mypy
- Tests with coverage
        |
        v
Cross-platform tests
- Ubuntu, Windows, macOS
- Python 3.9 to 3.13
        |
        v
Deploy workflow
- semantic-release
- GitHub release and tag
- PyPI publication
```

This automation reduces manual work, improves code quality, and ensures that
new versions of MeetNote are released consistently.