# CLAUDE.md — labs

Guidance for working on any lab in this directory.
Copied into each published lab repo, so it must read sensibly to a student as well as to an agent.

## Julia environments: always build with Pkg, never by hand

**Never hand-write a `Project.toml` dependency line, and never guess a UUID.** Add packages by running Pkg from inside the lab directory:

```bash
julia --project=. -e 'using Pkg; Pkg.add(["CSV", "DataFrames"])'
```

Pkg writes the correct UUID and a consistent `Manifest.toml` by construction.
A guessed UUID either fails at instantiate time with "expected package X to be registered" or silently resolves to a different package sharing the name.
Never copy a dependency block from another lab; run `Pkg.add` in this one.

Verify with `Pkg.instantiate()` and read the output before reporting an environment as working.

Every lab has its own environment, and its first `{julia}` cell activates it:

```julia
using Pkg
Pkg.activate(@__DIR__)
Pkg.instantiate()
```

`@__DIR__` is the lab's own directory whether the notebook is rendered on its own or as part of the site, so the lab gets its packages no matter where it is run from.
A lab that reads its packages from the root environment breaks the moment it ships to a student, who has the lab and nothing else.

On a fresh clone, or whenever a render fails with "Package X is required but does not seem to be installed", instantiate the environment from the lab directory before doing anything else:

```bash
julia --project=. -e 'using Pkg; Pkg.instantiate()'
```

The first run downloads and precompiles everything and can take 15 minutes or more; after that it verifies in seconds, so there is no need to guard or skip it.

## Solution cells

Every `{julia}` cell carries an explicit disposition in its cell options:

```julia
#| solution: true
```

`true` strips the cell from the student copy; `false` keeps it.
The sanitizer's guard fails the build on any cell missing the key, so a cell cannot be forgotten.
This is not optional and there is no default.

Write the solution inline in the instructor copy.
Do not keep a parallel solutions file.

Prose solutions and teaching notes go in a `::: {.instructor}` div, which the sanitizer removes whole from the student copy.
A `{julia}` cell inside an instructor div still needs its `#| solution: true` key.

Check a lab with `julia --project=. scripts/publish.jl --check` from the repo root, which names every offending cell by file and line.
In the instructor repo, the `check-labs` skill runs that and fixes what it reports.

## What a lab is

A short notebook, shipped pre-filled and working, plus a few exercises that modify or extend it.
Students are expected to use an LLM on the exercises.
A lab is started together in the Friday session and due a week later, as a rendered PDF and a repository link on Canvas.
It is worth 3 points, graded on the answers the student writes in the **Your answer** blocks rather than on the code.

Keep the notebook runnable end to end at all times.
A lab that errors on a fresh checkout is broken, whatever its content says.
