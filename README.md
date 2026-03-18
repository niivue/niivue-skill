# NiiVue Skill for Claude Code

> [!WARNING]
> This skill targets the NiiVue 1.0.0 API, which has not been published yet. The skill will not work correctly until NiiVue 1.0.0 is released.

A Claude Code plugin that provides API guidance, patterns, and examples for building web applications with the [NiiVue](https://github.com/niivue) neuroimaging visualization library.

## What It Does

When installed, Claude automatically detects NiiVue usage in your project and provides:

- Correct API patterns (flat property setters, async initialization, event handling)
- Property reference with naming conventions and domain prefixes
- Constants and enum values (SLICE_TYPE, DRAG_MODE, COLORMAP_TYPE)
- Working code examples for common use cases
- Guidance on common mistakes and pitfalls

The skill triggers automatically when:
- Your code imports `niivuegpu` or `niivue`
- You ask about displaying brain images, medical volumes, or neuroimaging meshes in a browser

## Installation

### From GitHub (marketplace)

```
/plugin marketplace add niivue/niivue-skill
/plugin install niivue@niivue-skill
```

### Local Testing

```bash
claude --plugin-dir ~/path/to/niivue-skill
```

## Updating

Plugin updates are **version-driven**. When we publish a new version:

1. **Auto-update at startup** -- If auto-update is enabled for the marketplace, Claude Code fetches the latest version automatically when you start a new session. Third-party marketplaces have auto-update disabled by default; enable it via `/plugin` > Marketplaces > select marketplace > toggle auto-update.

2. **Manual update** -- Run:
   ```
   /plugin update niivue@niivue-skill
   ```

3. **Reload in current session** -- After an update, reload plugins without restarting:
   ```
   /reload-plugins
   ```

Plugins are cached locally at `~/.claude/plugins/cache/`. Updates only trigger when the version number changes in the manifest.

## Skills Provided

### `niivue`

The main skill, accessible as `/niivue:niivue`. Covers:

- Quick start pattern (3 lines)
- Core API patterns (async init, flat setters, events, per-item options)
- Quick reference table for common tasks
- Common mistakes to avoid

Supporting reference files:
- **api-reference.md** -- Complete property tables, methods, events, and constants
- **examples.md** -- 10 curated working code examples

## Links

- [NiiVueGPU](https://github.com/rordenlab/niivuegpu) -- The visualization library
- [NiiVue](https://github.com/niivue) -- NiiVue organization

## License

MIT
