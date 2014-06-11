These githooks are designed to run across all Wikia repositories. They are
general coding convention guidelines enforced by code quality tools to
ensure a high code quality among all codebases.

Installation
------------

```sh
cd path/to/git/repository
/path/to/guidelines/githooks/install.sh
```

The installation script will remove any existing `pre-commit` hook currently
in the repository, and replace it with a symlink to the [`pre-commit`][pre-commit]
script in this directory. The install script MUST be run from the root of the
git repository where you intend to install these hooks.

Updating
--------

```
cd /path/to/guidelines
git pull
```

Since the `pre-commit` script is a symlink to this repository, all common
githooks in the [`githooks/pre-commit.d/`][pre-commit.d] directory will be run.
Updating the repository is enough to update all of the githooks across all
repositories using these githooks

Custom Hooks
------------

In addition to the common hooks in `githooks/pre-commit.d/` that will run
for all repositories linking to this one, custom hooks may be defined on a
per-repository basis. To create a new hook, run:

```sh
mkdir -p .git/hooks/pre-commit.d
touch .git/hooks/pre-commit.d/your-new-hook
chmod +x .git/hooks/pre-commit.d/your-new-hook
```

All hooks are invoked exactly once before each commit, with the list of
files changed in the given commit as the arguments and two convenience
variables defined: `$ROOT_DIR`, which points to the absolute path of the
guidelines repository root, and `$REVISION`, which is the git revision
being diffed against. Examples of filtering the list of files and invoking
a specific command once per file or over the entire list of filtered files
in one call can be found in the [`guidelines/githooks/pre-commit.d`][pre-commit.d] directory.

Contributing
------------

Changes to the global githooks should be submitted as pull requests against
the Wikia Guidelines repository and be reviewed by the individuals reponsible
for the coding conventions and style guides for the respective languages.
The general cross-repository githooks should run quickly, since they will be
invoked many times across many commits by many developers, and while ensuring
the highest possible code quality is important, impeding developer speed with
a time-intensive githook is something better left to an offline process such
as Jenkins, Code Climate, or Travis, which can create a Github commit status
visible in pull requests.

[pre-commit]: //github.com/Wikia/guidelines/blob/master/githooks/pre-commit
[pre-commit.d]: //github.com/Wikia/guidelines/tree/master/githooks/pre-commit.d
