# Markdown Semi-WYSIWYG (Unified View Pipeline)

Goal: keep Markdown source intact and visible, render a semi-WYSIWYG view (styles, flow, structure) without mutating the buffer, and let plugins drive presentation through a single, unified view pipeline. Prefer simplicity and determinism over heuristics.

## Principles
- Source is the only truth; control characters (fences, bullets, brackets, backslashes, spaces) remain editable and visible.
- Rendering is derived, never stored; the pipeline can always fall back to the identity view (source → view unchanged).
- Wrapping and line flow belong to the view pipeline, not the renderer; built-in “wrap while drawing” is disabled for Markdown compose mode.
- Every step is pluggable: a plugin may rewrite the representation at each pass, including removing or inserting break markers.
- Multi-view is first class: the same buffer can be shown in multiple splits/tabs with different transforms/layout hints.

## Pipeline (per viewport)
1) **Source ingest** (core): take a viewport slice anchored at `top_byte`. Build a base view stream of tokens: `Text`, `Newline`, `Space` (for tabs, if expanded), plus resolved overlays/virtual text anchors. Each token carries the originating source byte offset (or `None` for injected content).
2) **Transform stages** (plugins and/or core):
   - Input: the current view stream + mapping (view index → source offset) + layout hints.
   - Output: a rewritten stream + updated mapping. Plugins may completely replace the stream (e.g., drop/merge newline tokens to model soft breaks) or add styling/link/color spans.
   - Multiple passes are allowed; core treats “no plugin” as the identity transform.
3) **Layout** (core):
   - Apply wrapping as a transform: insert break tokens based on `compose_width`/`max_width`.
   - Center the composed column when the terminal is wider; tint side margins. `max_width` renders as margins when there is extra space.
   - Produce display lines and maintain mapping for hit-testing/cursors.
4) **Render** (core): draw styled lines. Uses the mapping for cursor placement, selection, and overlays. No conditional branches for “with/without transform”; identity is just another transform.

## Transform API (new/updated)
- **submitViewTransform(buffer_id, split_id, payload)**: send transformed tokens + mapping and optional layout hints (compose width, column guides). Per-split state allows different views of the same buffer.
- **Tokens**: `Text`, `Newline`, `Space`; style/overlay/virtual-text markers attach out of band (existing overlay/virtual text APIs). Mapping is per character to the originating source byte, or `None` for generated view-only characters.
- **Rewrites allowed**: newline→space (soft break), space→newline, removal/duplication of tokens, style/color/link hints, table column guides. Plugins can always affect newline rendering without user prompts.
- **Identity fallback**: if no plugin responds, core synthesizes the identity stream from the source slice and its overlays.

## Markdown-specific behavior
- **Soft breaks**: inside paragraphs/lists/quotes, plugin rewrites buffer newlines to spaces (or otherwise) in the view stream; mapping keeps cursors/selections consistent. Hard breaks (two spaces+newline, backslash+newline, `<br>`) remain as explicit newlines unless the plugin chooses otherwise.
- **Flow & width**: text flow uses `compose_width`/`max_width` and centers the column; built-in renderer wrapping is off for compose mode because wrapping is injected by the pipeline.
- **Navigation**: in compose mode, up/down operate on visual lines (post-transform); source mode keeps logical-line navigation.
- **Structure rendering**: headers, lists/bullets/checkboxes, block quotes, tables (with column guides), inline code, fenced code blocks, links/autolinks, emphasis/strong/strike, colors. Code blocks keep source fences visible; future work: underline styles.
- **Control characters**: fences, bullets, brackets, backslashes, and spaces are the Markdown source; they remain editable and visible while affecting rendering.
- **Line numbers**: source mode keeps them; compose mode may hide them (plugin-configurable) to reinforce the document view.

## Core vs Plugin Responsibilities
- **Core**
  - Build base view stream from source + overlays/virtual text (viewport-scoped).
  - Maintain per-split view state (mode, compose width, layout hints, submitted transforms).
  - Apply wrapping/centering as a transform; render with margins tinted; mapping-aware cursor/selection/hit-testing.
  - Expose ops: toggle compose mode, set compose width/max width, submit view transform, set layout hints. Disable renderer line-wrap logic when compose mode is active.
- **Plugin (`markdown_compose`)**
  - Parse Markdown incrementally for the visible slice; rewrite newlines to soft breaks where appropriate; leave hard breaks intact.
  - Emit style/link/color spans, table column guides, list indentation fixes, and code-block styling cues.
  - Decide whether to hide line numbers in compose mode; manage compose width preference per buffer/split.
  - Provide commands: toggle compose, set compose width/max width, refresh transform.

## Multi-view Support
- Each split/tab stores its own view transform + layout hints. The same buffer can be rendered differently in each split; submitting a transform includes `split_id`, so plugins can tailor the view per pane without altering buffer state.

## Implementation Plan
1) **Pipeline plumbing**: keep the unified view path in the renderer (always render from a view stream + mapping; identity is synthesized when no transform is available). Remove renderer-side wrapping logic for compose mode; wrapping is injected as a transform.
2) **State & ops**: per-split compose mode, compose/max width, optional line-number hiding; ops for toggle/set width/submitViewTransform with `split_id`.
3) **Renderer**: consume the transformed stream, center with margins, mapping-aware cursor/selection/overlays; support per-split transforms.
4) **Plugin**: incrementally parse Markdown; apply soft-break rules, styling, tables/lists/headers/links/code blocks; emit transforms and layout hints; bind visual-line navigation in compose mode.
5) **Validation**: ensure newlines can be rewritten to whitespace via transform; verify multi-split rendering shows different views of the same buffer; keep fallbacks working (identity view) when the plugin is absent.***
