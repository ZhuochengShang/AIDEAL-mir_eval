# mir_eval — AIDEAL five-condition preparation

**Setup only: five baseline source branches; treatments and evaluation pending. No model is selected; this new setup made zero model calls.**
The date in the study/ref names is the setup start date, 2026-09-21; preparation continued after midnight.

[Shared AIDEAL study guide](https://github.com/ZhuochengShang/AIDEAL/blob/setup/2026-09-21-library-five-conditions/studies/mir_eval/2026-09-21-five-condition-setup/README.md) · [Source/setup identities](study.json) · [Shared workflow](https://github.com/ZhuochengShang/AIDEAL/blob/setup/2026-09-21-library-five-conditions/docs/SUMMARY.md)

The source repository and shared AIDEAL serve different purposes: these branches hold library source; shared AIDEAL holds prompts, controller/harness code, settings conventions and future reports. The companion repository requires private access.
Publication policy keeps GitHub Actions disabled while preparation is pending; upstream CI has not validated this setup.

## Source identity and attribution

Upstream: [https://github.com/mir-evaluation/mir_eval.git](https://github.com/mir-evaluation/mir_eval.git); selected commit [fe73b3533737814f83dbd9739f06e90f5f82f758](https://github.com/mir-evaluation/mir_eval/commit/fe73b3533737814f83dbd9739f06e90f5f82f758).
Preparation snapshot: [7405db3ea4d82d15c7a2cf7ba5f1ef78b4989c39](https://github.com/ZhuochengShang/AIDEAL-mir_eval/commit/7405db3ea4d82d15c7a2cf7ba5f1ef78b4989c39); tree `3a5a01ea65b8db088f3e71da0a5c1b5ab90fa788` is exactly the upstream tree. This new commit does not ship upstream ancestry.

Original documentation selected for future attachment: [README.rst](https://github.com/ZhuochengShang/AIDEAL-mir_eval/blob/7405db3ea4d82d15c7a2cf7ba5f1ef78b4989c39/README.rst). Source-branch root READMEs remain upstream bytes. This navigation README on `main` is not a documentation treatment. A future installed generated README is selected from `.aideal/treatments/README.md` in the relevant condition.

Upstream license/notice files are copied byte-for-byte: [LICENSE.txt](LICENSE.txt). They retain their original attribution and terms; this setup does not relicense upstream source.

## Five conditions

| Condition | Intended difference from original | Actual state |
| --- | --- | --- |
| [Original](https://github.com/ZhuochengShang/AIDEAL-mir_eval/tree/preparation/2026-09-21-five-conditions/original) | Original documentation and APIs | Baseline; not evaluated |
| [README only](https://github.com/ZhuochengShang/AIDEAL-mir_eval/tree/preparation/2026-09-21-five-conditions/readme-only) | Select a reviewed generated README | Same baseline; treatment pending; not evaluated |
| [Alias only](https://github.com/ZhuochengShang/AIDEAL-mir_eval/tree/preparation/2026-09-21-five-conditions/alias-only) | Add tested delegating aliases and their interface | Same baseline; treatment pending; not evaluated |
| [Error hints only](https://github.com/ZhuochengShang/AIDEAL-mir_eval/tree/preparation/2026-09-21-five-conditions/error-hints-only) | Deliver matched development-error hints on repair | Same baseline; treatment pending; not evaluated |
| [Combined](https://github.com/ZhuochengShang/AIDEAL-mir_eval/tree/preparation/2026-09-21-five-conditions/combined) | Combine the same README, aliases, and error hints | Same baseline; treatment pending; not evaluated |

All rows have the same commit/tree today. No historical generated README, aliases, hints or evaluation artifacts have been installed here. Branch names express the study plan, not measured differences.

## Get the source

For one clean source checkout (private repository access required):

```sh
git clone -b preparation/2026-09-21-five-conditions/original https://github.com/ZhuochengShang/AIDEAL-mir_eval.git AIDEAL-mir_eval
```

Or clone the navigation branch once and create five **distinct named branches/worktrees**:

```sh
git clone https://github.com/ZhuochengShang/AIDEAL-mir_eval.git AIDEAL-mir_eval
cd AIDEAL-mir_eval
git worktree add -b review/original ../mir_eval-original origin/preparation/2026-09-21-five-conditions/original
git worktree add -b review/readme-only ../mir_eval-readme-only origin/preparation/2026-09-21-five-conditions/readme-only
git worktree add -b review/alias-only ../mir_eval-alias-only origin/preparation/2026-09-21-five-conditions/alias-only
git worktree add -b review/error-hints-only ../mir_eval-error-hints-only origin/preparation/2026-09-21-five-conditions/error-hints-only
git worktree add -b review/combined ../mir_eval-combined origin/preparation/2026-09-21-five-conditions/combined
```

All five currently resolve to `7405db3ea4d82d15c7a2cf7ba5f1ef78b4989c39`. Sharing a commit is intentional; branch names are different.
`main` contains this navigation guide. Use a preparation branch/worktree for the actual source, not `main`.


## Before evaluation

Historical 148/148 generated README is a reuse candidate only; not installed in this new study.

Use the [shared attachment instructions](https://github.com/ZhuochengShang/AIDEAL/blob/setup/2026-09-21-library-five-conditions/studies/mir_eval/2026-09-21-five-condition-setup/README.md#prepare-your-own-local-study). Complete and validate the library runtime, independent task bank and controls, treatment artifacts, per-condition builds, model/prompt/budget settings and a new local freeze. Documentation and draft configuration alone are not a runnable evaluator. No setup test or example execution is a measured semantic score.
