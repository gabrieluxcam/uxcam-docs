# ReadMe.io Documentation Compliance Checklist

This document captures ReadMe platform requirements and best practices for the UXCam documentation repository.

---

## 1. Code Block Requirements

### Supported Language Tags
ReadMe supports these language tags for syntax highlighting:

| Category | Valid Tags |
|----------|------------|
| **Mobile** | `swift`, `kotlin`, `java`, `dart`, `objc`, `objectivec` |
| **Web** | `javascript`, `js`, `typescript`, `ts`, `jsx`, `html`, `css` |
| **Backend** | `python`, `py`, `ruby`, `rb`, `go`, `rust`, `php` |
| **Shell** | `bash`, `sh`, `shell`, `zsh`, `powershell` |
| **Data** | `json`, `yaml`, `yml`, `xml`, `sql` |
| **Other** | `csharp`, `cs`, `c`, `c++`, `cpp`, `graphql`, `markdown` |

### Tabbed Code Blocks
Multiple code blocks render as tabs when there's **NO blank line** between them:

```markdown
```swift iOS
// Swift code here
\```
```kotlin Android
// Kotlin code here
\```
```

**Tab names** go after the language tag, separated by a space.

### Invalid Language Tags to Avoid
- `coffeescript` (was incorrectly used as catch-all)
- `c iOS` (use `swift`)
- `javascript Android` (use `java` or `kotlin`)
- `text` for code (use specific language or omit tag)

---

## 2. MDX/JSX Requirements

ReadMe uses MDX (Markdown eXtended) which requires JSX-compliant HTML.

### Self-Closing Tags (REQUIRED)
All void elements must be self-closing:

| Invalid | Valid |
|---------|-------|
| `<br>` | `<br />` |
| `<img>` | `<img />` |
| `<hr>` | `<hr />` |
| `<input>` | `<input />` |

### Attribute Syntax
| HTML Attribute | JSX Attribute |
|----------------|---------------|
| `class="..."` | `className="..."` |
| `for="..."` | `htmlFor="..."` |
| `style="margin-left: auto"` | `style={{ marginLeft: 'auto' }}` |

**Exceptions**: `data-*` and `aria-*` attributes remain unchanged.

### Comments
| Invalid (HTML) | Valid (JSX) |
|----------------|-------------|
| `<!-- comment -->` | `{/* comment */}` |

### All Tags Must Be Closed
```html
<!-- Invalid -->
<p>Content
<li>Item 1
<li>Item 2

<!-- Valid -->
<p>Content</p>
<li>Item 1</li>
<li>Item 2</li>
```

---

## 3. Callout Syntax

ReadMe callouts use emoji-prefixed blockquotes:

### Theme Emojis
| Emoji | Theme | Color | Use For |
|-------|-------|-------|---------|
| 📘 | info | Blue | General information |
| 👍 | success | Green | Best practices, success states |
| 🚧 | warning | Orange | Cautions, deprecations |
| ❗️ | error | Red | Critical warnings, errors |

### Syntax
```markdown
> 📘 **Title Here**
>
> Body text goes here. Can include **formatting** and `code`.
```

### Current UXCam Pattern vs ReadMe Pattern
**Current (may not render correctly):**
```markdown
> **Note**: Some information here.
```

**ReadMe-Compliant:**
```markdown
> 📘 **Note**
>
> Some information here.
```

---

## 4. GitHub Sync Requirements

### Branch Naming
Branch names in GitHub **MUST exactly match** version names in ReadMe:

| ReadMe Version | GitHub Branch | Status |
|----------------|---------------|--------|
| `v1.1` | `v1.1` | ✅ Valid |
| `v2.0_new-branch` | `v2.0_new-branch` | ✅ Valid |
| `v2.0_new-branch` | `v2-new-branch` | ❌ Won't sync |

### Current Repository Branches
- Main branch: `v1.1`
- Current branch: `v0.0.1-github`

**Action Required**: Verify these match ReadMe version names.

---

## 5. Built-in Components Available

ReadMe provides these MDX components that can enhance documentation:

### Tabs Component
```jsx
<Tabs>
  <Tab title="Tab 1">Content for tab 1</Tab>
  <Tab title="Tab 2">Content for tab 2</Tab>
</Tabs>
```

### Accordion Component
```jsx
<Accordion title="Click to expand">
  Hidden content here
</Accordion>
```

### Cards Component
```jsx
<Cards>
  <Card title="Card 1" href="/docs/page1">Description</Card>
  <Card title="Card 2" href="/docs/page2">Description</Card>
</Cards>
```

### Columns Component
```jsx
<Columns>
  <Column>Left content</Column>
  <Column>Right content</Column>
</Columns>
```

---

## 6. Tables

Standard markdown table syntax is supported:

```markdown
| Left | Center | Right |
|:-----|:------:|------:|
| L1   | C1     | R1    |
| L2   | C2     | R2    |
```

Alignment:
- `:---` = left
- `:---:` = center
- `---:` = right

---

## 7. Images

```markdown
![Alt text](https://url.to/image.png "Optional title")
```

For sizing, use HTML with JSX syntax:
```jsx
<img src="https://url.to/image.png" alt="Alt text" style={{ maxWidth: '500px' }} />
```

---

## 8. Links

### Internal Links
```markdown
[Link text](/docs/page-slug)
```

### External Links
```markdown
[Link text](https://external-url.com)
```

---

## 9. Frontmatter

ReadMe supports YAML frontmatter for page metadata:

```yaml
---
title: Page Title
excerpt: Brief description for SEO
deprecated: false
hidden: false
metadata:
  title: SEO title override
  description: SEO description
  robots: index
next:
  description: ''
  pages:
    - type: basic
      slug: next-page-slug
      title: Next Page Title
---
```

---

## 10. Audit Results

### Priority 1: MDX/JSX Issues
Scan all `.md` files for:
- [x] Unclosed HTML tags (`<br>`, `<img>`, `<hr>`) - **None found**
- [x] `class=` attributes outside code blocks - **Fixed 4 instances**
  - `flutter-occlusion-feature.md`: Converted `<pre><code class=...>` to markdown code blocks
  - `ios/index.md`: Changed `class=` to `className=` (3 instances)
- [x] Inline `style=` strings - **Already using JSX objects correctly**
- [x] HTML comments `<!-- -->` - **Only inside code examples (valid)**

### Priority 2: Code Block Syntax
- [x] Fixed `coffeescript` tags → proper languages (50+ fixes)
- [x] Fixed `javascript Android` → `java`
- [x] Fixed `c iOS` → `swift`
- [x] All language tags verified against supported list

### Priority 3: Callouts
6 files found with potential `> **Note**` patterns to review:
- `docs/Mobile SDK Installation/cordova/sensitive-data-occlusion-cordova.md`
- `docs/Mobile SDK Installation/nativescript/sensitive-data-occlusion-nativescript.md`
- `docs/ALL UXCAM APIs/tag-of-screens/index.md`
- `docs/Mobile SDK Installation/react-native/advanced-configuration-and-apis-2/crashes-and-anrs-react-native.md`
- `docs/Mobile SDK Installation/flutter/sensitive-data-occlusion-flutter/index.md`
- `docs/ALL UXCAM APIs/screen-blurring/jetpack-compose-occlusion.md`

### Priority 4: Feature Opportunities Already In Use
The codebase already uses advanced ReadMe components:
- `<GitHubCallout>` - Custom callout component
- `<SimpleStepper>` / `<SimpleStep>` - Step-by-step guides
- `<Accordion>` - Collapsible content
- `<Cards>` / `<Card>` - Navigation cards
- `<Terminal>` - Terminal command display
- `<Embed>` - YouTube embeds
- `<Image>` - Image component

---

## Status

| Area | Status | Notes |
|------|--------|-------|
| Code block languages | ✅ Fixed | 50+ syntax errors corrected |
| Self-closing tags | ✅ Complete | No issues found |
| MDX class attributes | ✅ Fixed | 4 instances corrected |
| HTML comments | ✅ Complete | Only in code examples |
| Callout syntax | ⚠️ Optional | 6 files could use emoji callouts |
| Built-in components | ✅ In Use | Custom components already implemented |
| Branch naming | ⚠️ Verify | Check `v1.1` matches ReadMe version |
