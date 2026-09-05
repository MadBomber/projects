# projects — Workspace-Level Asgard Task Toolkit

This repo is the **workspace layer** of a three-tier task organization built on
[Asgard](https://github.com/madbomber/asgard), a Thor-based task runner whose
tasks are defined in `.loki` files (see the
[Asgard documentation site](https://madbomber.github.io/asgard) for the full
`.loki` DSL and feature set). It standardizes how I manage *projects that
are families of components* — a core gem plus its extension gems, demo apps,
and related repos — so that every project workspace gets the same cross-repo
tasks without copying any code.

## The Three Layers

| Layer | Repo | Scope | Example tasks |
|-------|------|-------|---------------|
| Component | [madbomber/dev](https://github.com/MadBomber/dev) | One repo (a gem, a Rails app) | `test`, `flog_check`, `reek_check`, `release`, `pull` |
| Workspace | **this repo** | One project = many component repos | `check`, `quality_all`, `bump_versions`, `clone` |
| Project | `*_project/` directories (local, gitignored) | The actual working directories | entry-point `.loki` per project |

The layers work in concert:

- **`madbomber/dev`** holds the shared *per-component* tasks. Each component
  repo's `.loki` (or a shared `repo_dev.loki`) pulls them in with
  `import_up "dev/quality.loki"`, `import_up "dev/gem_tasks.loki"`, etc.
- **This repo** holds the shared *workspace* tasks in `workspace/*.loki`. They
  operate across all of a project's component repos at once — and many of them
  (`fetch`, `pull`, `push`, `quality_all`) delegate down to each component's
  own Asgard tasks, so the two layers compose rather than duplicate.
- **Each project directory** (e.g. `robot_lab_project/`, `sqa_project/`) is a
  local working directory containing clones of that project's component repos
  plus a small `.loki` entry point that imports the workspace toolkit:

  ```ruby
  import_up "workspace/ws_repos.loki"   # repo lists first
  import_up "workspace/*.loki"          # then the task modules
  ```

`import_up` walks up the directory tree from wherever `asgard` is run until it
finds the named file, which is why nesting is what wires everything together:

```
~/sandbox/git_repos/madbomber/
├── dev/                        # component-level task libraries (own repo)
└── projects/                   # THIS repo
    ├── README.md
    ├── workspace/              # workspace-level task libraries (tracked)
    │   ├── ws_repos.loki       #   shared repo lists — always imported first
    │   ├── ws_overview.loki
    │   ├── ws_git.loki
    │   ├── ws_quality.loki
    │   ├── ws_gems.loki
    │   └── ws_sync.loki
    ├── robot_lab_project/      # local project workspace (gitignored)
    │   ├── .loki               #   entry point → import_up workspace/*.loki
    │   ├── repo_dev.loki       #   per-component tasks → import_up dev/*.loki
    │   ├── Rakefile.common     #   synced to extensions by `asgard sync_rakefiles`
    │   ├── robot_lab/          #   core gem
    │   ├── robot_lab-a2a/      #   extension gems: -am, -audit, -cyborg,
    │   ├── robot_lab-.../      #     -discovery, -document_store, -durable,
    │   ├── robot_lab-rails/    #     -ractor, -sandbox, -to, -web, ...
    │   └── aia/                #   independent app (depends on core, not "robot_lab-*")
    └── sqa_project/            # another local project workspace (gitignored)
        ├── .loki               #   entry point (+ clone override for renamed remotes)
        ├── repo_dev.loki       #   per-component tasks → import_up dev/*.loki
        ├── ws_version.loki     #   project-only: lockstep set_version/bump + CHANGELOGs
        ├── sqa/                #   core gem
        ├── sqa-tai/            #   TA-Lib wrapper gem (core depends on IT — not an extension)
        ├── sqa-cli/            #   extension gems ...
        ├── sqa-advisor/
        ├── sqa-sinatra/
        └── sqa-rails/          #   Rails 8 demo app (not a gem)
```

A project workspace isn't limited to what the shared toolkit provides. Each one
also carries **project-local task files** that layer on top of it: a
`repo_dev.loki` that the project's component repos `import_up` to pull in the
`dev/` layer, optionally extra `ws_*.loki` modules with no shared counterpart
(sqa's `ws_version.loki` adds coordinated `set_version`/`bump` with CHANGELOG
updates and per-repo commits), and task **overrides** in the entry-point `.loki`
itself — a later definition wins, which is how `sqa_project` replaces the shared
`clone` with one that maps its renamed GitHub remotes (`sqa-rails` →
`sqa_demo-rails`) and can bootstrap a fresh workspace from an explicit repo
list.

Only `workspace/`, `.envrc`, and `.gitignore` are tracked here. The
`*_project/` directories are deliberately gitignored: they are scratch
workspaces assembled from other repos (each component keeps its own history),
and `asgard clone` can rebuild one from nothing.

## Environment Layering with direnv (`.envrc`)

The task layering is mirrored by an **environment layering** built on
[direnv](https://direnv.net) (`brew install direnv`, then hook it into your
shell with `eval "$(direnv hook zsh)"` — or `bash` — in your shell rc file).
direnv automatically loads a directory's `.envrc` when you `cd` in and unloads
it when you leave, so every shell — and every `asgard` run — gets the right
environment for wherever it is standing.

The glue is the `source_up` directive: an `.envrc` that begins with
`source_up` first loads the nearest `.envrc` *above* it, then applies its own
exports on top. Chained through every level, a shell sitting in a component
repo inherits the entire stack, with each level free to add vars or override
inherited ones (an export after `source_up` wins over the parent's value):

| Level | `.envrc` exports | Purpose |
|-------|------------------|---------|
| `madbomber/` | `MADBOMBER_SOFTWARE`, `DEV` | Workspace root; where the `dev/` task libraries live |
| `projects/` (this repo) | `PROJECTS`, `WS` | This directory; the shared `workspace/` toolkit location |
| `<name>_project/` | `PROJECT`, `RR`, `WORKING`, `BUNDLE_GEMFILE` | Project name; project root; which Gemfile bundler uses workspace-wide |
| component repo | `RR`, `BUNDLE_GEMFILE`, (`RAILS_ROOT` in Rails apps) | Repo root (overrides the project-level `RR`); per-repo Gemfile choice |

Two of these carry real behavior, not just convenience:

- **`BUNDLE_GEMFILE`** is how a whole project switches between locally-wired
  gems (`Gemfile.local`, with `path: ../<core>` overrides) and released gems
  (`Gemfile`). The `asgard set_env` task works by rewriting exactly this line
  in the project's `.envrc` and re-running `direnv allow` — the environment
  file *is* the switch.
- **`RAILS_ROOT`** is the signal `repo_dev.loki` uses to decide whether to
  `import_up "dev/quality_rails.loki"`. Asgard runs as a separate process
  outside the app it is checking, so `defined?(Rails)` can never see the app's
  Rails constant — a Rails repo's own `.envrc` exporting `RAILS_ROOT=$RR` is
  what tells the task layer "this component is a Rails app."

`RR` always points at the *nearest* enclosing root (component beats project),
which keeps shell aliases like `rr='cd $RR'` meaningful at every depth.

One direnv caution: it refuses to load an `.envrc` it hasn't been explicitly
trusted with — after creating or hand-editing one, run `direnv allow` in that
directory or every subsequent command runs with a silently stale environment.

## Auto-Configuration: No Per-Project Editing

`workspace/ws_repos.loki` derives everything from the filesystem, so the same
toolkit serves any core+extensions gem family (robot_lab, sqa, ...) unchanged:

- `@@repos` — every immediate subdirectory that is a git repo
- `@@gems` — repos carrying a `*.gemspec`
- `@@core` — the gem whose name (plus `-`) prefixes an extension family
  (`robot_lab` for `robot_lab-*`)
- `@@extensions` — gems named `<core>-*` that also declare a gemspec
  dependency on the core (both signals required, keeping look-alikes out)
- `@@family` — core + extensions: the release unit that shares config and
  moves in version lockstep

A project's `.loki` can also set these class variables explicitly (as
`sqa_project` does) when the auto-detection isn't what you want.

## The Workspace Task Files

Run `asgard help` inside any project directory to see all of these live.

### `workspace/ws_repos.loki` — shared repo lists

No user-facing tasks. Defines the `@@repos` / `@@gems` / `@@core` /
`@@extensions` / `@@family` lists described above, plus the `in_each` helper
that runs `asgard <task>` inside each component repo — the mechanism that lets
a component override a workspace behavior by defining its own task of the same
name. Always imported first so the other modules can reference the lists.

### `workspace/ws_overview.loki` — workspace health dashboard

| Task | What it does |
|------|--------------|
| `check` | Full health dashboard — runs `status`, `unpushed`, `paths`, and `versions` (the default task in project entry points) |
| `status` | Show uncommitted changes across all repos |
| `unpushed` | Show commits not yet pushed to each repo's remote |
| `paths` | Show active `path:` overrides in Gemfiles (local sibling wiring) |
| `versions` | Check extension version lockstep against the core gem — per-repo version, branch, and published RubyGems version, with PASS/FAIL badges |

### `workspace/ws_git.loki` — git operations across all repos

| Task | What it does |
|------|--------------|
| `clone` | Clone every repo in `@@repos` from GitHub; safe to re-run — existing directories are skipped |
| `fetch` | Fetch in all repos (delegates to each repo's own `fetch` task) |
| `pull` | Pull in all repos (delegates to each repo's own `pull` task) |
| `push` | Push in all repos (delegates, so a component can override) |
| `each CMD` | Run an arbitrary shell command in every repo |
| `each_gem CMD` | Run an arbitrary shell command in every gem repo |
| `branches` | Show each repo's current branch, flagging dirty working trees |

### `workspace/ws_quality.loki` — cross-repo quality gates

| Task | What it does |
|------|--------------|
| `test_all` | Run each repo's `asgard test_check` in all gem repos in parallel, output buffered per repo |
| `rubocop_all` | Run RuboCop in all gem repos in parallel |
| `rubocop_fix_all` | Run `rubocop -a` auto-fix sequentially in each gem repo |
| `quality_all` | The big one: bundle-update everything, then run every gate in parallel per repo — Test, Flay, Flog, RuboCop, Reek (plus Archspec when a repo has an `Archspec.rb`) — and print a per-gate PASS/FAIL status table |

Every gate is the repo's own `asgard *_check` task from `dev/quality.loki`
(`test_check`, `flay_check`, `flog_check`, `rubocop_check`, `reek_check`,
`archspec_check`) — a concrete example of the workspace layer orchestrating
the dev layer, with no `rake` in the loop.

### `workspace/ws_gems.loki` — gem lifecycle across all repos

| Task | What it does |
|------|--------------|
| `bundle_update_all` | `bundle update` in every gem repo, continuing past failures and summarizing |
| `bundle_install_all` | `bundle install` in every gem repo |
| `build_all` | Each gem repo's `asgard build` |
| `install_all` | `asgard install` everywhere — core first, then extensions, respecting the dependency order |
| `wire_local` | Rewrite each extension's Gemfile to use `path: "../<core>"` for cross-gem development against the local core |
| `unwire_local` | Restore each extension's Gemfile to the released core gem |

### `workspace/ws_sync.loki` — shared-file sync and version management

| Task | What it does |
|------|--------------|
| `set_env [ENV]` | Switch the workspace between `Gemfile.local` (path-wired) and `Gemfile` (released gems) by rewriting `BUNDLE_GEMFILE` in `.envrc`; omit the argument to toggle |
| `sync` | Run `sync_rakefiles`, `sync_rubocop`, and `sync_versions` together |
| `sync_versions` | Set every extension's `version.rb` to match the core gem's version |
| `sync_rakefiles` | Copy the project's `Rakefile.common` into each extension repo |
| `sync_rubocop` | Copy `.rubocop.yml.common` to every family repo (no-op in projects that use inherited RuboCop config instead) |
| `bump_versions [VERSION]` | Bump every family `version.rb` to VERSION (with confirmation, `-y` to skip); omit VERSION to sync extensions to the core's current version |

## The Day-to-Day Payoff

The point of all this layering shows up in ordinary development on a
multi-component project like `robot_lab_project`, where a "small" change can
touch a core gem plus a dozen extensions. Standing at the project root, one
command fans out across every component — no cd-ing into fourteen repos, no
forgetting one:

```bash
asgard each 'bundle update'       # any shell command, in every component repo
asgard each_gem 'asgard install'  # same, limited to the gem repos
```

`each` and `each_gem` are the escape hatches: whatever one-off command you
would have typed repo by repo, they run everywhere. For the recurring
operations, the curated tasks go further than a plain fan-out —
`asgard install_all` installs the core gem *first* and then the extensions
(which depend on it), `asgard quality_all` runs every gate in parallel and
summarizes them in one table, and `asgard check` answers "what state is this
whole project in?" before you start typing.

The same muscle memory works in every project workspace: `asgard check`,
`asgard test_all`, `asgard each '...'` mean the same thing in
`robot_lab_project` and `sqa_project`, because they are literally the same
code — parameterized by each workspace's auto-detected repo lists rather than
copied and drifted.

## Versioning Convention

Family gems move in **lockstep**: an extension's first three version segments
must match the core gem's version. An extension may append a fourth segment
for an extension-only patch (`0.4.2.1` against core `0.4.2`). `asgard versions`
enforces this; `bump_versions` and `sync_versions` maintain it.

## Starting a New Project Workspace

1. `mkdir <name>_project` under this directory (already gitignored).
2. Add a `.loki` entry point that does `import_up "workspace/ws_repos.loki"`
   then `import_up "workspace/*.loki"`, plus a `header`, `footer`, and
   `default_task :check`.
3. Add an `.envrc` starting with `source_up`, plus the project-level exports
   (`PROJECT`, `RR`, `WORKING`, `BUNDLE_GEMFILE` — see the direnv section),
   and run `direnv allow`.
4. Add a `repo_dev.loki` that `import_up`s the `dev/` task files, so each
   component repo's own `.loki` can import it for the per-component layer.
5. List the component repos in the project `.loki` — or just clone them and
   let auto-detection find them: `asgard clone` / `git clone` as needed.
   (Auto-detected `@@repos` only sees existing directories, so a `clone` that
   must bootstrap a fresh workspace needs an explicit repo list — see
   `sqa_project/.loki` for the pattern.)
6. `asgard check` to confirm the dashboard sees everything.
