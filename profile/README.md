<p align="center">
  <a href="https://github.com/zuke-build/zuke">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://github.com/zuke-build/zuke/raw/master/assets/logo-white.png" />
      <img width="420px" alt="Zuke" src="https://github.com/zuke-build/zuke/raw/master/assets/logo.png" />
    </picture>
  </a>
</p>

<h3 align="center">A code-first, strongly-typed build automation system for Deno &amp; TypeScript.</h3>

<p align="center">
  <strong>Your build and your CI are one typed file. And your agent can run it.</strong>
</p>

<p align="center">
  <a href="https://github.com/zuke-build/zuke/actions/workflows/ci.yml"><img alt="CI" src="https://github.com/zuke-build/zuke/actions/workflows/ci.yml/badge.svg" /></a>
  <a href="https://jsr.io/@zuke/core"><img alt="JSR" src="https://jsr.io/badges/@zuke/core" /></a>
  <a href="https://jsr.io/@zuke/core"><img alt="JSR score" src="https://jsr.io/badges/@zuke/core/score" /></a>
  <a href="https://codecov.io/gh/zuke-build/zuke"><img alt="Coverage" src="https://codecov.io/gh/zuke-build/zuke/branch/master/graph/badge.svg" /></a>
  <a href="https://scorecard.dev/viewer/?uri=github.com/zuke-build/zuke"><img alt="OpenSSF Scorecard" src="https://api.scorecard.dev/projects/github.com/zuke-build/zuke/badge" /></a>
  <a href="https://www.bestpractices.dev/projects/14036"><img alt="OpenSSF Best Practices" src="https://www.bestpractices.dev/projects/14036/badge" /></a>
  <a href="https://github.com/marketplace/actions/zuke-build"><img alt="GitHub Marketplace" src="https://img.shields.io/github/v/release/zuke-build/zuke?filter=%21%2A-%2A&amp;label=Marketplace&amp;logo=github&amp;color=2ea44f" /></a>
  <a href="https://github.com/zuke-build/zuke/blob/master/LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-yellow.svg" /></a>
  <a href="https://deno.com/"><img alt="Built for Deno" src="https://img.shields.io/badge/Deno-2.x-000?logo=deno&logoColor=white" /></a>
</p>

<p align="center">
  <a href="https://zuke.build"><strong>zuke.build</strong></a> ·
  <a href="https://github.com/zuke-build/zuke#readme">README</a> ·
  <a href="https://github.com/zuke-build/zuke/tree/master/docs">Docs</a> ·
  <a href="https://jsr.io/@zuke">Packages on JSR</a> ·
  <a href="https://github.com/zuke-build/zuke/tree/master/examples">Examples</a>
</p>

---

## The build is a class. The targets are fields. The CI is generated.

```ts
import { Build, cicd, run, target } from "@zuke/core";
import { DenoTasks } from "@zuke/deno";

class MyBuild extends Build {
  ci = cicd({ provider: "github" }); // the pipeline, generated and verified

  lint = target().executes(() => DenoTasks.lint());

  test = target()
    .dependsOn(this.lint) // this.lint, not "lint": a rename is a refactor
    .executes(() => DenoTasks.test((s) => s.allowAll().coverage("cov")));

  default = target().dependsOn(this.test).executes(() => {});
}

await run(MyBuild);
```

No bespoke DSL. No YAML by hand. No `scripts/*.sh` nobody dares touch. One
TypeScript file, with full editor support, that Zuke turns into a dependency
graph, runs in order, and compiles into your CI workflow on every run.

<p align="center">
  <img src="https://github.com/zuke-build/zuke/raw/master/assets/demo.svg" alt="Zuke in action: scaffold a build, list targets, run the gate" width="760" />
</p>

## Five minutes to a typed build

```sh
# 1. Install the CLI (pick one)
deno install -A -g -n zuke jsr:@zuke/cli      # from JSR, any OS with Deno
brew install zuke-build/tap/zuke              # Homebrew, no Deno needed up front

# 2. Scaffold, run, and generate CI
zuke setup          # writes zuke.ts + the ./zuke launcher
zuke                # runs the build (or ./zuke, no install needed)
zuke generate-ci    # writes .github/workflows/ci.yml from the build
```

Already have `package.json` scripts or a `Makefile`? `zuke import` turns them
into a `zuke.ts` with a target per script.

## Why Zuke

| | |
| --- | --- |
| **Typed, refactor-safe dependencies** | Targets reference each other with `this.clean`, never `"clean"`. A typo is a compile error, not a 3 a.m. surprise. |
| **Never write CI YAML again** | `cicd({ provider: "github" })` generates GitHub Actions, GitLab CI, Azure Pipelines, or Bitbucket YAML. `fanOut: true` makes every target its own job wired by `needs:` edges. `generate-ci --check` fails CI when it drifts. |
| **Run it locally, exactly as CI does** | `./zuke ci` is the same target the workflow runs. One gate, not a hand-maintained subset. |
| **Let your agent run the build** | `./zuke mcp` serves the build over the Model Context Protocol: an agent lists the targets, reads the graph, and runs one with typed parameters. |
| **A typed wrapper for every tool** | 68 packages: a tiny core, the CLI, and a `*Tasks` object per tool whose settings lambdas mirror the real flags. |
| **Injection-safe shell** | The `$` tagged template from `@zuke/core/shell` tokenises interpolated values into argv entries, never a concatenated string. |
| **Zero runtime dependencies** | The published packages depend on nothing. The test suite runs with no network at all. |

## A typed wrapper for every tool

Every external tool is a namespaced `*Tasks` object configured with a settings
lambda that mirrors the real CLI, so `DenoTasks.test((s) => s.allowAll())` reads
like `deno test --allow-all` and the flags are type-checked.

| Category | Packages |
| --- | --- |
| **Runtimes & package managers** | [`deno`](https://jsr.io/@zuke/deno) · [`node`](https://jsr.io/@zuke/node) · [`npm`](https://jsr.io/@zuke/npm) · [`npx`](https://jsr.io/@zuke/npx) · [`pnpm`](https://jsr.io/@zuke/pnpm) · [`yarn`](https://jsr.io/@zuke/yarn) · [`bun`](https://jsr.io/@zuke/bun) |
| **Bundlers & monorepo** | [`vite`](https://jsr.io/@zuke/vite) · [`tsup`](https://jsr.io/@zuke/tsup) · [`tsdown`](https://jsr.io/@zuke/tsdown) · [`storybook`](https://jsr.io/@zuke/storybook) · [`turbo`](https://jsr.io/@zuke/turbo) · [`nx`](https://jsr.io/@zuke/nx) |
| **TypeScript runners & compilers** | [`tsc`](https://jsr.io/@zuke/tsc) · [`tsc-alias`](https://jsr.io/@zuke/tsc-alias) · [`tsx`](https://jsr.io/@zuke/tsx) |
| **Frameworks & code generation** | [`nest`](https://jsr.io/@zuke/nest) · [`openapi-ts`](https://jsr.io/@zuke/openapi-ts) · [`orval`](https://jsr.io/@zuke/orval) · [`redocly`](https://jsr.io/@zuke/redocly) |
| **AI coding, review & self-healing** | [`ai`](https://jsr.io/@zuke/ai) · [`claude`](https://jsr.io/@zuke/claude) · [`codex`](https://jsr.io/@zuke/codex) · [`gemini`](https://jsr.io/@zuke/gemini) |
| **Lint, format & quality** | [`eslint`](https://jsr.io/@zuke/eslint) · [`oxlint`](https://jsr.io/@zuke/oxlint) · [`biome`](https://jsr.io/@zuke/biome) · [`dprint`](https://jsr.io/@zuke/dprint) · [`cspell`](https://jsr.io/@zuke/cspell) · [`knip`](https://jsr.io/@zuke/knip) · [`dpdm`](https://jsr.io/@zuke/dpdm) · [`shellcheck`](https://jsr.io/@zuke/shellcheck) · [`husky`](https://jsr.io/@zuke/husky) · [`lint-staged`](https://jsr.io/@zuke/lint-staged) |
| **Test, coverage & browsers** | [`vitest`](https://jsr.io/@zuke/vitest) · [`jest`](https://jsr.io/@zuke/jest) · [`playwright`](https://jsr.io/@zuke/playwright) · [`cypress`](https://jsr.io/@zuke/cypress) · [`codecov`](https://jsr.io/@zuke/codecov) |
| **Containers & orchestration** | [`docker`](https://jsr.io/@zuke/docker) · [`docker-compose`](https://jsr.io/@zuke/docker-compose) · [`kubectl`](https://jsr.io/@zuke/kubectl) · [`kustomize`](https://jsr.io/@zuke/kustomize) · [`helm`](https://jsr.io/@zuke/helm) · [`argo-rollouts`](https://jsr.io/@zuke/argo-rollouts) · [`canary`](https://jsr.io/@zuke/canary) |
| **Cloud & infrastructure** | [`aws`](https://jsr.io/@zuke/aws) · [`az`](https://jsr.io/@zuke/az) · [`gcloud`](https://jsr.io/@zuke/gcloud) · [`terraform`](https://jsr.io/@zuke/terraform) · [`tofu`](https://jsr.io/@zuke/tofu) · [`prometheus`](https://jsr.io/@zuke/prometheus) · [`otel`](https://jsr.io/@zuke/otel) |
| **Version control, registry & CI** | [`git`](https://jsr.io/@zuke/git) · [`gh`](https://jsr.io/@zuke/gh) · [`jsr`](https://jsr.io/@zuke/jsr) · [`release-please`](https://jsr.io/@zuke/release-please) · [`homebrew`](https://jsr.io/@zuke/homebrew) · [`chocolatey`](https://jsr.io/@zuke/chocolatey) |
| **Code signing & supply chain** | [`apple`](https://jsr.io/@zuke/apple) · [`signpath`](https://jsr.io/@zuke/signpath) · [`security`](https://jsr.io/@zuke/security) |
| **Engine & plugins** | [`core`](https://jsr.io/@zuke/core) · [`cli`](https://jsr.io/@zuke/cli) · [`cmd`](https://jsr.io/@zuke/cmd) · [`console`](https://jsr.io/@zuke/console) · [`docs`](https://jsr.io/@zuke/docs) · [`qr`](https://jsr.io/@zuke/qr) |

Every one of them lives under
[`@zuke` on JSR](https://jsr.io/@zuke). The full matrix with live badges is in
[Packages](https://github.com/zuke-build/zuke/blob/master/docs/packages.md), and
the complete typed surface of every package is one file:
[`llms-full.txt`](https://github.com/zuke-build/zuke/blob/master/llms-full.txt).

## AI in your pipeline

Three ways a model joins the build, each one a typed target with refactor-safe
dependencies:

- **Drive the coding CLIs.** `@zuke/claude`, `@zuke/codex` and `@zuke/gemini`
  run Claude Code, OpenAI Codex and Gemini CLI non-interactively, with the API
  key riding a `parameter().secret()` that Zuke masks in CI output.
- **AI code review that breaks the build.** `@zuke/ai` reads the diff, returns a
  structured assessment, posts it to the pull request, and fails the run when
  the risk crosses your threshold.
- **Self-healing targets.** Attach `.recoverWith(aiFixer(…))` to any target. On
  failure it diagnoses from the error and the diff, posts a committable
  suggestion, or applies the fix and re-runs the real command to verify.

```ts
test = target()
  .executes(() => DenoTasks.test((s) => s.allowAll()))
  .recoverWith(aiFixer((f) => f.provider("openai").apiKey(this.key)));
```

## Teach your agent to write the build

Two portable [Agent Skills](https://agentskills.io), `zuke-setup` and
`zuke-write-build`, teach a coding assistant to scaffold Zuke and write a
`zuke.ts` with the typed wrappers instead of guessing the API:

```text
/plugin marketplace add zuke-build/zuke && /plugin install zuke@zuke   # Claude Code
codex plugin marketplace add zuke-build/zuke && codex plugin add zuke@zuke
gemini extensions install https://github.com/zuke-build/zuke
```

## One step in GitHub Actions

The [Zuke Build](https://github.com/marketplace/actions/zuke-build) action
hardens the runner, checks the repository out, and runs a target:

```yaml
steps:
  - uses: zuke-build/zuke@v1
    with:
      target: ci
```

## Repositories

| Repository | What it is |
| --- | --- |
| [**zuke**](https://github.com/zuke-build/zuke) | The engine, the `zuke` CLI, 68 typed packages, the docs, and Zuke's own build (`zuke.ts`), which is a runnable example |
| [**zuke-build.github.io**](https://github.com/zuke-build/zuke-build.github.io) | The website at [zuke.build](https://zuke.build) |
| [**homebrew-tap**](https://github.com/zuke-build/homebrew-tap) | The Homebrew formula behind `brew install zuke-build/tap/zuke` |

## Used in production by

<p align="center">
  <a href="https://payhawk.com">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://github.com/zuke-build/zuke/raw/master/assets/users/payhawk-white.svg" />
      <img width="170px" alt="Payhawk" src="https://github.com/zuke-build/zuke/raw/master/assets/users/payhawk.svg" />
    </picture>
  </a>
</p>

Running Zuke at your company? Open a pull request adding your logo to
[`assets/users/`](https://github.com/zuke-build/zuke/tree/master/assets/users),
or say hello in an [issue](https://github.com/zuke-build/zuke/issues).

## Community

Questions, ideas, or just want to say hi?
[Open an issue](https://github.com/zuke-build/zuke/issues), write to
[contact@zuke.build](mailto:contact@zuke.build), or wear the build from the
[swag shop](https://totollyshop.myspreadshop.net/).

<p align="center">
  <a href="https://zuke.build"><img alt="Website" src="https://img.shields.io/badge/Website-zuke.build-000000?style=for-the-badge&logo=googlechrome&logoColor=white" /></a>
  <a href="mailto:contact@zuke.build"><img alt="Email" src="https://img.shields.io/badge/email-contact@zuke.build-8B89CC?style=for-the-badge&logo=protonmail&logoColor=white" /></a>
  <a href="https://twitter.com/totollygeek"><img alt="X" src="https://img.shields.io/badge/@totollygeek-000000?style=for-the-badge&logo=x&logoColor=white" /></a>
  <a href="https://bsky.app/profile/totollygeek.com"><img alt="Bluesky" src="https://img.shields.io/badge/totollygeek.com-0285FF?style=for-the-badge&logo=bluesky&logoColor=white" /></a>
  <a href="https://www.linkedin.com/in/totollygeek"><img alt="LinkedIn" src="https://custom-icon-badges.demolab.com/badge/totollygeek-0A66C2?style=for-the-badge&logo=linkedin-white&logoColor=white" /></a>
</p>

<p align="center">
  <a href="https://repostars.dev/?repos=zuke-build%2Fzuke&theme=dark"><img alt="Star history" src="https://repostars.dev/api/embed?repo=zuke-build%2Fzuke&theme=dark" /></a>
</p>

<p align="center">
  If Zuke is useful to you, <a href="https://github.com/zuke-build/zuke">star the repo</a>. It helps others find the project. ⭐
</p>

<p align="center">
  <sub>Inspired by <a href="https://nuke.build/">NUKE</a> for .NET. Made in Bulgaria 🇧🇬. MIT licensed.</sub>
</p>
