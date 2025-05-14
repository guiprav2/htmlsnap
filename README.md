# 📸 htmlsnap

`htmlsnap` is a lightweight utility for generating a filtered and optionally ID-annotated (`data-htmlsnap` attribute) HTML snapshots of a DOM subtree. It can be configured to clean up whitespace, include only visible or LLM-relevant content, unobtrusively track DOM nodes via IDs and bi-directional maps, and selectively preserve element attributes.

---

## Usage

### Via CDN

You can use `htmlsnap` in the browser or Deno via e.g. [esm.sh](https://esm.sh):

```js
import htmlsnap from "https://esm.sh/htmlsnap";

const [html, map] = htmlsnap(document.body, {
  llm: true,
  idtrack: true,
});
```

### Via `npm`

Install first:

```bash
npm install htmlsnap
```

Then use:

```js
import htmlsnap from 'htmlsnap';

const [html, map] = htmlsnap(document.body, {
  llm: true,
  idtrack: true,
});
```

---

## Options

### `opt.collapseWhitespace` (default: `false`)
- If `true`, all text content will be trimmed and internal whitespace collapsed to a single space.
- Automatically enabled when `opt.llm` is `true`.

### `opt.idtrack` (default: `false`)
- If `true`, generates and attaches `data-htmlsnap` IDs to **all** elements and maps them in the output.
- IDs are consistent for the same DOM elements when `opt.map` is provided.

### `opt.llm` (default: `false`)
- Enables filtering and tagging for large language model (LLM) consumption.
- Implies `collapseWhitespace: true` (input token preservation).
- Hides elements like `<script>`, `<style>`, `<iframe>`, and any with CSS `display: none` or `visibility: hidden`.
- Excludes elements with the class `ai-invisible`; includes elements with `ai-only`, even if hidden.
- Annotates actionable elements (e.g., `<a>`, `<input>`, `<button>`) with `data-htmlsnap` and tracks them in a bi-directional map.
- Also affects default `opt.attrs`.

### `opt.removeInvisible` (default: same as `opt.llm`)
- If `true`, elements deemed invisible (based on computed style, dimensions, or tag type) will be removed from the snapshot.
- This includes `<script>`, `<style>`, `<iframe>`, elements with `display: none` or `visibility: hidden`, and the class `ai-invisible` (if `opt.llm` is also true).
- If `false`, invisible elements are retained.

### `opt.attrs` (default: `null` or LLM-relevant attributes if `opt.llm` is `true`)
- Specifies which element attributes to retain in the output.
- Can be an array of strings and/or regular expressions.
- Default if `opt.llm` is enabled:
  ```js
  [/^aria-/, 'alt', 'title', 'href', 'src', 'type', 'name', 'placeholder', 'value', 'id', 'role']
  ```

### `opt.map` (default: `undefined`)
- An optional pre-existing `BiMap` instance to use for ID tracking.
- If provided, it is updated and also used in the output (same instance).
- Required if you want to share or reuse mappings across multiple snapshots.

---

## Interactions Between Options

| Option            | Effect on Others                                         |
|-------------------|----------------------------------------------------------|
| `llm: true`       | Implies `collapseWhitespace: true`                       |
|                   | Sets default for `attrs` (overridable)                   |
|                   | Enables node visibility filtering and `ai-*` classes     |
|                   | Annotates form elements with `data-htmlsnap`             |
| `idtrack: true`   | Annotates **all elements** with `data-htmlsnap`          |
| `removeInvisible` | Defaults to `true` if `llm: true`, otherwise `false`     |
| `attrs`           | Filters retained attributes; `llm` sets a default        |
| `map`             | Used for ID tracking; shared between `idtrack` and `llm` |

---

## Output Format

- If `opt.llm` or `opt.idtrack` is enabled:
  - Returns the array: `[html, map]`
  - `map` is an instance of `BiMap` mapping IDs to original DOM elements.
- Otherwise:
  - Returns a string containing the filtered HTML snapshot.

---

## Testing

This library is implicitly tested by the extensive test coverage found in [KittyGPT](https://github.com/camilaprav/kittygpt), which exercises all of its functionality.

---

## License

### ISC (Internet Systems Consortium)

htmlsnap is free software: you can redistribute it and/or modify it under the terms of the [ISC License](COPYING).

## Exclusion of warranty

THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.

