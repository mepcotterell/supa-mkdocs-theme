# Supa MkDocs Theme: implementation and distribution plan

Status: approved product decisions; researched implementation plan, revised after adversarial review. Implementation has not started.
Research date: 2026-11-08. Project owner: Michael E. Cotterell.

## 1. Objective and scope

Create **Supa MkDocs Theme**, distributed as **`supa-mkdocs-theme`**, as a faithful MkDocs port of Supa Maven Skin. By 1.0, provide every transferable feature from the pinned Supa Maven Skin **0.2.1-SNAPSHOT** baseline identified below, using MkDocs conventions for configuration, navigation, search, URLs, and content. Make the installed package straightforward to use, verifiably portable across required Python versions, and releasable through GitHub Actions.

This planning task delivers only this plan and its initial local Git commit. The files, workflows, tests, packages, hosted documentation, and releases described below are future implementation work. Repository creation on GitHub, pushes, account configuration, and publication are not performed by this planning task.

### Fixed identity

| Field | Value |
| --- | --- |
| Display name | Supa MkDocs Theme |
| Distribution / intended repository name | `supa-mkdocs-theme` |
| Python import package | `supa_mkdocs_theme` |
| MkDocs theme entry-point name | `supa` |
| Author | Michael E. Cotterell <mepcotterell@gmail.com> |
| Maintainer | Michael E. Cotterell <mepcotterell@gmail.com> |
| Project-authored source license | MIT |
| Python requirement | `>=3.11,<4.0` |
| Initial MkDocs requirement | `>=1.6.1,<2` |

The current local directory is named `supa-mkocs-theme`. Keep it in place during this task; use the correctly spelled distribution and repository names in project metadata.

### Confirmed decisions

1. Full transferable feature parity is the 1.0 gate; 0.x releases may publish explicitly documented subsets.
2. Every maintained **stable** Python release is mandatory. Development and prerelease Python are monitored separately and are nonblocking. Retain all released Python 3.x minors admitted by the 3.11 floor, including after upstream end of life, until the maintainer explicitly changes that promise.
3. Theme-controlled browser assets are local, including optional diagrams and math. A locally served site must work without external network access. Direct `file://` browsing is not promised.
4. Installed-distribution, browser, accessibility, and visual evidence are required. The theme-controlled interface targets WCAG 2.2 AA. The maintainer gives visual acceptance before 1.0.
5. Use current uv, Ruff, ty, and pytest, with recorded versions and tested updates. External GitHub Actions use immutable commits corresponding to their latest stable releases, with freshness checks on PRs, daily, and before publication.
6. Distribute wheels and source archives on PyPI; rehearse on TestPyPI; publish GitHub Releases and a GitHub Pages documentation/demo site. Use trusted publishing.
7. Maintain an attributed, adapted copy of the relevant Supa frontend sources, with a separate contributor frontend toolchain and a documented upstream-update procedure. Use the latest snapshot selected for import, recording its exact commit; refresh snapshots through reviewed provenance and parity updates.
8. Prefer native Python-Markdown/PyMdown authoring and migration recipes. Functional parity does not require byte-for-byte Maven Markdown compatibility. Add custom Python integration only for a demonstrated gap.
9. Support MkDocs 1.6.1 through compatible stable 1.x releases initially. MkDocs 2 compatibility is a separate future milestone.
10. Provide English UI defaults, label overrides, configured page language, and Unicode support. Bundled translations and RTL layout are outside 1.0.
11. A tested Bootstrap 6 prerelease is acceptable for this theme's 1.0. Begin with the baseline's exact version; newer Bootstrap 6 releases require regression and visual review.

## 2. Evidence and feasibility gates

These observations establish a starting point, not proof that the port already works:

- The selected baseline is the latest Supa Maven Skin **0.2.1-SNAPSHOT**, verified on **2026-11-08** at upstream `main` commit **`6db6b4ff8a215be27d07d4f13ca219023e3f8c10`**. Comparison with v0.2.0 shows one commit changing only version values in `pom.xml`, `package.json`, and `package-lock.json`; there is no feature or dependency change in that delta. Pin source imports and references to this immutable snapshot commit. [Snapshot commit](https://github.com/mepcotterell/supa-maven-skin/commit/6db6b4ff8a215be27d07d4f13ca219023e3f8c10), [comparison with v0.2.0](https://github.com/mepcotterell/supa-maven-skin/compare/v0.2.0...6db6b4ff8a215be27d07d4f13ca219023e3f8c10)
- The baseline uses **Bootstrap 6.0.0-alpha.1**, SCSS, TypeScript, Vite, and Sass. Its dependency ranges also name Bootstrap Icons, Floating UI, Highlight.js, and vanilla-calendar-pro. Resolve exact frontend versions from the upstream lockfile; declared ranges are not resolved versions. [Upstream manifest](https://github.com/mepcotterell/supa-maven-skin/blob/6db6b4ff8a215be27d07d4f13ca219023e3f8c10/package.json)
- The official Python table currently lists maintained stable **3.11, 3.12, 3.13, 3.14, and 3.15**, plus development 3.16. Classify by release status rather than treating every table row as a mandatory stable interpreter. [Python version status](https://devguide.python.org/versions/#supported-versions)
- **MkDocs 1.6.1** is the stable baseline. Its permissive Python metadata does not prove compatibility with every newer Python. Building an installed-theme fixture on 3.15 is an early release-blocking feasibility test. MkDocs 2 development documentation already shows a changed template interface. [MkDocs release](https://www.mkdocs.org/about/release-notes/#version-161-2024-08-30), [1.6.1 metadata](https://github.com/mkdocs/mkdocs/blob/1.6.1/pyproject.toml), [MkDocs 2 documentation](https://www.encode.io/mkdocs/)

Before substantial porting, prove three narrow paths: a discoverable packaged theme builds on every required Python; the native search assets work with a Supa interface and project-subpath URLs; and frontend rebuilding produces a complete local asset closure, including optional loaders and fonts. Record failure as a blocker rather than narrowing an approved promise silently.

### Tool freshness baseline

Resolve versions again when implementation starts and before release. Research observed uv **0.13.7**, Ruff **0.17.4**, ty **0.0.99**, and pytest **9.2.0**. These are dated observations, not permanent pins. Capture upstream release URLs, resolution timestamps, Python requirements, and exact versions in a machine-readable toolchain record. The local uv executable inspected during planning was older; planning did not upgrade it. [uv releases](https://github.com/astral-sh/uv/releases), [Ruff releases](https://github.com/astral-sh/ruff/releases), [ty releases](https://github.com/astral-sh/ty/releases), [pytest releases](https://github.com/pytest-dev/pytest/releases)

## 3. Repository, packaging, and development model

### Intended layout

```text
AGENTS.md                    CLAUDE.md
PLAN.md                      README.md
LICENSE                      THIRD_PARTY_NOTICES.md
CONTRIBUTING.md               CHANGELOG.md
GLOSSARY.md                   pyproject.toml
uv.lock                      .python-version
package.json                 package-lock.json
mkdocs.yml                   .gitignore
.agents/
  rules/                     skills/
  skills-provenance.json
.claude/skills -> ../.agents/skills
.github/workflows/
  ci.yml                     maintenance.yml
  release.yml                docs.yml
config/
  python-support.json        toolchain.json
  actions-lock.json          upstream-sources.json
src/supa_mkdocs_theme/
  __init__.py                 mkdocs_theme.yml
  base.html                  main.html
  404.html                   search.html
  partials/                  search/main.js
  assets/css/                assets/js/
  assets/fonts/              assets/icons/
  assets/licenses/
frontend/
  scss/                      ts/
scripts/
tests/
  unit/                      integration/
  fixtures/                  browser/
docs/
  getting-started/            reference/
  authoring/                 migration/
  development/               showcase/
licenses/
```

Names below are proposed implementation interfaces. Create only the directories justified by their actual responsibilities; keep theme-served files separate from contributor-only sources and governance.

### Python package

- Use `uv init --package --name supa-mkdocs-theme` with a `src/` layout, preserving the existing plan and Git history, then set exact identity metadata, README, project URLs, supported classifiers, and `requires-python = ">=3.11,<4.0"` in **`pyproject.toml`**. Use a single project version in this file and obtain it through package metadata when needed.
- Use `uv_build` for the pure Python/data package, with an explicit compatible backend version range selected from the current release. The normalized module name is `supa_mkdocs_theme`; it may be stated explicitly for clarity. Prove backend file inclusion in the first packaging spike. [uv build backend](https://docs.astral.sh/uv/concepts/build-backend/)
- Register `[project.entry-points."mkdocs.themes"]` with `supa = "supa_mkdocs_theme"`. The installed module contains `mkdocs_theme.yml`, templates, and runtime assets. A custom-directory demo is not sufficient proof of installed discovery. [Theme packaging](https://www.mkdocs.org/dev-guide/themes/#packaging-themes)
- Depend on `mkdocs>=1.6.1,<2`. Keep optional authoring dependencies, such as PyMdown Extensions, in a `content` extra and document the necessary `markdown_extensions` configuration. Basic pages must build without that extra. Declare direct dependencies for any library directly imported by new Python code.
- Include compiled browser assets in both wheel and sdist. Include the frontend source, lockfile, build configuration, tests, and documentation needed to reproduce and inspect the release in the sdist. Building a wheel from the sdist uses the included compiled assets and requires no Node, npm, network asset fetch, Git checkout, or submodule.
- Keep frontend sources and governance outside the installed theme directory. Inspect both archives for required templates, chunks, fonts, notices, metadata, and unwanted repository files. Use content hashes and a manifest to catch absent or stale generated assets.
- License project-authored source under MIT. Preserve upstream and third-party copyright notices, including the separate licenses of vendored assets and fonts. Derive truthful distribution licensing metadata from the shipped inventory; the project's MIT license does not relicense third-party code. Include required notices in both distributions and in generated-site asset notices where redistribution requires them. [Packaging license guidance](https://packaging.python.org/en/latest/guides/licensing-examples-and-user-scenarios/)

### Development tooling

Use uv for Python dependency management, execution, building, and publication. Commit `uv.lock`; use locked synchronization and execution in ordinary CI. Maintain separate development groups for lint/type checks, tests, docs, and release tooling as useful. Run Ruff for Python linting/formatting, ty for type checking, and pytest for behavior and packaging tests. Type-check against the 3.11 language baseline; verify newer interpreters through runtime tests. Address errors rather than adding blanket ignores.

Retain a separately locked Node/Vite/Sass/TypeScript pipeline for frontend contributors. Use `npm ci`, explicit Node/package-manager versions, TypeScript checking, appropriate JS/TS linting, targeted unit tests, and Playwright browser tests. Ruff and ty do not validate TypeScript, SCSS, Jinja, YAML, or browser behavior. Record all test/browser versions and update them through reviewed changes.

Commit compiled runtime assets alongside their editable sources. CI rebuilds assets and checks the generated manifest/content for drift. Exclude nondeterministic timestamps and machine paths from generation. Consumer commands require only the Python package ecosystem:

```sh
uv add supa-mkdocs-theme
uv run mkdocs build --strict
```

With the installed theme:

```yaml
theme:
  name: supa
```

Keep reproducibility and freshness separate: locked jobs reproduce a commit; scheduled and pre-release jobs detect outdated tools/dependencies and require a tested update. If future development tools need a newer Python than 3.11, run those tools on a suitable current interpreter while preserving package/build/consumer tests on every promised interpreter. Do not raise runtime metadata merely to satisfy a development tool.

## 4. Theme architecture and feature parity

### Ownership and extension points

Use Jinja templates with `base.html`, a small extending `main.html`, and focused partials. Define documented blocks for metadata, styles, scripts, navigation, content, TOC, and footer. Support `theme.custom_dir`, `extra_css`, `extra_javascript` including modern script attributes, CSS tokens, custom logos, and accessible labels. Preserve MkDocs page content and heading IDs; map its `nav`, `page`, `page.toc`, canonical/edit URLs, and previous/next pages directly. Use MkDocs' URL and script-tag filters. Escape plain configuration text; allow authored HTML only at documented content boundaries. [MkDocs theme customization](https://www.mkdocs.org/user-guide/customizing-your-theme/)

Translate Supa configuration into documented snake_case MkDocs theme keys. Produce an option reference recording type, default, accepted values, behavior when disabled, and the original Supa setting. Make documented keys, template blocks, CSS tokens, and content markup the public customization contract. Add a plugin only after a fixture demonstrates a requirement that a normal theme and established Markdown extensions cannot satisfy; record the rationale and compatibility tests before adding it.

Copy/adapt SCSS and TypeScript at the pinned upstream revision, preserving provenance. Recheck the latest upstream snapshot before the initial import; if `main` has advanced, review the delta and update the selected commit, source links, provenance, and parity ledger together. Ordinary builds consume the recorded sources and locks rather than fetching a floating branch or resolving a mutable Maven SNAPSHOT artifact. Replace Velocity, Doxia workarounds, Maven metadata, and Maven-specific discovery with MkDocs integration. Keep transferable component behavior isolated from page-generator adapters so later upstream updates remain reviewable. Subsequent snapshot synchronization compares against the recorded source commit, classifies relevant changes, records local deviations, rebuilds assets, and re-runs affected parity tests.

### Parity ledger

Create `docs/development/parity.md` with one row per documented upstream option/component, its source reference, MkDocs mapping, fixture, tests, accessibility status, visual status, and release milestone. The following groups define its minimum coverage; inspect the complete upstream guides and sources to expand each group rather than treating this summary as exhaustive. [Upstream configuration](https://github.com/mepcotterell/supa-maven-skin/blob/6db6b4ff8a215be27d07d4f13ca219023e3f8c10/src/site/markdown/configuration.md), [authoring](https://github.com/mepcotterell/supa-maven-skin/blob/6db6b4ff8a215be27d07d4f13ca219023e3f8c10/src/site/markdown/authoring-guide.md), [architecture](https://github.com/mepcotterell/supa-maven-skin/blob/6db6b4ff8a215be27d07d4f13ca219023e3f8c10/src/site/markdown/architecture.md)

| Surface | Required mapping and acceptance evidence |
| --- | --- |
| Layout | Three-column layout; left/right sidebar thresholds initially 1024/1280px; 56rem article cap; responsive drawers, long content, fixed/sticky regions, narrow-screen overflow. |
| Appearance | `furo`, `bootstrap`, `modus`, `modus-tinted`, `modus-deuteranopia`, `modus-tritanopia`; light/dark/auto; persistence, system changes, early application without visible flash, storage-disabled behavior, light/dark logos. |
| Navigation | Arbitrarily nested sections, active ancestors, breadcrumbs, next/previous cards, external links, source/edit links, optional sticky header, TOC activation, heading permalinks, back-to-top, reduced motion, and fragment navigation after delayed diagram/math layout without overriding subsequent reader movement. |
| Code | Highlighting, copy feedback/failure, filenames, line numbers, diff styles, long lines, accurate copied text. Recognize pre-highlighted output and prevent duplicate highlighting or controls. |
| Rich content | Native admonitions and alerts; icons; keyboard-accessible synchronized tabs; accordions and steppers; tables, footnotes, images, links, nested content and disclosure states. |
| Components | Menus, dialogs, drawers, tooltips, datepicker, combobox, chips, OTP, range, strength, overflow navigation, carousel, tabs, and a component showcase/playground. Test each supported declarative component rather than inheriting upstream test claims. |
| Search | Supa dialog and shortcuts; useful page/section results; localized labels; index-backed behavior; no old Maven crawler; documented response to disabled/missing search and index failures. |
| Diagrams/math | Opt-in Mermaid and KaTeX, local dependencies/fonts/chunks, lazy loading on relevant pages, dark-mode and hidden-tab behavior, readable failure/fallback states, documented authoring. |
| Language | English defaults, full exposed-label overrides, page `lang`, Unicode, and explicit remaining upstream widget limitations; no claim of RTL support or bundled translations. |
| Print | Article-focused light-polarity output, readable tables/code, external-link destinations, and a documented rule for selected tabs/open disclosures matching the baseline. |
| MkDocs essentials | Root and project-subpath hosting, both directory-URL modes, 404/search pages without a page context, navigation omissions, custom templates, canonical metadata, standard extra assets. |

Maven-only exclusions must be listed explicitly: Velocity/macros, `site.xml`, Maven project/report generation, synthetic report navigation, Doxia anchor/table repair, JAR packaging, and Maven build plugins. The corresponding browser experience remains required where an equivalent makes sense in MkDocs. Record intentional accessibility improvements as reviewed deviations from the baseline rather than regressions in fidelity.

### Search integration

Start with MkDocs' normal search plugin assets (`theme.search_index_only: false` and `prebuild_index: false` in the `plugins` entry for `search`) and a theme-provided `search/main.js` Supa UI adapter. Reuse MkDocs' packaged worker, Lunr, and configured language assets; validate template/file precedence, worker URL construction, and single initialization in the early spike. This is a proposed integration that must pass the spike, not a claim of an implemented adapter. [MkDocs search source](https://github.com/mkdocs/mkdocs/blob/1.6.1/mkdocs/contrib/search/__init__.py)

If the override cannot provide the required interface reliably, switch to `theme.search_index_only: true` only with an explicitly packaged local engine, worker, and required language modules. That setting also disables MkDocs' usual search asset copying. Either implementation consumes `search/search_index.json` and honors the configured index contents, separators, language, and indexing mode. Do not recover omitted text by crawling pages or scraping the current article. Keep prebuilding that requires Node out of the default consumer setup.

Test local engine/worker loading, a non-English language, minimum-query-length behavior, invalid query handling and safe result insertion, keyboard focus/escape and restoration, result navigation, loading/error/empty states, missing search, nested deployment paths, and cache invalidation across site versions. Use a classic worker for MkDocs' `importScripts` implementation; replace its jQuery-oriented main script rather than introducing jQuery. Prevent stale asynchronous responses from replacing newer results, accounting for the worker protocol's lack of request identifiers. Any page-first ranking must be derived from permitted index entries. Readable documentation and ordinary navigation remain available when JavaScript fails; search and enhanced widgets can report or visibly fall back from their unavailable state. [Native worker](https://github.com/mkdocs/mkdocs/blob/1.6.1/mkdocs/contrib/search/templates/search/worker.js)

### Asset closure and content integration

Bundle or retain through declared package dependencies every asset requested by theme-controlled code: fonts and their CSS URLs, icons, lazy component chunks, workers, Mermaid chunks, KaTeX scripts/styles/fonts, and search language files. Load optional features only when enabled and needed. A disabled feature must not fetch or execute its library. Include local license inventories for direct and transitive embedded code.

Use supported Python-Markdown/PyMdown configurations for admonitions, fenced/nested code, linked tabs, and math preservation. Document a minimal installation and a full-feature installation separately. Maintain one syntax-highlighting owner for each code block. Test Markdown-looking text in code, malformed optional content, nested tabs/disclosures, custom IDs, and non-ASCII labels. [Admonitions](https://python-markdown.github.io/extensions/admonition/), [tabs](https://facelessuser.github.io/pymdown-extensions/extensions/tabbed/), [code fences](https://facelessuser.github.io/pymdown-extensions/extensions/superfences/), [math](https://facelessuser.github.io/pymdown-extensions/extensions/arithmatex/)

Audit CSP requirements with all local features enabled. Prefer a small local synchronous appearance initializer before the stylesheets; use a documented hash only if an inline initializer proves necessary. Do not claim a strict CSP works until browser-tested. Use conservative Mermaid settings and keep KaTeX trust disabled; avoid injecting search/configuration strings as executable HTML. Promise external-network independence for theme-controlled assets, not for external media a site author deliberately embeds. Publish the tested policy and any feature-specific restrictions. [KaTeX browser assets](https://katex.org/docs/browser.html), [Mermaid configuration](https://mermaid.js.org/config/usage.html)

## 5. Verification strategy

Use small representative fixtures with deliberate edge cases, then reuse their built output for browser checks. Test public behavior and artifact completeness instead of mirroring implementation. Distinguish five evidence levels: source inspection, automated unit/integration results, archive/installed-consumer results, browser evidence, and human visual acceptance.

| Layer | Required checks |
| --- | --- |
| Python quality | Ruff lint and format checks; ty on Python sources/helpers/tests; targeted pytest behavior tests; no Python syntax above the promised minimum. |
| Frontend quality | TypeScript check, JS/TS lint, meaningful component/unit tests, asset regeneration without drift, source/license inventory consistency. |
| Package/build | Force PEP 517 and select each required stable interpreter explicitly for sdist/wheel builds; rebuild from the canonical sdist without Node; record interpreter/backend identity and assert entry points, metadata, assets, licenses and filenames; compare expected file/content manifests. |
| Consumer integration | In temporary directories outside the checkout, install the canonical wheel and the independently rebuilt sdist wheel in separate fresh environments, with no editable project/path override; prove imported theme location is installed; build strict fixtures with required MkDocs endpoints and optional extras both present and absent. |
| MkDocs semantics | Test both URL modes, nested pages, project prefix, absent page context, custom_dir/block overrides, extra assets, search enabled/disabled, labels, canonical/edit links, representative Markdown extension combinations. |
| Browser | Playwright Chromium/Firefox/WebKit; responsive boundaries, focus management, keyboard operation, dialogs/drawers, theme persistence, search, copy, components, optional features and failure fallbacks; unexpected console errors fail. |
| Offline/CSP | Serve built output locally, block nonlocal requests, exercise every lazy feature and search language, fail missing assets/requests; exercise documented CSP separately. |
| Accessibility | Automated axe checks in default, open, focused and changed interaction states; keyboard/zoom/reflow/reduced-motion checks and manual screen-reader checks; track each theme-caused WCAG 2.2 AA issue to resolution. |
| Visual/print | Reviewed baselines for six schemes, both polarities, responsive widths and representative components; fixed browser/font/environment versions; print media plus human print inspection. |
| Release delivery | Download and inspect published distributions; clean consumer smoke build; verify Pages project-subpath/404/assets/search; reconcile artifact checksums with the tested release bundle. |

Run the full Python version matrix on Linux. Add packaging/installed-consumer smoke tests on Windows and macOS using the minimum and newest stable Python. Run browser suites against a representative installed-wheel build rather than multiplying every browser, OS, Python and palette combination. Broaden only where a distinct compatibility risk warrants it.

At minimum, visual coverage includes each scheme in light/dark at a desktop reference width, core layouts at mobile/tablet/sidebar boundaries, and representative component/print pages. Automate behavior in all three engines; keep rendering baselines per engine where differences are material. Review baseline changes intentionally. Automated screenshots and axe results do not substitute for the maintainer's acceptance or establish complete WCAG conformance. Track color-contrast targets separately from whole-interface conformance. Use the [WCAG 2.2 Recommendation](https://www.w3.org/TR/WCAG22/) as the criterion reference for the audit.

## 6. Python support and release freshness rules

Create `.agents/rules/python-support.md` as the authoritative prose rule and `config/python-support.json` as the checked data source for workflows. Store source URL, check time, maintained stable versions, development versions, and retained older versions. The blocking matrix covers every released minor from 3.11 through the newest stable Python 3.x; EOL status does not drop an intermediate minor such as 3.12. Derive classifiers/matrices from one checked record and validate `requires-python` contains every mandatory version. Use the latest available maintenance patch for each required minor release.

Fetch and validate the official support status daily and in the release preflight. Trigger a failing maintenance check/update proposal when the required matrix changes. A newly stable minor becomes mandatory immediately for the next release. A development branch stays nonblocking. An EOL transition does not remove the promised 3.11 floor. Parse status and version explicitly; unexpected structure or an unavailable trusted source produces an unknown state and blocks publication, not an empty successful matrix. Any equivalent fallback source must be official and recorded.

Every required interpreter must actually build the project's distributions and build/install a wheel from the canonical sdist. Use `uv build --force-pep517 --python <version> --no-sources` for the per-interpreter validation and its wheel-from-sdist equivalent, recording the selected interpreter and backend. uv's normal `uv_build` fast path can bypass a Python build environment, so a matrix environment alone would not prove this requirement. Publish one chosen `py3-none-any` wheel and one sdist after verification; do not upload multiple same-named universal wheels from matrix jobs. A broad metadata range or universal tag alone is not compatibility evidence. Recheck external support facts after any long release approval delay. [uv build CLI](https://docs.astral.sh/uv/reference/cli/#uv-build)

Development Python failures must remain visible in CI and the maintenance report. They may not turn a failed stable job into an allowed failure. If a future maintained Python falls outside the approved `<4.0` boundary, block release and seek an explicit support-policy change; never claim both contradictory promises have been met.

## 7. GitHub Actions and distribution

### Action/version policy

Create `.agents/rules/github-actions.md` and `config/actions-lock.json`. Enumerate every external action and reusable workflow, including nested composite references, its upstream repository, latest stable release, fully resolved commit, and check timestamp. Scope includes GitHub-owned actions. Preserve a human-readable release-version comment beside each pinned `uses` reference. Reject floating branches/major tags, prereleases under the stable-Action policy, stale releases, and unknown release state.

Check freshness on PRs, daily, and immediately before production publishing. Routine jobs reproduce pinned commits; the freshness gate requires tested updates when upstream changes. Use update PRs rather than automatically rewriting or merging dependencies. Handle rate limits, failed lookups, moved tags, and new-major releases explicitly. Offline local development may use its lock snapshot; a release needs current successful checks. Check nested refs even when upstream's current composite Action has not yet updated them.

Observed direct-action candidates on the research date: [checkout v7.0.1](https://github.com/actions/checkout/releases/tag/v7.0.1), [setup-uv v10.3.3](https://github.com/astral-sh/setup-uv/releases/tag/v10.3.3), [setup-node v7.1.0](https://github.com/actions/setup-node/releases/tag/v7.1.0), [upload-artifact v7.0.2](https://github.com/actions/upload-artifact/releases/tag/v7.0.2), [download-artifact v8.0.2](https://github.com/actions/download-artifact/releases/tag/v8.0.2), [configure-pages v6.0.0](https://github.com/actions/configure-pages/releases/tag/v6.0.0), and [deploy-pages v5.0.1](https://github.com/actions/deploy-pages/releases/tag/v5.0.1). Resolve their exact commits and current release state during implementation rather than copying mutable version refs into workflows. Peel annotated tags; a release's `target_commitish` is not its resolved release commit.

The inspected [upload-pages-artifact v5.0.0](https://github.com/actions/upload-pages-artifact/blob/v5.0.0/action.yml) embedded `upload-artifact@v7.0.0`, behind the latest inspected `v7.0.2`. Plan a small local packaging step using the current Pages artifact format, direct latest `upload-artifact`, and latest `deploy-pages`. Validate archive naming, format, contents, size and absence of symbolic/hard links against the current service requirements. Reevaluate the workaround when upstream updates. [Pages workflow requirements](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages)

The inspected [Astral attest-action v0.0.7](https://github.com/astral-sh/attest-action/blob/v0.0.7/action.yml) and [PyPA publisher v1.14.3](https://github.com/pypa/gh-action-pypi-publish/blob/v1.14.3/action.yml) also declared outdated nested setup Actions. Conditional references remain part of the inventory even when their branches usually do not execute. Use uv's OIDC publishing path initially. PEP 740 attestations are a separately evaluated enhancement, not a reason to introduce an unrequested custom signing subsystem or relax the Action policy.

### Workflow responsibilities

| Workflow | Trigger and responsibilities |
| --- | --- |
| `ci.yml` | PRs and main pushes: policy checks, locked quality checks, frontend regeneration, Python build/install matrix, fixture/site builds, browser/accessibility/visual checks; upload development-docs preview artifacts and failure evidence with bounded retention. |
| `maintenance.yml` | Daily and manual: official Python status, latest tools/Actions/dependencies, upstream Supa changes, development-Python checks; report actionable drift through CI summaries and update proposals. |
| `release.yml` | Maintainer-created `v*` tag, plus a retry entry point restricted to that same tag/commit: verify source/version, current policy gates, required build/test evidence, exact distribution bundle, TestPyPI rehearsal, protected production publication, release artifacts and post-publication smoke checks. |
| `docs.yml` | Reusable workflow called after successful release, plus a restricted manual retry for the published version. Build from that exact installed distribution and tagged docs sources/lock, validate subpath URLs, and deploy the complete stable site through the protected Pages environment. |

Default to `contents: read` and disable persisted checkout credentials. Keep PR testing unprivileged, including fork PRs. Give `id-token: write` only to jobs that need OIDC; give Pages and Release write permissions only to their respective deployment/publication jobs. Avoid executing untrusted PR code in privileged workflows. Set timeouts, avoid cancelling production publication midway, and serialize releases. Cache keys include interpreter/toolchain/lockfile information; publishing does not trust arbitrary PR caches or artifacts. Transfer the canonical bundle between jobs by immutable run/artifact identity and verify recorded hashes before use.

### Release procedure

1. Resolve the intended public GitHub repository and PyPI/TestPyPI names. Confirm ownership/name availability and register trusted publishers with the exact repository, workflow filename, and protected environment. These are implementation-time account setup steps; a name lookup is not a reservation. Document initial setup and recovery.
2. Update the package version and changelog; refresh supported-Python/tool/Action facts and upstream review; run all mandatory checks on the candidate source commit. For 1.0, every required parity row and the maintainer's visual acceptance must be complete. The maintainer creates the matching `v<version>` tag on that protected-main commit. The release workflow verifies tag, package version, commit ancestry and expected repository before proceeding.
3. Rebuild and verify compiled assets from the locks. Build the canonical distributions with `uv build --force-pep517 --python <release-interpreter> --no-sources`; validate metadata, manifests, licenses and README rendering. Exercise all required build/consumer combinations using explicit interpreters, including the canonical wheel and canonical-sdist rebuilds. Keep the selected publish artifacts, checksums, source commit, tool versions and check results together as one immutable release bundle. [uv package publishing](https://docs.astral.sh/uv/guides/package/)
4. Upload the selected candidate distributions to TestPyPI using trusted publishing. Download the exact files by version and verify hashes; install them with runtime dependencies sourced deliberately from PyPI, avoiding accidental selection of unrelated TestPyPI packages. Perform a clean strict site build and representative browser smoke test.
5. After required release approval, rerun time-sensitive freshness checks. Publish those same tested bytes to PyPI with `uv publish --trusted-publishing always`, using the registered environment and explicit target endpoint. If freshness changed, stop: update and retest a new candidate, using a new version if the prior candidate has already consumed an immutable index version. Do not edit dependencies in the publication job. uv can upload supported attestation files but does not generate them; make no attestation claim until a compliant optional generation step is validated. [Trusted publishers](https://docs.pypi.org/trusted-publishers/)
6. Create the GitHub Release against the already verified version tag after PyPI publication. Attach the verified distributions/checksums, validation manifest and changelog. Deploy stable documentation using the released wheel and recorded docs dependencies; verify the published version and project-prefix links.
7. Download the PyPI artifacts and compare hashes. Smoke-test installation and the live documentation. If publication partially succeeds, reconcile which exact files exist before retrying; do not rebuild different bytes under the same version. For a defective release, document yanking and publish a corrected version; restore a known-good documentation deployment if needed.

Exercise tag validation, artifact transfer, protected approval, safe retry and partial-failure recovery on TestPyPI before enabling production. Public Pages documentation at the project root always represents the current published release; identify 0.x limitations clearly. Main/PR documentation builds remain downloadable CI preview artifacts and do not deploy over that site. Call `docs.yml` explicitly from the release workflow; do not depend on a second workflow firing from a release created with `GITHUB_TOKEN`. Serialize Pages deployments and verify the intended current release again before deployment so an older delayed run cannot replace a newer release. Use semver-style change discipline with PEP 440-compatible Python versions and treat public configuration/template/token changes as compatibility changes after 1.0.

## 8. Agent instructions, rules, and vendored skills

Write a concise `AGENTS.md` inspired by T3 Code's structure: product principles, domain vocabulary, repository map, task-specific pointers, contributor commands, and verification/completion rules. Adapt the useful ideas to this package. The upstream prohibition on committing plans and monorepo-specific restrictions do not apply. Preserve this plan and normal appropriate checks. [T3 inspiration](https://github.com/pingdotgg/t3code/blob/main/AGENTS.md)

Make `CLAUDE.md` contain only `@AGENTS.md` followed by a newline. Make `.claude/skills` a real relative symlink whose stored target is exactly `../.agents/skills`. Verify the link resolves to `.agents/skills` and is recorded as a symlink in Git. Document the Windows checkout setup needed when symlinks are unavailable; this contributor feature must not become a package runtime dependency.

Place scoped rules in `.agents/rules/`, with explicit pointers from `AGENTS.md` because arbitrary rule directories are not automatically loaded by every harness:

| Rule file | Trigger / authoritative policy |
| --- | --- |
| `python-support.md` | Dependency, metadata, CI, and release changes: official support status, fixed floor, matrix/build obligations, unknown-state handling. |
| `github-actions.md` | Workflow or Action updates and release: latest stable direct/transitive Actions, full commit pins, privilege boundaries, freshness gates. |
| `tooling-and-verification.md` | Implementation and validation: uv/Ruff/ty/pytest, frontend toolchain, relevant tests, proof boundaries, installed artifacts. |
| `theme-parity.md` | Templates/styles/scripts/authoring changes: baseline mapping, public extension contracts, accessibility, offline assets, visual review. |
| `release.md` | Release preparation/publication: immutable tested artifacts, account prerequisites, versioning, approvals and recovery. |
| `vendored-sources.md` | Upstream imports/updates: full supporting files, licenses, hashes, patches, provenance and invocation policies. |

Vendor the complete requested skill directories under `.agents/skills/`, including their supporting files and upstream notices. Preserve invocation policies and the `grill-me` router's relationship to `grilling`.

| Local skill | Upstream location | Supporting files observed |
| --- | --- | --- |
| `create-readme` | [github/awesome-copilot: skills/create-readme](https://github.com/github/awesome-copilot/tree/main/skills/create-readme) | `SKILL.md` |
| `grill-me` | [mattpocock/skills: skills/productivity/grill-me](https://github.com/mattpocock/skills/tree/main/skills/productivity/grill-me) | `SKILL.md`, `agents/openai.yaml` |
| `handoff` | [mattpocock/skills: skills/productivity/handoff](https://github.com/mattpocock/skills/tree/main/skills/productivity/handoff) | `SKILL.md`, `agents/openai.yaml` |
| `wait-what` | [mattpocock/skills: skills/productivity/wait-what](https://github.com/mattpocock/skills/tree/main/skills/productivity/wait-what) | `SKILL.md`, `agents/openai.yaml` |
| `grilling` | [mattpocock/skills: skills/productivity/grilling](https://github.com/mattpocock/skills/tree/main/skills/productivity/grilling) | `SKILL.md`, `agents/openai.yaml` |
| `writing-for-agents` | [mattpocock/skills: skills/productivity/writing-for-agents](https://github.com/mattpocock/skills/tree/main/skills/productivity/writing-for-agents) | `SKILL.md`, `SKILL-MECHANICS.md`, `agents/openai.yaml` |

Research snapshots: Awesome Copilot `797337f33f73e35d08d82f840e21c4b8c4932d88`; Matt Pocock skills `4ae2317ddb22b046e26455e4f9c9b3aaaf62212d`; T3 inspiration `8e6a7323050ea8d2891f745f7e624e84f9fd8d9a`. Recheck at import time and pin the chosen revisions. `skills-provenance.json` records repository, upstream path/ref, imported files/hashes, license location and local patches. Keep original instructions intact where possible; put cross-harness loading guidance in `AGENTS.md` rather than assuming every harness exposes a tool named `Skill`.

Upstream currently makes `grill-me`, `handoff`, and `wait-what` explicit-only in both skill frontmatter and OpenAI metadata. `grilling`, `writing-for-agents`, and `create-readme` allow implicit discovery. Retain those distinctions. Add a small `GLOSSARY.md` for project terms referenced by `wait-what`. Preserve the handoff skill's temporary-file behavior. Validate relative links, complete directory imports, metadata and license preservation; imported instructions are reviewed project content, not authority to override user instructions.

## 9. Milestones and exit criteria

| Milestone | Deliverables | Exit criteria |
| --- | --- | --- |
| M0: foundation and feasibility | Metadata/package skeleton; current tool pins/locks; AGENTS/rules/skills/CLAUDE/symlink; baseline provenance; first package, search and asset spikes; parity ledger. | A real installed `supa` theme builds strictly on every mandatory Python, including 3.15; wheel-from-sdist works without Node; search/local-asset approach demonstrated; unresolved upstream failures explicitly block further release claims. |
| M1: visual shell and MkDocs integration | Jinja structure, navigation/TOC, responsive drawers, six schemes, appearance settings, canonical/edit links, 404/search basics, extension blocks. | Installed fixtures pass URL/context/customization tests; keyboard navigation and responsive behavior pass three engines; representative visual baseline reviewed. |
| M2: content and component parity | Code tools, admonitions/icons, tabs, disclosures/steppers, all transferable widgets, native authoring adapters, optional math/diagrams, localization and print. | Every feature has a documented authoring path, fixture, behavior tests and local assets; no unrecorded omissions; offline/lazy-feature tests pass. |
| M3: release-quality hardening | Full archive/consumer matrix, minimum/latest dependency endpoints, accessibility audit, CSP profile, visual/print review, actionable documentation. | Mandatory CI passes; theme-controlled AA issues resolved; content/asset/license inventories complete; source and sdist builds agree on runtime contents; installation guides reproduce. |
| M4: preview distribution | Trusted publishing setup, TestPyPI rehearsal, immutable release bundle, 0.x PyPI/GitHub release, release-only Pages and separate CI preview artifacts, recovery runbook. | Exact artifact download/install/hash checks pass; production publishing flow demonstrated; external names/ownership verified; known preview limitations published. |
| M5: 1.0 acceptance | Closed parity ledger, complete authoring/customization/migration/reference docs, maintenance and update runbooks, maintainer review. | Full transferable parity, every stable-Python build gate, browser/offline/accessibility gates, fresh dependencies/Actions, and recorded maintainer visual acceptance; then publish through the rehearsed release process. |

Implement one coherent vertical slice at a time and commit source, generated assets, tests and documentation together. Independent template/frontend/packaging work may proceed in parallel after M0 fixes their interfaces. Publish a 0.x preview when its documented subset is useful and all universal packaging/support/release requirements pass; 0.x is not an exception to the Python or Action policies.

## 10. Risks and decisions to resolve through evidence

| Risk | Required response |
| --- | --- |
| MkDocs 1.x or its dependencies fail on a newly mandatory Python | Reproduce early; fix/adapt within supported APIs or pursue upstream correction; block release until the promise is met. A change to the approved support contract needs a new decision. |
| Bootstrap 6 alpha behavior/API changes | Pin the baseline, isolate integration, test upgrades, document the prerelease dependency; never update merely to silence freshness checks intended for Actions/tools. |
| Full feature parity becomes an unbounded claim | Enumerate every baseline feature/option in the ledger with explicit exclusions and evidence; 1.0 requires all transferable rows, not just a visually similar homepage. |
| Local optional dependencies inflate archives or still reach a CDN | Track size and request budgets from the first measured build; inspect dynamic imports/CSS URLs and exercise features under blocked external access; optimize without silently removing parity. |
| Generated assets drift from sources | Locked regeneration and content manifest checks; archive roundtrip verification; compare release bundle checksums. |
| Latest upstream Action embeds an outdated action | Use a minimal reviewed local wrapper/direct current Actions or block release; never silently exempt transitive dependencies. |
| A template override or author extension breaks behavior | Document supported public hooks and extension combinations; test representative overrides; distinguish theme defects from arbitrary author modifications. |
| Source licenses or skill support files disappear during copying | Complete source manifests, preserved notices, validation of imported links and hashes, archive/license inventory review. |
| Release succeeds in only one destination | Reconcile immutable artifacts, retry only matching bytes, document yanking/fix-forward and docs restoration. |

Evidence-driven implementation choices may refine filenames and internal modules while preserving the approved contracts. A material conflict, scope reduction, unsupported platform promise, or new external prerequisite must be surfaced rather than hidden in a technical fallback.

## 11. Planning review record

Independent agents researched and proposed the architecture and release design. A challenger exchanged objections with both proposers. Separate reviewers then examined the complete draft for architecture, delivery, and requirements coverage. Their material findings and the research objections were incorporated as follows:

| Objection | Disposition in this plan |
| --- | --- |
| A JSON index alone does not implement search, and index-only mode suppresses language assets. | Use a proved native-worker/UI adapter; any index-only fallback owns the full engine/worker/language closure. |
| Broad Python metadata and repeated uv fast-path builds do not prove each interpreter works. | Force PEP 517 with explicit interpreters, rebuild the canonical sdist, and test the canonical wheel in isolated consumers on every required version. |
| A fixed floor can silently lose intermediate EOL Python versions. | Retain every admitted stable minor until the support contract is explicitly changed. |
| Main and release Pages deployments can overwrite each other. | Publish release-only documentation at the public root; keep development builds as CI preview artifacts; serialize deployment and reject stale releases. |
| Latest composite Actions can embed outdated dependencies. | Inspect transitive references; use direct current Actions/local Pages packaging and uv OIDC publication. |
| Adding custom signing machinery would expand an optional concern. | Keep attestations optional; claim only the provenance actually generated and verified. |
| MIT project licensing does not relicense bundled assets. | Retain all notices and derive distribution metadata from the emitted asset inventory. |
| Local diagrams/math require fonts, chunks, and CSS resources too. | Inventory the complete closure and test every lazy feature with external requests blocked. |
| Search settings belong to different configuration scopes; the module-name rationale was inaccurate. | Name the theme/search-plugin settings explicitly and correct the normalization explanation. |

The final independent recheck found no material issues after these revisions and confirmed coverage of the original requirements and all eleven accepted decisions. No material review finding remains unresolved. This is planning evidence only: no implementation tests, browser runs, package builds, or accessibility acceptance have occurred during planning.

Subsequent baseline amendment, **2026-11-08**: the maintainer requested the latest Supa Maven Skin snapshot. The baseline and source references now use **0.2.1-SNAPSHOT**, commit `6db6b4ff8a215be27d07d4f13ca219023e3f8c10`. Live upstream comparison confirmed that its delta from the originally reviewed v0.2.0 contains only version changes. A separate reference audit identified the affected baseline and update-policy passages; the feature inventory therefore remains applicable. Snapshot imports remain pinned and reviewed.

The immediate completion check is: `PLAN.md` contains the confirmed requirements and source-backed execution plan; Git is initialized locally on `main`; only `PLAN.md` is staged and committed; the final report identifies the commit and verification limits.
