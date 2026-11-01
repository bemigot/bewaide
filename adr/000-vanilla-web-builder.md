# AI-first web-developer IDE

[conversation with Gemini on 2026-02-15](https://share.gemini.google/iVUbQjFLKL4V). 2026-11-01 summary.

> This desktop app lets developer or designer select a preview window/page region with a pointing device and give instruction to the agent.

> This tool builds "no-framework", or rather "React/Angular/whatever-free" webapp: modern JavaScript with Signals, compact and elegant CSS solutions, semantic HTML

### Is "no-framework" the future of AI-first IDE
AI is significantly better at writing standard-compliant, low-abstraction code than it is at navigating complex,
opinionated framework boilerplate.
* Less Hallucination: In a React project, the AI has to worry about hooks, dependency arrays, and state synchronization.
  "Good Parts" project (Vanilla + Signals) code is more imperative and predictable.
* Faster Feedback Loops: preview window can update in milliseconds.
* Long-term Stability: a framework may change API in two years. DOM API is "forever."

### Modern "Good Parts" Stack
1. Signals (State Management) - they aren't *yet* a native browser standard (though there is a proposal).
   Providing a tiny native-feeling Signal library (like `@preact/signals-core` or a custom 1KB implementation)
   gives developers that "magic" reactivity without the framework baggage.
2. Native Web Components (The UI Layer): lean into *Shadow DOM* and *Custom Elements*.
   * Advantage: They are encapsulated by the browser.
   * IDE Hook: AI agent can generate a single `.js` file that contains the logic, template, and styles
     for a component that works inany* browser without a compiler.
3. Modern CSS. We don't need Tailwind or Sass anymore. IDE should double down on:
   * *CSS Variables*: for dynamic theming.
   * *CSS Nesting*: now native in all major browsers.
   * *Container Queries*: for truly modular, component-based layout.
   * `@scope`: To prevent styles from leaking without needing "CSS-in-JS."

### IDE as The Glue
* The "Agentic" Compiler:** Instead of a traditional build step, your AI agent acts as the compiler. It ensures that when a developer selects a region, it understands the relationship between the `Signal` state and the DOM element it’s updating.
* Automatic Imports: Since you’re going vanilla, the IDE should manage ES Modules (`import/export`) invisibly in the background, so the user just writes code.

### For whom is this tool
Senior/Lead Engineers and performance nerds. Those who hate "magic" they can't control but love efficiency.
