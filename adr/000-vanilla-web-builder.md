# AI-first web-developer IDE

> This desktop app lets developer select a preview window/page region with a pointing device and give instruction to the agent.

> This tool builds framework-free webapp: modern JavaScript with Signals, native CSS, semantic HTML, a11y.
> Or [Solid 2.0](#solid-20).

> Focus on UX. Vanilla: web "page" paradigm, no SPA, no build step, no server rendering.
> Solid: build step, SPA and server rendering allowed.
> Vanilla and Solid SSR: content lives in static HTML; JS enhances. No blank-until-JS.
> Clear [API boundaries](#api-boundaries): schema-first, tested against schema.

### Goal
Not quick-builder (v0, Lovable, Bolt: prompt → demo). Developer's knowledge builder:
developer stays author, agent helps understand, decide, record.
* Traceability: screen region → DOM → Signal → source.
* Explanations cite authoritative sources, dated.
* Decisions land in kb/ADR, not lost in chat.
* Generated code readable and owned: no hidden layers developer can't follow.
* Not frontend-only: hosting and integration options listed, compared, applied.

### Non-goals
* Refactoring/migrating existing codebases (e.g. React). Too complex now; maybe separate product.

### Milestones
* v0.1: preview window + chat interface.

### Stack
1. Signals (State Management) - not *yet* native browser standard. Follow Solid: same primitives, same semantics.
2. Custom Elements: thin, at edges only. See [Web Components](#web-components) below.
   Element ≠ Component. Composition = plain JS functions + Signals; Custom Element = island/leaf wrapper.
   * Light DOM default, styled by `@scope`. Shadow DOM only for isolated leaf widgets, 3rd-party embeds.
   * IDE Hook: agent generates single `.js` file (logic, template, styles), no compiler.
3. Native CSS, no preprocessors or utility frameworks:
   * *CSS Variables*: theming
   * *CSS Nesting*
   * *Container Queries*: component-based layout
   * `@scope`: style isolation without CSS-in-JS

### Solid 2.0
Same mental model: fine-grained Signals, components vanish at runtime.
* JSX is OK for templating.
* Solid tree inside; Custom Element only as export boundary, never between Solid components.

### Web Components
[Ryan Carniato's critique](opinion/ryansolid-on-web-components.md) (2024/2025) holds in 2026:
* Element ≠ Component: every interface forced through DOM.
* Attribute/property ambiguity: casing, reflection, string-only attributes.
* Shadow DOM: events don't compose, focus retargets to host, ARIA can't cross boundary → a11y trap.
* Upgrade-timing lifecycle breaks reactivity tracking and Context APIs.
* SSR/DSD duplicates logic: moot for Vanilla (no server rendering).

Fence (Ryan's safe subset), enforced by agent:
* Attributes: strings only, lowercase, presence = boolean. Rich data → property or shared Signal.
* Events: lowercase native-style names, `bubbles: true, composed: true`.
* State in Signals/modules, not in elements. No DOM-event Context API.
* Browser-only rendering; no `cloneNode` of custom elements.
* No custom elements in Solid's server-rendered output.

### API boundaries
Each boundary: schema = source of truth. Agent codes against it, tests check it. No build: schemas checked, not compiled.

| Boundary | Schema | Tests |
|---|---|---|
| Server HTTP | OpenAPI 3.1 (JSON Schema inside) | contract: responses vs schema (Schemathesis); mock server from schema (Prism) for UI work |
| Server push (SSE, WebSocket) | AsyncAPI | messages vs schema |
| JS modules | JSDoc types, `tsc --checkJs --noEmit` (check only) | unit: `node --test`; Vitest for Solid |
| Custom Elements | Custom Elements Manifest: attributes, properties, events | browser (Playwright), fence lint, axe |
| Page | — | Playwright e2e, visual snapshots, axe |

### IDE as The Glue
* When developer selects region, agent knows relationship between `Signal` state and DOM element it updates.
* Region → source: Vanilla DOM maps ~1:1 to source (no build); Solid: write small dev-mode compiler plugin emitting source locations.
* IDE manages ES Modules (`import/export`) in background.

### For whom is this tool
Engineers who want to understand and own their code: Vanilla or Solid, greenfield projects.
