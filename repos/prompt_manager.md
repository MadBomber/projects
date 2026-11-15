# prompt_manager

Prompt file management gem used by aia. A change to a public method must not break aia; run aia's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/prompt_manager
- RubyGems: https://rubygems.org/gems/prompt_manager
- Issues: https://github.com/MadBomber/prompt_manager/issues

## Status

_Updated 2026-11-15 (twelfth run, madbot)._ Tender rebased #56/#57 (still waiting). Opened [#58](https://github.com/MadBomber/prompt_manager/pull/58) (Fixes #54), [#59](https://github.com/MadBomber/prompt_manager/pull/59) (Fixes #52, **merged low risk** as develop 5d9ac2c) and [#63](https://github.com/MadBomber/prompt_manager/pull/63) (F-59 BOM fix). Dependencies audit (develop 03995ff): F-61..F-68. Filed issues [#60](https://github.com/MadBomber/prompt_manager/issues/60), [#61](https://github.com/MadBomber/prompt_manager/issues/61), [#62](https://github.com/MadBomber/prompt_manager/issues/62). develop HEAD 5d9ac2c.

_Previously, eleventh run (2026-11-14):_ Queue was empty: Dewayne merged #36/#40/#41 and fixed every issue BomberBot filed on 11-13 himself (#45-#47, #49-#51, squash merges). Opened [#55](https://github.com/MadBomber/prompt_manager/pull/55) (F-51 branch-coverage gate, **merged low risk** as develop 03995ff), [#56](https://github.com/MadBomber/prompt_manager/pull/56) and [#57](https://github.com/MadBomber/prompt_manager/pull/57). Incremental **dead-code** audit (develop 881aaea): nothing dead; F-53..F-60. Filed issues [#52](https://github.com/MadBomber/prompt_manager/issues/52), [#53](https://github.com/MadBomber/prompt_manager/issues/53), [#54](https://github.com/MadBomber/prompt_manager/issues/54). develop HEAD 03995ff.

_Previously, tenth run (2026-11-13 03:15):_ queue full (#36, #40, #41); security audit F-47..F-49 plus S-6; filed #42-#44 (all since fixed by Dewayne).

_Previously, ninth run (2026-11-13 02:20):_ Dewayne merged all four waiting PRs (#25, #26, #31, #35) on 2026-11-10, so the queue was empty. Ran the second **quality** audit (develop 37420ea): F-38..F-46 plus S-5. Opened three PRs: [#40](https://github.com/MadBomber/prompt_manager/pull/40) (Fixes #32), [#41](https://github.com/MadBomber/prompt_manager/pull/41) (F-34, the eighth run's never-opened PR), [#36](https://github.com/MadBomber/prompt_manager/pull/36) (Fixes #33). Filed issues #37, #38, #39. develop HEAD 37420ea.

_Previously, eighth run (2026-11-10):_ dead-code audit; #31/#35 opened and later merged by Dewayne; issues #32-#34 filed.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Issue [#54](https://github.com/MadBomber/prompt_manager/issues/54): list the reserved directive names in `docs/guides/custom-directives.md` "Name Format" (F-56). Docs-only; not self-mergeable (Pages deploys from develop). Added 2026-11-14. **PR open** [#58](https://github.com/MadBomber/prompt_manager/pull/58).
- Issue [#53](https://github.com/MadBomber/prompt_manager/issues/53): name the command (or say "empty") in the `$()` ShellExpansionError (F-55). lib; wait for Dewayne's #48 to merge first (both touch `test/shell_safety_test.rb`). Added 2026-11-14.
- Issue [#60](https://github.com/MadBomber/prompt_manager/issues/60): tag `$VAR`/`$(cmd)` output UTF-8 so non-ASCII expansion doesn't crash under `LANG=C` (F-58). lib; test in `test/shell_test.rb`. Added 2026-11-15.
- Issue [#61](https://github.com/MadBomber/prompt_manager/issues/61): pass `filename:` and a line offset to `YAML.safe_load` (F-63). lib. Added 2026-11-15.
- Issue [#62](https://github.com/MadBomber/prompt_manager/issues/62): `docs/api/configuration.md` `prompts_dir` type (F-64). Docs one-liner. Added 2026-11-15.
- F-65: test `~` expansion in `prompts_dir` (test-only). Added 2026-11-15.

## Open bomberbot PRs

- [#63](https://github.com/MadBomber/prompt_manager/pull/63) fix: strip a UTF-8 byte order mark when reading prompt files (**PR open**, targets develop; CI green; F-59; adds `PM.read_file`; review clean in 2 rounds; aia suite 1998/0 green; waits for Dewayne: lib)
- [#58](https://github.com/MadBomber/prompt_manager/pull/58) docs: list reserved directive names in the custom directives guide (**PR open**, targets develop; Fixes #54; CI green; +23 docs; review clean; not self-merged: `docs/` deploys Pages)
- [#57](https://github.com/MadBomber/prompt_manager/pull/57) docs: describe front-matter fence rules and UTF-8 reading in the parsing guide (**PR open**, targets develop; CI green; docs only, 16 lines; rebased onto 03995ff 2026-11-15; every claim run, `mkdocs build --strict` clean; review hit the 3-round cap, each round finding new edge cases in the same paragraph; not self-merged: cap plus `docs/` deploys Pages; waits for Dewayne)
- [#56](https://github.com/MadBomber/prompt_manager/pull/56) test: silence Encoding.default_external warnings in encoding test (**PR open**, targets develop; CI green; test only, +24/-2; rebased onto 03995ff 2026-11-15; rake test warnings 4 -> 0, guard test added; review hit the 3-round cap with minor notes only; waits for Dewayne)

Merged: [#59](https://github.com/MadBomber/prompt_manager/pull/59) (2026-11-15, **merged (low risk)** by madbot: directive tests, Fixes #52, develop 5d9ac2c), [#55](https://github.com/MadBomber/prompt_manager/pull/55) (2026-11-14, **merged (low risk)** by mini_bomberbot: branch-coverage gate in `test/test_helper.rb` + README comment, develop 03995ff), [#36](https://github.com/MadBomber/prompt_manager/pull/36), [#40](https://github.com/MadBomber/prompt_manager/pull/40), [#41](https://github.com/MadBomber/prompt_manager/pull/41) (2026-11-13 by Dewayne, squash, no comments), [#25](https://github.com/MadBomber/prompt_manager/pull/25), [#26](https://github.com/MadBomber/prompt_manager/pull/26), [#31](https://github.com/MadBomber/prompt_manager/pull/31), [#35](https://github.com/MadBomber/prompt_manager/pull/35) (all 2026-11-10 by Dewayne, no comments), [#19](https://github.com/MadBomber/prompt_manager/pull/19), [#20](https://github.com/MadBomber/prompt_manager/pull/20), [#21](https://github.com/MadBomber/prompt_manager/pull/21), [#22](https://github.com/MadBomber/prompt_manager/pull/22) (all 2026-11-07, no comments), [#23](https://github.com/MadBomber/prompt_manager/pull/23), [#24](https://github.com/MadBomber/prompt_manager/pull/24) (both 2026-11-09, merge commits, no comments), [#27](https://github.com/MadBomber/prompt_manager/pull/27) (2026-11-10, **self-merged by madbot as low risk** — docs-only `Metadata#==` fix, develop 39827bf), [#29](https://github.com/MadBomber/prompt_manager/pull/29) (2026-11-10, **self-merged low risk** — `--show-toplevel` docs fix, c2eab17), [#30](https://github.com/MadBomber/prompt_manager/pull/30) (2026-11-10, **self-merged low risk** — ai-agent include paths, b01cc88), [#28](https://github.com/MadBomber/prompt_manager/pull/28) (2026-11-10, **self-merged low risk** — PM::VERSION doc example 1.2.0, 9219201).
