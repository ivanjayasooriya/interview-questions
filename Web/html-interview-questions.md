# HTML Interview Questions & Answers — Complete Guide

A comprehensive reference covering HTML fundamentals, semantic markup, forms, media, accessibility, HTML5 APIs, SEO, performance, and security.

---

## Table of Contents
1. [HTML Basics & Document Structure](#1-html-basics--document-structure)
2. [Elements, Tags & Attributes](#2-elements-tags--attributes)
3. [Semantic HTML](#3-semantic-html)
4. [Text, Lists & Links](#4-text-lists--links)
5. [Forms & Input Elements](#5-forms--input-elements)
6. [Form Validation](#6-form-validation)
7. [Tables](#7-tables)
8. [Images & Responsive Images](#8-images--responsive-images)
9. [Audio, Video & Embedded Content](#9-audio-video--embedded-content)
10. [HTML5 Canvas & SVG](#10-html5-canvas--svg)
11. [Metadata, SEO & the `<head>`](#11-metadata-seo--the-head)
12. [Accessibility (a11y) & ARIA](#12-accessibility-a11y--aria)
13. [HTML5 APIs](#13-html5-apis)
14. [Web Storage & Offline](#14-web-storage--offline)
15. [Web Components](#15-web-components)
16. [Responsive Design & Viewport](#16-responsive-design--viewport)
17. [Performance](#17-performance)
18. [Security](#18-security)
19. [HTML Versions & Browser Compatibility](#19-html-versions--browser-compatibility)
20. [Best Practices & Common Pitfalls](#20-best-practices--common-pitfalls)

---

## 1. HTML Basics & Document Structure

**Q1. What is HTML, and what is its role in web development?**
HTML (HyperText Markup Language) is the standard markup language for structuring content on the web — defining the meaning and organization of content (headings, paragraphs, lists, forms) via elements. It works alongside CSS (presentation/styling) and JavaScript (behavior/interactivity) to form the three core web technologies.

**Q2. What is the basic structure of an HTML document?**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Page Title</title>
</head>
<body>
    <h1>Content goes here</h1>
</body>
</html>
```

**Q3. What is `<!DOCTYPE html>`, and why is it needed?**
A declaration at the very top of an HTML document telling the browser which version of HTML the page uses, ensuring the browser renders it in **standards mode** rather than "quirks mode" (an older, less-standardized rendering behavior for legacy pages). `<!DOCTYPE html>` is the simplified HTML5 doctype — no version number needed, unlike the verbose DTDs required by HTML4/XHTML.

**Q4. What is the difference between HTML elements, tags, and attributes?**
A **tag** is the markup syntax itself (`<p>`, `</p>`). An **element** is the complete unit — the opening tag, content, and closing tag together (`<p>Hello</p>`). An **attribute** provides additional information about an element, specified within the opening tag (`<img src="pic.jpg" alt="description">` — `src` and `alt` are attributes).

**Q5. What is the difference between the `<head>` and `<body>` sections?**
The `<head>` contains metadata about the document not directly rendered as visible page content (title, character encoding, linked stylesheets/scripts, meta tags for SEO). The `<body>` contains the actual visible content of the page rendered in the browser window.

**Q6. What is the difference between block-level and inline elements?**
**Block-level elements** (`<div>`, `<p>`, `<h1>`–`<h6>`, `<section>`) start on a new line and take up the full available width by default. **Inline elements** (`<span>`, `<a>`, `<strong>`, `<em>`) don't force a line break and only take up as much width as their content requires, flowing within surrounding text.

**Q7. What is the difference between `<div>` and `<span>`?**
`<div>` is a generic **block-level** container used to group larger sections of content for styling/scripting purposes, with no inherent semantic meaning. `<span>` is a generic **inline** container used to wrap small portions of text/content within a line, also with no inherent semantic meaning.

**Q8. What are void (self-closing) elements in HTML?**
Elements that cannot have any content/children and don't require a closing tag, since they represent a single point in the document rather than wrapping content: `<img>`, `<br>`, `<hr>`, `<input>`, `<meta>`, `<link>`, `<source>`, `<area>`, `<col>`, `<wbr>`.

**Q9. What is the correct way to add comments in HTML?**
```html
<!-- This is a comment and won't be rendered by the browser -->
```
Comments are useful for documentation but are still visible in page source, so they shouldn't contain sensitive information.

**Q10. What is the `lang` attribute on `<html>` used for?**
Declares the primary language of the document's content (`<html lang="en">`), which assists screen readers in using correct pronunciation rules, helps search engines with language-specific indexing, and enables browser features like translation prompts to work correctly.

---

## 2. Elements, Tags & Attributes

**Q11. What are global attributes in HTML, and can you name several?**
Attributes that can be applied to virtually any HTML element: `id`, `class`, `style`, `title`, `data-*` (custom data attributes), `hidden`, `tabindex`, `contenteditable`, `draggable`, `lang`, `dir` (text direction), `role` (ARIA), `spellcheck`.

**Q12. What is the difference between `id` and `class` attributes?**
`id` must be **unique** within the document (identifies a single specific element, used for anchor links, JavaScript targeting via `getElementById`, and label associations). `class` can be applied to **multiple** elements and a single element can have multiple classes (space-separated), used primarily for grouping elements for shared CSS styling or JavaScript selection.

**Q13. What are `data-*` attributes used for?**
Custom attributes for embedding application-specific data directly in HTML elements without resorting to non-standard attributes or hacks: `<div data-user-id="42" data-role="admin">`. Accessible in JavaScript via the `dataset` property (`element.dataset.userId` — automatically converts kebab-case to camelCase) and can be targeted in CSS via attribute selectors (`[data-role="admin"]`).

**Q14. What is the difference between `<b>`/`<strong>` and `<i>`/`<em>`?**
`<b>` and `<i>` are purely presentational (bold/italic visual styling with no semantic meaning). `<strong>` and `<em>` carry **semantic** meaning — `<strong>` indicates strong importance/urgency, `<em>` indicates emphasized stress (both are also rendered bold/italic by default in most browsers, but screen readers convey the semantic emphasis differently than pure styling). Modern best practice favors the semantic versions when the meaning applies, and `<b>`/`<i>` only for cases genuinely lacking extra semantic importance (e.g., a keyword in a summary that's stylistically bolded without indicating special importance).

**Q15. What is the `title` attribute, and how does it differ from the `<title>` element?**
The `title` **attribute** can be applied to most elements to provide supplementary/tooltip information shown on hover (e.g., `<abbr title="HyperText Markup Language">HTML</abbr>`). The `<title>` **element** (inside `<head>`) sets the document's title, shown in the browser tab and used as the default title in search engine results/bookmarks — entirely different purposes despite the similar name.

**Q16. What is the `hidden` attribute?**
A boolean global attribute that hides an element from rendering (equivalent to `display: none` in CSS, but semantically indicates the content is not currently relevant) — the element is removed from the visual flow but its content may still be programmatically accessible unless also excluded via other means.

**Q17. What does the `contenteditable` attribute do?**
Makes an element's content directly editable by the user within the browser (e.g., `<div contenteditable="true">`), commonly used as the foundation for building custom rich-text editors without needing a `<textarea>`.

---

## 3. Semantic HTML

**Q18. What is semantic HTML, and why does it matter?**
Using HTML elements according to their intended meaning rather than purely for visual layout — e.g., using `<nav>` for navigation instead of a generic `<div class="nav">`. It matters because semantic markup improves **accessibility** (screen readers use semantic structure to help users navigate), **SEO** (search engines better understand page structure/content importance), **maintainability** (code is more self-documenting), and enables browser/tooling features that rely on document structure.

**Q19. What are the key HTML5 semantic structural elements, and what does each represent?**
- `<header>`: introductory content or navigational aids for a page or section.
- `<nav>`: a section containing primary navigation links.
- `<main>`: the dominant, unique content of the document (only one per page).
- `<article>`: a self-contained, independently distributable piece of content (a blog post, news article).
- `<section>`: a thematic grouping of content, typically with its own heading.
- `<aside>`: content tangentially related to the surrounding content (sidebars, pull quotes).
- `<footer>`: footer content for its nearest sectioning ancestor (copyright, related links).
- `<figure>`/`<figcaption>`: self-contained media (image, diagram, code snippet) with an optional caption.

**Q20. What is the difference between `<section>` and `<div>`?**
`<section>` carries semantic meaning — it represents a distinct, thematically-related grouping of content, typically expected to have a heading. `<div>` is a purely generic, non-semantic container with no inherent meaning, used when no more specific semantic element applies (e.g., a wrapper purely for CSS layout/styling purposes with no independent thematic content).

**Q21. What is the difference between `<article>` and `<section>`?**
`<article>` represents content that is **self-contained and independently meaningful/distributable** — it should make sense on its own if extracted and syndicated elsewhere (e.g., a blog post, a forum comment, a widget). `<section>` is a more general **thematic grouping** within a document that doesn't necessarily stand alone independently — an `<article>` might contain multiple `<section>`s, and a `<section>` might contain multiple `<article>`s (e.g., a "Latest News" section containing several article summaries).

**Q22. Can a page have multiple `<nav>`, `<header>`, or `<footer>` elements?**
Yes — a page can have multiple `<nav>` elements (e.g., primary navigation and a footer navigation), and `<header>`/`<footer>` can appear multiple times, scoped to their nearest sectioning ancestor (e.g., an `<article>` can have its own `<header>` with the article's title/byline, distinct from the page's overall `<header>`). However, `<main>` should appear **only once** per page, representing the single dominant content.

**Q23. What is the correct heading hierarchy best practice (`<h1>`–`<h6>`)?**
Headings should form a logical, nested outline of the document's content, ideally with only one `<h1>` per page (representing the main topic), and subsequent heading levels used in order without skipping levels purely for visual sizing purposes (styling should be handled via CSS, not by choosing a heading level for its default visual size) — this matters significantly for screen reader users who navigate by heading structure.

**Q24. What is the `<time>` element used for?**
Represents a specific date/time in a machine-readable format via the `datetime` attribute, while allowing human-readable display text as the element's content: `<time datetime="2026-08-17">August 17, 2026</time>` — useful for search engines, calendar/scheduling tools, and browser extensions that parse dates.

**Q25. What is the `<mark>` element used for?**
Represents text that is highlighted or marked for reference purposes relevant to the current context (e.g., search result highlighting) — semantically distinct from purely visual highlighting done via CSS `background-color`.

---

## 4. Text, Lists & Links

**Q26. What are the different types of HTML lists?**
- `<ul>` (unordered list): a bulleted list of items with no inherent sequence.
- `<ol>` (ordered list): a numbered list where sequence/order matters.
- `<dl>` (description list): pairs of terms (`<dt>`) and their descriptions (`<dd>`) — e.g., a glossary.

**Q27. What attributes can customize an `<ol>`'s numbering?**
`start` (sets the starting number), `reversed` (counts down instead of up), `type` (numbering style: `1`, `A`, `a`, `I`, `i` for decimal/uppercase-letter/lowercase-letter/uppercase-roman/lowercase-roman).

**Q28. What is the difference between an absolute and a relative URL in an `<a href="">`?**
An **absolute URL** specifies the full path including the protocol and domain (`https://example.com/page`), working regardless of the current page's location. A **relative URL** specifies a path relative to the current document's location (`/page`, `../page`, `page.html`) — shorter and more portable within the same site, but dependent on the current page's context.

**Q29. What does the `target="_blank"` attribute do on a link, and what security consideration comes with it?**
Opens the linked document in a new browsing tab/window. Security consideration: pages opened via `target="_blank"` historically had access to the originating page via `window.opener`, enabling a potential **"tabnabbing"** attack (the new page maliciously redirecting the original tab). Best practice: always pair `target="_blank"` with `rel="noopener noreferrer"` to prevent the new page from accessing `window.opener` (modern browsers now apply `noopener` behavior by default for `target="_blank"` links, but explicitly specifying it remains a recommended, defensive best practice).

**Q30. What is the difference between `rel="nofollow"`, `rel="noopener"`, and `rel="noreferrer"` on a link?**
- `nofollow`: tells search engines not to pass ranking credit/"link juice" through this link (commonly used for user-generated content, sponsored/paid links, or untrusted external links).
- `noopener`: prevents the newly opened page from accessing the originating page via `window.opener` (security).
- `noreferrer`: prevents the browser from sending the `Referer` header to the destination page (privacy — the destination won't know which page linked to it) and also implies `noopener` behavior.

**Q31. How do you create an anchor link that jumps to a specific section within the same page?**
Assign an `id` to the target element (`<h2 id="section2">`), then link to it with a fragment identifier: `<a href="#section2">Jump to Section 2</a>`.

**Q32. What is the `download` attribute on an anchor tag?**
Instructs the browser to download the linked resource rather than navigating to/displaying it, optionally specifying a suggested filename: `<a href="file.pdf" download="report.pdf">Download</a>`.

**Q33. What are HTML entities, and why are they needed?**
Special character codes used to represent characters that have special meaning in HTML markup itself, or characters not easily typed directly: `&lt;` (`<`), `&gt;` (`>`), `&amp;` (`&`), `&nbsp;` (non-breaking space), `&copy;` (©). Necessary to display literal `<`/`>`/`&` characters without the browser interpreting them as markup syntax.

---

## 5. Forms & Input Elements

**Q34. What is the purpose of the `<form>` element, and what are its key attributes?**
Groups interactive controls for submitting user input to a server. Key attributes: `action` (the URL the form data is submitted to), `method` (`GET` or `POST`), `enctype` (encoding type for the submitted data, important for file uploads: `multipart/form-data`), `autocomplete`, `novalidate` (disables built-in browser validation).

**Q35. What is the difference between the `GET` and `POST` methods for form submission?**
`GET` appends form data as URL query parameters (visible in the URL, bookmarkable, has length limitations, should be used for non-destructive/idempotent requests like search) — data is not suitable for sensitive information since it's visible in browser history/server logs. `POST` sends form data in the request body (not visible in the URL, no practical length limit, used for state-changing operations and sensitive data like passwords, though still requires HTTPS for actual transport security).

**Q36. What is the difference between `<input>`, `<textarea>`, and `<select>`?**
`<input>` is a versatile, single-value control whose behavior varies based on its `type` attribute (text, checkbox, radio, etc.) — it's a void element. `<textarea>` provides a multi-line free-text input area. `<select>` (combined with `<option>` children) provides a dropdown/list selection control.

**Q37. What are the common HTML5 input types?**
`text`, `password`, `email`, `number`, `tel`, `url`, `search`, `date`, `time`, `datetime-local`, `month`, `week`, `color`, `range`, `checkbox`, `radio`, `file`, `hidden`, `submit`, `reset`, `button`. Using the semantically correct type (e.g., `type="email"`) triggers appropriate mobile keyboards and enables built-in browser validation without custom JavaScript.

**Q38. What is the difference between `<label>` used with a `for` attribute vs. wrapping the input directly?**
```html
<label for="username">Username</label>
<input id="username" type="text">
<!-- or -->
<label>Username <input type="text"></label>
```
Both approaches programmatically associate the label with the input (important for accessibility — screen readers announce the label when the input is focused, and clicking the label focuses/activates the input). The `for`/`id` approach is more flexible (label doesn't need to be adjacent to the input in the markup), while wrapping avoids needing a unique `id`.

**Q39. What is the difference between the `name` and `id` attributes on a form input?**
`id` uniquely identifies the element within the document (used for `label for=`, CSS, JavaScript targeting). `name` is the key used when the form data is submitted/serialized (`name=value` pairs sent to the server) — an input needs a `name` attribute to actually be included in form submission; `id` is not used for submission purposes at all.

**Q40. What is the purpose of the `placeholder` attribute, and why shouldn't it replace a `<label>`?**
`placeholder` shows hint text inside an empty input field, disappearing once the user types. It should **never** replace a proper `<label>`, because: placeholder text disappears once typing begins (losing context for what the field is), it often has poor color contrast (accessibility issue), and screen readers don't reliably treat it as a substitute for an actual accessible label.

**Q41. What are `<fieldset>` and `<legend>` used for?**
`<fieldset>` groups a set of related form controls together (e.g., all fields for a shipping address), and `<legend>` provides a caption/heading for that group — improving both visual organization and accessibility (screen readers announce the legend when focus enters the fieldset, providing context for the grouped controls, e.g., important for grouped radio button sets).

**Q42. What is the difference between `disabled` and `readonly` on a form input?**
`disabled` completely prevents user interaction with the field **and** excludes its value from form submission entirely. `readonly` prevents the user from editing the value, but the field remains focusable/selectable (e.g., text can still be copied) **and** its value is still included in form submission.

**Q43. What does the `multiple` attribute do on an `<input type="file">` or `<select>`?**
On a file input, it allows selecting multiple files at once. On a `<select>`, it allows selecting multiple options simultaneously (typically rendered as a scrollable list box instead of a dropdown, with the user able to Ctrl/Cmd-click to select multiple options).

**Q44. What is the `<datalist>` element?**
Provides a list of predefined autocomplete suggestions for an associated `<input>` (linked via the input's `list` attribute matching the datalist's `id`), giving users suggested options while still allowing free-text entry — a lighter-weight alternative to a full `<select>` when suggestions (not strict enumeration) are desired.

---

## 6. Form Validation

**Q45. What is HTML5 built-in (native) form validation, and how is it triggered?**
Browser-native validation applied automatically based on input attributes, without requiring custom JavaScript — triggered when the form is submitted (or via `checkValidity()` programmatically). If validation fails, the browser prevents submission and displays a default validation message near the invalid field.

**Q46. What are common HTML5 validation attributes?**
`required` (field must be filled), `minlength`/`maxlength` (text length bounds), `min`/`max` (numeric/date value bounds), `pattern` (a regular expression the value must match), `step` (increment granularity for numeric/date inputs). Combined with type-specific validation (e.g., `type="email"` automatically validates email format).

**Q47. How do you disable native HTML5 form validation?**
Add the `novalidate` attribute to the `<form>` element — useful when implementing fully custom JavaScript-based validation and handling of error messaging instead of relying on the browser's default behavior/styling.

**Q48. What CSS pseudo-classes are available for styling form validation states?**
`:valid`/`:invalid` (whether the current value satisfies validation constraints), `:required`/`:optional`, `:in-range`/`:out-of-range` (for numeric/date inputs with min/max), `:focus`, `:disabled`, `:checked` (checkboxes/radios) — enabling validation-state-aware styling without JavaScript.

**Q49. What is the Constraint Validation API, and what methods does it provide for custom validation logic in JavaScript?**
A JavaScript API for interacting with native HTML5 validation programmatically: `element.checkValidity()` (returns boolean, whether the field is currently valid), `element.reportValidity()` (checks validity and shows the browser's validation UI if invalid), `element.setCustomValidity("message")` (sets a custom error message, making the field invalid until cleared with an empty string) — used to implement custom validation rules while still leveraging the browser's native validation UI/behavior.

**Q50. Why is client-side (HTML5/JavaScript) validation not sufficient on its own, and what must always accompany it?**
Client-side validation can always be bypassed (disabled JavaScript, direct API requests, browser dev tools manipulation) — it exists purely for **user experience** (immediate feedback without a round trip to the server). **Server-side validation is mandatory** for actual security/data integrity, since the server can never trust that data arriving from the client has actually been validated.

---

## 7. Tables

**Q51. What are the core elements used to build an HTML table?**
`<table>` (the container), `<tr>` (table row), `<td>` (table data/cell), `<th>` (table header cell), `<thead>`/`<tbody>`/`<tfoot>` (grouping header, body, and footer rows respectively), `<caption>` (a title/description for the table).

**Q52. What is the difference between `<th>` and `<td>`?**
`<th>` defines a **header** cell (bold and centered by default; also carries semantic meaning associated with the data cells it labels — important for screen readers describing table data). `<td>` defines a standard **data** cell.

**Q53. What is the `scope` attribute on `<th>`, and why does it matter for accessibility?**
Specifies whether a header cell applies to a `col`umn, `row`, `colgroup`, or `rowgroup` — helping screen readers correctly announce which header(s) apply to a given data cell as the user navigates through a complex table, since visual/spatial header-to-cell association isn't available to non-visual users.

**Q54. What do `colspan` and `rowspan` do?**
`colspan="n"` makes a cell span across `n` columns. `rowspan="n"` makes a cell span across `n` rows — used to create merged cells for complex table layouts (e.g., a cell heading that applies to multiple sub-columns).

**Q55. Should HTML tables be used for page layout purposes?**
No — this was common practice in early web development but is now strongly discouraged. Tables should be reserved for genuinely **tabular data** (rows/columns of related data points). Page layout should be handled with CSS (Flexbox, Grid) — using tables for layout creates accessibility problems (screen readers announce layout tables as if they were data tables, causing confusion) and results in inflexible, hard-to-maintain markup.

---

## 8. Images & Responsive Images

**Q56. What is the `alt` attribute on `<img>`, and why is it critically important?**
Provides alternative text describing the image's content/purpose, displayed if the image fails to load and read aloud by screen readers for visually impaired users — essential for accessibility and also used by search engines for image indexing/SEO. For purely decorative images with no informational content, `alt=""` (empty, not omitted) should be used so screen readers skip announcing it.

**Q57. What is the difference between `width`/`height` HTML attributes on `<img>` vs. setting dimensions via CSS?**
Setting `width`/`height` as HTML **attributes** (even if the actual displayed size is later overridden by CSS) allows the browser to reserve the correct aspect-ratio space for the image **before** it finishes loading — preventing **Cumulative Layout Shift (CLS)**, a page-stability/performance metric. Relying purely on CSS without also specifying the intrinsic dimensions in HTML can cause content to visibly jump as images load.

**Q58. What is the `<picture>` element, and how does it enable responsive/art-directed images?**
Allows specifying multiple image sources with different conditions (`media` queries for art direction, or `type` for format fallback), with the browser selecting the most appropriate one:
```html
<picture>
    <source media="(min-width: 800px)" srcset="large.jpg">
    <source media="(min-width: 400px)" srcset="medium.jpg">
    <img src="small.jpg" alt="Description">
</picture>
```
The `<img>` fallback is required and used if no `<source>` matches (or the browser doesn't support `<picture>`).

**Q59. What is the `srcset` and `sizes` attribute on `<img>` used for, and how does it differ from `<picture>`?**
`srcset` provides multiple image resolution/width variants for the **same** image, letting the browser pick the most appropriate one based on device pixel density and viewport size (resolution switching, not art direction): `<img src="med.jpg" srcset="small.jpg 480w, med.jpg 800w, large.jpg 1200w" sizes="(max-width: 600px) 480px, 800px">`. Unlike `<picture>` (which lets you serve genuinely *different* cropped/composed images at different breakpoints), `srcset`/`sizes` is for serving different **resolutions of the same image**.

**Q60. What is lazy loading, and how do you enable it natively in HTML?**
Deferring the loading of off-screen images/iframes until the user scrolls near them, improving initial page load performance. Enabled natively via `<img src="pic.jpg" loading="lazy" alt="...">` — `loading="lazy"` is a simple, native browser feature requiring no JavaScript library, though it should be avoided on above-the-fold/critical images (which should load eagerly, or even be preloaded, for fastest perceived performance).

**Q61. What are common web image formats, and when would you use WebP or AVIF over JPEG/PNG?**
JPEG (good for photos, lossy compression), PNG (supports transparency, lossless, good for graphics/icons), GIF (supports simple animation, limited color palette), SVG (vector graphics, infinitely scalable, ideal for icons/logos). **WebP** and **AVIF** are modern formats offering significantly better compression than JPEG/PNG at equivalent visual quality, reducing file size/improving load performance — used via `<picture>` with a fallback for broader compatibility, or directly if targeting only modern browsers.

---

## 9. Audio, Video & Embedded Content

**Q62. How do you embed video and audio natively in HTML5?**
```html
<video controls width="640" poster="thumbnail.jpg">
    <source src="video.mp4" type="video/mp4">
    <source src="video.webm" type="video/webm">
    Your browser doesn't support video.
</video>
<audio controls>
    <source src="audio.mp3" type="audio/mpeg">
</audio>
```
Multiple `<source>` elements allow providing format fallbacks, since browser codec support varies; the browser picks the first one it supports.

**Q63. What are common attributes for `<video>` and `<audio>` elements?**
`controls` (shows native playback UI), `autoplay` (starts playing automatically — often blocked/restricted by browsers unless also `muted`), `loop`, `muted`, `preload` (`none`/`metadata`/`auto` — hints how much to preload), `poster` (video only — a placeholder image shown before playback starts).

**Q64. What is the `<track>` element used for?**
Adds timed text tracks (subtitles, captions, descriptions) to `<video>`/`<audio>` elements, typically using WebVTT files: `<track kind="subtitles" src="subs-en.vtt" srclang="en" label="English">` — important for accessibility (captions for deaf/hard-of-hearing users) and internationalization.

**Q65. What is an `<iframe>`, and what security considerations apply when using one?**
Embeds another HTML document within the current page (e.g., embedding a YouTube video, a map, or third-party widget). Security considerations: only embed content from trusted sources (an iframe can potentially be used for clickjacking attacks); use the `sandbox` attribute to restrict the embedded content's capabilities (disabling scripts, form submission, or top-level navigation as needed); consider `loading="lazy"` for off-screen iframes to improve performance.

**Q66. What does the `sandbox` attribute on an `<iframe>` do?**
Applies a set of restrictions to the embedded content by default (disabling scripts, form submission, plugins, popups, and treating the content as being from a unique origin), which can then be selectively re-enabled via space-separated tokens (`sandbox="allow-scripts allow-same-origin"`) — a defense-in-depth mechanism for safely embedding less-trusted third-party content.

---

## 10. HTML5 Canvas & SVG

**Q67. What is the `<canvas>` element, and how is it typically used?**
Provides a blank, scriptable bitmap drawing surface — content is rendered entirely via JavaScript (using the Canvas 2D API or WebGL for 3D), pixel by pixel, with no retained DOM representation of the drawn shapes (the browser only knows about the final rendered pixels, not individual "shape" objects). Commonly used for games, data visualizations, image manipulation, and animations requiring frequent, high-performance redraws.

**Q68. What is SVG, and how does it differ from Canvas?**
SVG (Scalable Vector Graphics) is an XML-based vector image format where shapes are defined **declaratively** as DOM elements (`<circle>`, `<path>`, `<rect>`) — each shape remains an individually addressable, stylable, and scriptable DOM node, and scales infinitely without quality loss (resolution-independent). **Canvas** is imperative/pixel-based (you draw pixels via JS calls, with no persistent object model of what was drawn) and better suited for scenarios with many rapidly-changing elements (particle effects, games) where per-object DOM overhead would be too costly; **SVG** is better for scalable graphics, icons, charts/diagrams needing individual element interactivity/accessibility, and cases where DOM-based styling/animation (CSS/JS on individual shapes) is desired.

**Q69. How do you embed SVG in an HTML document, and what are the different approaches?**
- **Inline** (`<svg>...</svg>` directly in the HTML): allows CSS styling and JavaScript manipulation of individual SVG elements, and it's part of the DOM.
- `<img src="icon.svg">`: simple, but the SVG becomes an opaque image — can't be styled/scripted from the parent page's CSS/JS.
- CSS `background-image: url(icon.svg)`: for decorative, non-interactive graphics.
- `<object>`/`<iframe>`: embeds the SVG as an external document, offering some scripting access but with additional overhead.

**Q70. Why is SVG generally preferred over raster formats (PNG/JPEG) for icons and logos?**
SVG is **resolution-independent** — scales to any size (including high-DPI/Retina displays) without pixelation or quality loss, typically results in smaller file sizes for simple graphics, and (as markup) can be styled with CSS (colors, hover states) and animated, unlike a static raster image.

---

## 11. Metadata, SEO & the `<head>`

**Q71. What is the purpose of `<meta charset="UTF-8">`?**
Declares the character encoding used by the document, ensuring text (including special characters, emoji, non-Latin scripts) is interpreted and displayed correctly by the browser. UTF-8 is the near-universal standard, supporting virtually all characters/languages, and should be declared as early as possible within `<head>`.

**Q72. What does `<meta name="viewport" content="width=device-width, initial-scale=1.0">` do?**
Instructs mobile browsers to set the viewport width to match the device's actual screen width (rather than a default desktop-oriented virtual viewport, e.g., 980px, which would force zooming out on mobile) and sets the initial zoom level to 100% — a foundational requirement for responsive web design to render correctly on mobile devices.

**Q73. What is the `<meta name="description">` tag used for?**
Provides a brief summary of the page's content, commonly displayed as the snippet text underneath the page title in search engine results — while it doesn't directly influence search ranking significantly, a well-written description can improve click-through rates from search results.

**Q74. What are Open Graph meta tags, and what are they used for?**
A set of `<meta property="og:...">` tags (originally developed by Facebook, now widely adopted) that control how a page is represented when shared on social media platforms — `og:title`, `og:description`, `og:image`, `og:url`, `og:type` — determining the preview card's appearance on platforms like Facebook, LinkedIn, and others that support the Open Graph protocol.

**Q75. What is the difference between `<link rel="stylesheet">`, `<link rel="icon">`, and `<link rel="canonical">`?**
- `rel="stylesheet"`: links an external CSS file.
- `rel="icon"`: specifies the favicon shown in browser tabs/bookmarks.
- `rel="canonical"`: specifies the "preferred"/authoritative URL for the current page's content, helping search engines avoid treating duplicate-content URLs (e.g., with different query parameters) as separate pages, consolidating SEO ranking signals to the canonical URL.

**Q76. What is the purpose of `robots.txt` and the `<meta name="robots">` tag?**
`robots.txt` (a file at the site's root) provides site-wide crawling directives to search engine bots (which paths to allow/disallow crawling). The `<meta name="robots" content="noindex, nofollow">` tag provides page-level directives — `noindex` prevents that specific page from appearing in search results, `nofollow` tells crawlers not to follow links on that page — offering more granular, per-page control than `robots.txt` alone.

**Q77. What is structured data / Schema.org markup, and why is it used?**
Structured, standardized metadata (often via JSON-LD embedded in a `<script type="application/ld+json">` tag, or via microdata attributes directly in HTML) describing the page's content in a machine-readable format search engines understand — enabling **rich results** in search listings (star ratings, recipe cook times, event dates, breadcrumbs) beyond plain blue-link snippets, improving visibility/click-through.

---

## 12. Accessibility (a11y) & ARIA

**Q78. What is web accessibility, and why does it matter?**
The practice of designing and building web content usable by people with a wide range of disabilities (visual, auditory, motor, cognitive) — including compatibility with assistive technologies like screen readers, keyboard-only navigation, and voice control. It matters both as a matter of **inclusive design/equal access** and increasingly as a **legal requirement** in many jurisdictions (e.g., ADA, WCAG-based regulations).

**Q79. What is WCAG, and what are its four core principles?**
The Web Content Accessibility Guidelines, the internationally recognized standard for web accessibility, organized around four principles (**POUR**): **Perceivable** (content must be presentable in ways users can perceive, e.g., text alternatives for images), **Operable** (interface components must be operable, e.g., full keyboard accessibility), **Understandable** (content and operation must be understandable, e.g., consistent navigation, clear error messages), **Robust** (content must be robust enough to work with a wide variety of user agents, including assistive technologies).

**Q80. What is ARIA, and when should it be used?**
Accessible Rich Internet Applications — a set of attributes (`role`, `aria-label`, `aria-hidden`, `aria-expanded`, etc.) that supplement HTML to improve accessibility, particularly for custom/complex interactive widgets that don't have a native semantic HTML equivalent (e.g., a custom dropdown, tab panel, or modal dialog). The core guiding principle ("**the first rule of ARIA**") is: **prefer native semantic HTML elements over ARIA whenever possible** — e.g., use a real `<button>` rather than a `<div role="button">`, since native elements come with built-in keyboard interaction, focus management, and semantics for free, which ARIA alone doesn't provide (ARIA only conveys semantics to assistive tech; it doesn't add behavior).

**Q81. What is the difference between `aria-label`, `aria-labelledby`, and `aria-describedby`?**
- `aria-label`: provides a direct, explicit accessible name string for an element (used when no visible text label exists).
- `aria-labelledby`: references the `id` of another element whose text content serves as this element's accessible name (useful when the labeling text already exists visibly elsewhere on the page).
- `aria-describedby`: references the `id` of another element providing additional descriptive detail (not the primary name) — e.g., linking a form field to its validation error message or supplementary hint text.

**Q82. What does `aria-hidden="true"` do, and what's a common mistake with it?**
Hides an element (and its descendants) from the accessibility tree, so screen readers skip it entirely — used for purely decorative/duplicate content (e.g., a decorative icon accompanying a text label that already conveys the same meaning). Common mistake: applying `aria-hidden="true"` to an element that still contains **focusable** content (like an interactive button) — this creates a confusing experience where keyboard/screen-reader users can still tab to the element but it's invisible/unannounced to assistive technology.

**Q83. What is `tabindex`, and what do its different values mean?**
Controls whether/how an element participates in sequential keyboard (Tab key) navigation. `tabindex="0"` makes an otherwise non-focusable element (like a `<div>`) focusable and includes it in the natural tab order. `tabindex="-1"` makes an element programmatically focusable (via JavaScript `.focus()`) but removes it from the natural tab sequence. **Positive** `tabindex` values (`tabindex="1"`, `"2"`, etc.) explicitly override the natural tab order — strongly discouraged, since it creates a confusing, hard-to-maintain navigation order disconnected from the visual/DOM layout.

**Q84. Why should native interactive elements (`<button>`, `<a>`) be preferred over styled `<div>`s with click handlers?**
Native elements provide, entirely for free: correct keyboard interaction (Enter/Space activates a button; Enter activates a link — a plain `<div>` gets none of this automatically), built-in focus styling and inclusion in tab order, and correct semantic role announcement to screen readers — recreating all of this manually with ARIA/JavaScript on a generic `<div>` is more error-prone and rarely as robust as the native element's built-in behavior.

**Q85. How do you make an image accessible if it's purely decorative vs. conveying meaningful information?**
For a **meaningful** image, provide a concise, descriptive `alt` text conveying the image's purpose/content (`alt="Bar chart showing Q3 revenue growth of 15%"`). For a **purely decorative** image adding no informational value, use an empty `alt=""` (not omitted entirely) so screen readers skip announcing it rather than reading out an unhelpful filename or generic description.

**Q86. What are skip links, and why are they important for accessibility?**
A hidden-until-focused link, typically placed as the very first focusable element on a page, allowing keyboard users to jump directly to the main content, bypassing repetitive navigation menus that would otherwise need to be tabbed through on every single page load: `<a href="#main-content" class="skip-link">Skip to main content</a>`.

---

## 13. HTML5 APIs

**Q87. What is the Geolocation API?**
A JavaScript API allowing web applications (with explicit user permission) to access the user's approximate geographic location: `navigator.geolocation.getCurrentPosition(successCallback, errorCallback)` — used for location-based features like maps, local search, or weather.

**Q88. What is the Drag and Drop API?**
A native HTML5 API enabling drag-and-drop interactions between elements on a page (or with the OS file system) without third-party libraries, using the `draggable="true"` attribute plus a set of drag-related events (`dragstart`, `dragover`, `drop`, `dragend`) and the `DataTransfer` object for carrying data between the drag source and drop target.

**Q89. What is the Web Workers API, and why is it useful?**
Allows running JavaScript in a background thread, separate from the main UI thread — enabling CPU-intensive computations (data processing, image manipulation) to run without blocking/freezing the page's UI responsiveness. Workers communicate with the main thread via message passing (`postMessage`/`onmessage`) since they don't share direct access to the DOM.

**Q90. What is the History API, and what problem does it solve for single-page applications (SPAs)?**
Allows JavaScript to manipulate the browser's session history and URL without triggering a full page reload — `history.pushState()`/`history.replaceState()` change the URL shown in the address bar while updating page content via JavaScript, and the `popstate` event fires when the user navigates via back/forward buttons — foundational to how client-side routing works in SPAs, allowing bookmarkable/shareable URLs without server round-trips for every navigation.

**Q91. What is the Fetch API, and how does it compare to the older `XMLHttpRequest`?**
A modern, Promise-based API for making HTTP requests from JavaScript, replacing the older, more verbose and callback-based `XMLHttpRequest`. Offers a cleaner syntax, native Promise integration (chaining with `.then()`/`async-await`), and a more flexible Request/Response object model — though it requires explicit handling of HTTP error status codes (Fetch doesn't reject the promise on a 404/500 response, only on network-level failures, unlike some might assume).

**Q92. What is the Intersection Observer API, and what common use cases does it address?**
An API for efficiently detecting when an element enters or exits the browser's viewport (or another specified ancestor element) asynchronously, without the performance cost of manually polling `getBoundingClientRect()` on every scroll event. Common use cases: implementing custom lazy-loading of images/content, infinite scroll, triggering animations when content scrolls into view, and tracking ad/content visibility for analytics.

**Q93. What is the Web Notifications API?**
Allows web applications (with user permission) to display system-level notifications outside the browser window/tab — useful for alerting users to time-sensitive updates (new messages, reminders) even when the browser tab isn't in focus.

**Q94. What is the difference between `window.postMessage()` and traditional AJAX requests?**
`postMessage()` enables safe **cross-origin communication between different browsing contexts** (e.g., a page and an embedded iframe from a different origin, or between browser tabs/windows) that the Same-Origin Policy would otherwise block from directly accessing each other's DOM/JavaScript — it's a client-side-to-client-side messaging mechanism, distinct from AJAX (which is a client-to-server HTTP communication mechanism).

---

## 14. Web Storage & Offline

**Q95. What is the difference between `localStorage` and `sessionStorage`?**
Both are key-value storage APIs (part of the Web Storage API) storing string data in the browser, with roughly 5–10MB capacity per origin. `localStorage` persists **indefinitely** (until explicitly cleared by the application or the user), and data is shared across all tabs/windows for the same origin. `sessionStorage` persists only for the duration of the **current tab's session** (cleared when the tab is closed) and is not shared across different tabs, even for the same origin.

**Q96. What is the difference between cookies and Web Storage (`localStorage`/`sessionStorage`)?**
**Cookies** are automatically sent to the server with every HTTP request to the matching domain (adding overhead to every request), have a much smaller storage limit (~4KB), and can be configured with expiration, security flags (`HttpOnly`, `Secure`, `SameSite`), and domain/path scoping — historically used for both client-side state and server-communicated session identifiers. **Web Storage** is purely client-side (never automatically sent to the server), has a larger capacity, and is accessed only via JavaScript — better suited for storing larger amounts of client-only state that the server doesn't need on every request.

**Q97. What is IndexedDB, and when would you use it over `localStorage`?**
A low-level, transactional, **asynchronous** client-side database API supporting structured data (not just strings), indexes for efficient querying, and much larger storage capacity than `localStorage`. Used when an application needs to store significant amounts of structured data client-side (e.g., offline-capable apps caching large datasets, complex client-side data that needs querying) — `localStorage`'s synchronous API and string-only, unindexed nature make it unsuitable for anything beyond small amounts of simple key-value data.

**Q98. What is a Service Worker, and what role does it play in enabling offline web applications (PWAs)?**
A JavaScript file that runs in the background, separate from the web page, acting as a programmable network proxy — able to intercept and handle network requests (e.g., serving cached responses when offline), manage a cache of assets (via the Cache API), and receive push notifications even when the site isn't open. It's the foundational technology enabling **Progressive Web Apps (PWAs)** to work offline and load reliably regardless of network conditions.

**Q99. What is a Web App Manifest, and what does it enable?**
A JSON file (`manifest.json`, linked via `<link rel="manifest" href="/manifest.json">`) describing a web application's metadata (name, icons, theme colors, start URL, display mode) — enabling the "Add to Home Screen" functionality on mobile devices and allowing a PWA to be launched in a standalone, app-like window rather than within a browser tab/address bar.

---

## 15. Web Components

**Q100. What are Web Components, and what three main technologies make them up?**
A set of native browser APIs for building reusable, encapsulated custom UI elements, without relying on a JavaScript framework. Three core technologies: **Custom Elements** (defining new HTML tags with their own JavaScript-driven behavior), **Shadow DOM** (encapsulated, isolated DOM/CSS scoping so a component's internal styles/markup don't leak out or get affected by the page's global styles), and **HTML Templates** (`<template>`/`<slot>` — reusable, inert markup fragments that can be cloned and instantiated).

**Q101. What is the `<template>` element, and how does its content differ from normal HTML?**
Defines a chunk of reusable HTML markup that is **parsed but not rendered** by the browser and is inert (scripts don't execute, images don't load) until explicitly cloned and inserted into the document via JavaScript (`template.content.cloneNode(true)`) — useful for defining reusable markup patterns to be instantiated repeatedly (e.g., a table row template for dynamically-populated data).

**Q102. What is the Shadow DOM, and what problem does it solve?**
An encapsulation mechanism that attaches a hidden, separate DOM tree to an element, isolating its internal markup and CSS from the rest of the page — styles defined inside the shadow root don't leak out to affect the main page, and (mostly) the main page's global CSS doesn't leak in to affect the shadow content either. Solves the classic problem of CSS/JS collisions when building reusable components meant to be dropped into arbitrary pages without naming-convention workarounds (like BEM) to avoid style conflicts.

**Q103. How do you define a Custom Element?**
```javascript
class MyWidget extends HTMLElement {
    connectedCallback() {
        this.innerHTML = `<p>Hello from my widget!</p>`;
    }
}
customElements.define('my-widget', MyWidget);
```
```html
<my-widget></my-widget>
```
Custom element names must contain a hyphen (distinguishing them from standard HTML tags and guaranteeing no future collision with new native elements).

---

## 16. Responsive Design & Viewport

**Q104. What is responsive web design, and what HTML/CSS techniques support it?**
An approach to building web pages that adapt their layout and presentation to different screen sizes/devices, using: the viewport meta tag (Q72), CSS media queries (applying different styles at different breakpoints), flexible/fluid layouts (percentage-based or CSS Grid/Flexbox), and responsive images (`srcset`/`<picture>`, Q58–59).

**Q105. What is the difference between a "mobile-first" and "desktop-first" responsive design approach?**
**Mobile-first** starts with base styles designed for small screens, then progressively adds complexity/layout changes for larger screens via `min-width` media queries — generally recommended, since it forces prioritizing essential content/functionality and tends to produce leaner CSS. **Desktop-first** starts with the full desktop layout and uses `max-width` media queries to simplify/adapt for smaller screens — can result in more overridden CSS and sometimes worse mobile performance if not carefully managed.

**Q106. What is the `viewport` unit (`vw`/`vh`) in CSS, and how does it relate to responsive HTML layout?**
`1vw` equals 1% of the viewport's width, `1vh` equals 1% of the viewport's height — CSS units that scale directly and fluidly with the browser window/device screen size, useful for full-viewport layouts (e.g., a hero section exactly filling the visible screen height: `height: 100vh`) without needing JavaScript-calculated pixel values.

---

## 17. Performance

**Q107. What is the difference between placing `<script>` tags in the `<head>` vs. before `</body>`, and how do `async`/`defer` change this?**
A plain `<script>` in the `<head>` **blocks HTML parsing** until the script downloads and executes, delaying page rendering. Placing scripts just before `</body>` avoids blocking initial rendering, since the DOM is already parsed by the time the script runs. `async` downloads the script in parallel with HTML parsing and executes it **as soon as it's ready** (potentially interrupting parsing, and execution order relative to other async scripts isn't guaranteed). `defer` also downloads in parallel but delays **execution until after HTML parsing completes**, and defer scripts execute in their original document order — generally the best choice for scripts that need the DOM to be ready and don't need to block anything.

**Q108. What is `<link rel="preload">`, and how does it differ from `<link rel="prefetch">`?**
`preload` tells the browser to fetch a resource **needed for the current page** with high priority, early in the loading process (e.g., a critical web font or hero image that would otherwise be discovered late in the CSS/rendering pipeline) — used for resources you know are needed *now*. `prefetch` hints that a resource will likely be needed for a **future navigation** (e.g., the next page a user is likely to visit) and fetches it at low priority during idle time — a speculative optimization for anticipated future needs, not the current page.

**Q109. What is Cumulative Layout Shift (CLS), and what HTML practices help minimize it?**
A Core Web Vitals metric measuring unexpected visual layout movement during page load (e.g., content jumping down as an image or ad loads in above it) — a poor user experience, especially disruptive if it causes accidental mis-clicks. HTML practices to minimize it: always specifying `width`/`height` attributes on `<img>`/`<video>` (Q57) so space is reserved before load, reserving space for ads/embeds with fixed dimensions, and avoiding inserting new content above existing content without user interaction.

**Q110. How does the `loading="lazy"` attribute affect performance, and what should you avoid applying it to?**
As covered in Q60, it defers loading of off-screen images/iframes, reducing initial page load bytes/time and saving bandwidth for content the user might never scroll to. It should be **avoided on above-the-fold images** (visible without scrolling) — lazy-loading these would actually delay their appearance rather than help, since the browser wouldn't start fetching them until it determines they're needed, unnecessarily adding delay to the page's most immediately visible/critical content.

**Q111. What is resource hinting via `<link rel="dns-prefetch">` and `<link rel="preconnect">`?**
Both help reduce connection-setup latency for resources hosted on other domains (e.g., a CDN, a third-party font provider, an analytics endpoint). `dns-prefetch` resolves the DNS lookup for a domain early, before it's actually needed. `preconnect` goes further — performing the DNS lookup **and** the TCP handshake **and** (for HTTPS) the TLS negotiation ahead of time, so the actual resource request, when it happens, skips all that setup latency — more impactful but also more resource-intensive per hint, so used more selectively for critical cross-origin resources.

**Q112. Why does minimizing the number of HTML elements/DOM size matter for performance?**
A large, deeply-nested DOM increases memory usage, slows down CSS selector matching and layout/reflow calculations (especially costly when JavaScript repeatedly queries/modifies the DOM), and can degrade scrolling/rendering performance — keeping markup as lean and semantically appropriate as possible (avoiding unnecessary wrapper `<div>`s) benefits both performance and maintainability.

---

## 18. Security

**Q113. What is Cross-Site Scripting (XSS), and how does it relate to HTML?**
An attack where malicious JavaScript is injected into a page (often via unsanitized user input rendered directly into HTML) and executed in the context of a victim's browser session — potentially stealing cookies/session tokens, defacing the page, or performing actions as the victim. Prevention: always properly **escape/sanitize** any user-supplied content before inserting it into HTML (never directly concatenate raw user input into `innerHTML` or template strings without escaping), and use a **Content Security Policy** as defense in depth.

**Q114. What is Content Security Policy (CSP), and how is it applied via HTML?**
An HTTP response header (or equivalently, a `<meta http-equiv="Content-Security-Policy" content="...">` tag) that restricts which sources of content (scripts, styles, images, fonts) a page is allowed to load from, significantly mitigating the impact of XSS attacks by preventing injected scripts from executing if they violate the policy (e.g., disallowing inline `<script>` tags or scripts from untrusted domains): `<meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self' https://trusted-cdn.com">`.

**Q115. Why is it dangerous to use `innerHTML` with untrusted/user-supplied data?**
Setting `element.innerHTML = userInput` parses and renders the string as actual HTML, meaning any embedded `<script>` tags or event-handler attributes (`onerror="..."`, `onclick="..."`) within the user input would execute — a direct XSS vulnerability. Safer alternatives: use `element.textContent` (treats the value as plain text, never parsed as markup) when displaying user content, or sanitize the HTML with a trusted library (e.g., DOMPurify) if actual HTML rendering of user content is genuinely required.

**Q116. What HTML-related security headers/attributes help protect against clickjacking?**
The `X-Frame-Options` HTTP header (or the more modern CSP `frame-ancestors` directive) restricts whether a page can be embedded within an `<iframe>` on another site — preventing **clickjacking** attacks where an attacker overlays invisible iframes of a legitimate site (e.g., a banking site) over deceptive UI, tricking users into unknowingly clicking legitimate but sensitive controls.

**Q117. Why is autocomplete sometimes disabled on sensitive form fields, and how is it done?**
`autocomplete="off"` (or more specific values like `autocomplete="new-password"`) can be applied to prevent browsers from suggesting/auto-filling previously entered sensitive values (e.g., on a shared/public computer) — though modern browsers increasingly ignore `autocomplete="off"` for login fields specifically (prioritizing user convenience for password managers), so this is a partial mitigation, not a guarantee.

---

## 19. HTML Versions & Browser Compatibility

**Q118. What is the difference between HTML4, XHTML, and HTML5?**
**HTML4** (1997) was the long-standing standard with a relatively loose, forgiving parsing model. **XHTML** reformulated HTML as strict, well-formed XML (requiring properly closed/nested tags, lowercase element names) — stricter but less forgiving of minor markup errors (a single malformed tag could break the entire page). **HTML5** (the current standard, developed by WHATWG/W3C) introduced a well-defined parsing algorithm (so browsers handle malformed markup consistently rather than XHTML's fail-hard approach), new semantic elements, native audio/video/canvas support, and numerous new APIs (Web Storage, Geolocation, etc.) — HTML5 is what all modern web development uses today.

**Q119. What is the WHATWG "Living Standard" model for HTML, and how does it differ from versioned specifications?**
Since HTML5, the specification (maintained by WHATWG) is a continuously updated **"living standard"** rather than being released in discrete, numbered versions (like HTML 4.01, 5.0, 5.1) — new features and clarifications are added incrementally over time without a formal "HTML6" version number, reflecting how the web platform actually evolves in practice (browser vendors implement features incrementally as they're specified/stabilized, rather than all at once tied to a version release).

**Q120. What is progressive enhancement, and how does it relate to HTML feature support across browsers?**
A development philosophy of building a baseline, functional experience using well-supported, core HTML/CSS/JS first, then layering on more advanced features/enhancements for browsers that support them — rather than requiring the most advanced features for basic functionality to work at all. E.g., providing a functional `<select>` fallback while enhancing it with a richer custom JavaScript widget only where supported, ensuring the page remains usable even if a newer API/feature isn't available in a given browser.

**Q121. How do you check browser support for a given HTML feature before using it in production?**
Consult resources like **caniuse.com** (crowd-sourced browser compatibility tables for HTML/CSS/JS features) or MDN Web Docs' browser compatibility tables — checking which browsers/versions support a feature, and if targeting older browsers, either using a **polyfill** (JavaScript that replicates missing native functionality) or applying **feature detection** (checking `if ('loading' in HTMLImageElement.prototype)` before relying on a feature) rather than assuming universal support.

---

## 20. Best Practices & Common Pitfalls

**Q122. Why should you avoid deeply nested, non-semantic `<div>` structures ("div soup")?**
Excessive, purely presentational nesting hurts accessibility (screen readers have no meaningful landmarks to navigate by), makes CSS specificity/styling harder to manage, increases DOM size (performance), and makes markup harder for other developers to understand/maintain — favoring semantic elements and only as much nesting as structurally necessary produces cleaner, more maintainable, more accessible code.

**Q123. Why is it important to validate HTML markup (e.g., via the W3C validator), even though browsers are forgiving of errors?**
While browsers do their best to render malformed HTML gracefully (thanks to HTML5's well-defined error-recovery parsing algorithm), invalid markup can still cause subtle, hard-to-debug rendering inconsistencies across different browsers, break assistive technology parsing, and complicate future maintenance — valid markup is more predictable and portable.

**Q124. What is a common mistake with using multiple `<h1>` elements or skipping heading levels?**
While HTML5 technically permits multiple `<h1>`s (particularly within separate `<article>`/`<section>` contexts), best practice for accessibility and SEO clarity still favors a single, clear `<h1>` per page representing its main topic, with subsequent headings following a logical, non-skipping hierarchy (`<h2>` before `<h3>`, not jumping straight from `<h1>` to `<h4>`) — skipped levels are confusing for screen reader users navigating by heading structure, even if visually the page "looks fine."

**Q125. Why should form inputs always have an associated, properly linked `<label>`?**
Beyond the accessibility benefit (Q38/Q40), a properly associated label also increases the clickable/tappable target area (clicking the label text focuses/activates the input) — particularly valuable for small controls like checkboxes/radio buttons on touch devices, improving usability for all users, not just those using assistive technology.

**Q126. What is a common mistake when using `target="_blank"` without considering security or UX?**
Beyond the `noopener`/tabnabbing security issue (Q29), opening links in new tabs unexpectedly (especially internal navigation) can disorient users and break the expected behavior of the browser's back button — `target="_blank"` should generally be reserved for genuinely external/supplementary content where staying on the current page is valuable (e.g., linking out to a reference/citation), not used indiscriminately for all links.

**Q127. Why is it a mistake to rely solely on color to convey information in HTML/CSS (e.g., marking required form fields only in red)?**
Users with color vision deficiencies (color blindness) or those using non-color-rendering assistive technology may not perceive color-only cues at all — accessible design requires an additional, non-color indicator (e.g., an asterisk `*` plus text like "required," or an icon plus a text label) alongside any color-based signal, ensuring the information is conveyed through multiple channels (WCAG's "use of color" success criterion).

**Q128. What is a common mistake with using `<br>` tags for spacing/layout purposes?**
`<br>` represents an actual **line break within content** (e.g., in a poem or address), not a general-purpose spacing/layout tool — using multiple consecutive `<br>` tags to create visual vertical spacing is a misuse of semantic markup; spacing between elements should be controlled via CSS (`margin`/`padding`) instead, keeping HTML focused on content structure and CSS focused on presentation.
