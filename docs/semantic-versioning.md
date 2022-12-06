# 1. Semantic Versioning

[link to offical docs](https://semver.org/)

**Semantic Versioning** (=SemVer in short) = specification for defining versions of an application. It consists of three numbers qualifying the version of the application: **MAJOR.MINOR.PATCH**

Each number is incremented in different circumstances:
- **MAJOR** version is incremented when adding breaking changes
- **MINOR** version is incremented when adding backward compatibility functionnality
- **PATCH** version is incremented when adding backward compatible bug fixes

Additional labels for pre-release and build metadata are available as extensions to this numbering format.

In the pre development phasis, the version number starts with 0.x.x. Once the application has been published, the version of the application starts at 1.0.0.

# 2. Conventional commits

[link to official docs](https://www.conventionalcommits.org/en/v1.0.0/)

**Conventional commits** = specification fixing a set of rules for writing commit messages. It will result in a more explicit commit history.

Is synergises with the concept of SemVer by describing respective features, fixes and breaking changes made in commit messages.

## 2.1. Conventional commit messages structure

A commit message should be structured as follows:
```sh
<type>[optional scope]: <description>
[optional body]
[optional footer(s)]
```

A commit message must use a **type** and a **description**.

**Example:** `git commit -m "chore: update of .gitignore related to PDI/fichiers folders"`

In this example, `chore` is the type of the commit and `update of .gitignore related to PDI/fichiers folders` its description.

Commits must be prefixed with a **type** followed by a semi-colon `:`.

Some types will initiate automatic CHANGELOG updates et will generate an automatic release with version tag in Github (with Semantic Release):

- `feat: <description>` correlating with MINOR in semantic versioning, indicates that the commit adds a new feature to the application or library
- `fix: <description>` correlating with PATCH in semantic versioning, represents a bug fix for the application
- `BREAKING CHANGE: <footer of the commit>` correlating a breaking change that correlates with MAJOR in semantic versioning

**Examples:**
- Commit message with description with patch update:
    ```sh
    git commit -m "fix: correction of a bug in product index table"
    ```
- Commit message with description and scope with minor update:
    ```sh
    git commit -m "feat(lang): add polish language"
    ```
- Commit message introducing a breaking change in the footer:
    ```sh
    git commit -m "feat: allow provided config object to extend other configs
    BREAKING CHANGE: `extends` key in config file is now used for extending other config files"
    ```

## 2.2. Other commit types

Other types can be used in given situations:

- `build: <description>` for commits that affect the build system or external dependencies (example scopes: gulp, broccoli, npm)
- `chore: <description>` for commits that affect the build system or external dependencies (example scopes: .gitignore, npm, etc), nothing that an external user would see
- `ci: <description>` for commits related to CI processing
- `dev: <description>` for commits related to developments in progress
- `docs: <description>` for commits related to documentation
- `install: <description>` for commits related to installation of dependencies or other tools
- `perf: <description>` for commits related to performance optimization
- `refactor: <description>` for commits related to refactoring
- `revert: <description>` for commits related to reverting code
- `style: <description>` for commit changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc)
- `test: <description>` for commits related to unit testing

:danger: No capitals in the message of a commit, no period at the end.