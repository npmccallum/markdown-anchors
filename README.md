# Markdown Anchor Extension

## Document Information

- Date: March 30, 2025
- Version: 0.1.0

## Introduction

This document defines an extension to Markdown that enables the creation of
non-structural "anchors" within text documents. These anchors allow for precise
referencing of specific passages, statements, or locations without affecting
document organization or hierarchy.

Many document types require fine-grained reference systems that operate
independently of structural elements like headings and sections. Academic papers
need to reference specific paragraphs, sentences, or even phrases. Legal
documents require precise citation to particular clauses, subsections, or
provisions. Technical standards often include detailed numbered requirements
that must be referenced exactly. Historical documents and their commentaries
need systems for citing specific passages. Literary analysis requires references
to particular lines, verses, or numbered segments. Religious and philosophical
texts employ traditional versification systems for precise quotation and
cross-reference.

Unlike heading-based references, these anchor systems often ingore structural
boundaries. A single numbered anchor might refer to multiple paragraphs, break
mid-sentence, or appear within lists, tables, or other formatted content. The
anchor markers are overlaid onto the document's logical structure rather than
defining it.

Current markdown provides excellent support for document structure through
headings, but lacks a mechanism for creating these non-structural anchor points.
This extension addresses this gap by providing a minimal syntax for creating
linkable location markers.

This extension can be implemented as a simple preprocessor that runs before
standard markdown processing, making it immediately adoptable without requiring
parser modifications. The preprocessor replaces anchor syntax with HTML spans,
ensuring compatibility with any existing markdown processor.

## Syntax

An anchor provides a simple syntax to facilitate non-structure reference points.
There are three forms of anchor:

### Invisible Form

Creates an invisible linkable anchor:

```markdown
[@v1.1] This text has an invisible anchor at the beginning.
```

This renders as:

```html
<span id="v1.1"></span> This text has an invisible anchor at the beginning.
```

### Visible Form

Creates a visible anchor that displays the ID:

```markdown
[@v1.1!] This text has a visible anchor showing the ID.
```

This renders as:

```html
<span id="v1.1">v1.1</span> This text has a visible anchor showing the ID.
```

### Custom Form

Creates a visible anchor with custom display text:

```markdown
[@chapter-1-opening!1.1] It was the best of times, it was the worst of times, it
was the age of wisdom, it was the age of foolishness.
```

This renders as:

```html
<span id="chapter-1-opening">1.1</span> It was the best of times, it was the
worst of times, it was the age of wisdom, it was the age of foolishness.
```

## Referencing Anchors

Anchors can be referenced using standard CommonMark fragment links. No change to
the parser is necessary.

```markdown
As mentioned in [the opening passage](#v1.1), the novel begins with contrasting
themes. As mentioned in [chapter 1.1](#chapter-1-opening), Dickens establishes
the central paradox.
```

## Interaction with Existing Markdown Elements

### Headings

```markdown
[@intro] # Introduction

# [@key-concepts!Concepts] and Applications
```

Renders as:

```html
<span id="intro"></span>
<h1>Introduction</h1>
<h1><span id="key-concepts">Concepts</span> and Applications</h1>
```

### Blockquotes

```markdown
> [@quote1] This is an anchored quote.
```

Renders as:

```html
<blockquote>
  <p><span id="quote1"></span> This is an anchored quote.</p>
</blockquote>
```

### Tables

```markdown
| Section            | Description               |
| ------------------ | ------------------------- |
| [@sec1!sec1] First | Description of first item |
```

Renders as:

```html
<table>
  <thead>
    <tr>
      <th>Section</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><span id="sec1">sec1</span> First</td>
      <td>Description of first item</td>
    </tr>
  </tbody>
</table>
```

### Code Blocks

Anchors within code blocks are treated as literal text. No span element is
emitted.

````markdown
```
[@not-an-anchor] This is code, not an anchor
```

`[@also-not-an-anchor] This is inline code`
````

## Escaping

To display literal anchor syntax, use backslash escaping:

```markdown
\[@v1.1] This is not an anchor, just literal text. \[@long-id!1.1] This is also
not an anchor.
```

## Examples

### Classical Text Citations

```markdown
# Aristotle, Nicomachean Ethics Book I

[@ne-1094a1!1094a1] Every art and every inquiry, and similarly every action and
pursuit, is thought to aim at some good; and for this reason the good has
rightly been declared to be that at which all things aim. [@ne-1094a3!1094a3]
But a certain difference is found among ends; some are activities, others are
products apart from the activities that produce them.

As established in [the opening argument](#ne-1094a1), Aristotle claims that all
human activities aim at some perceived good.
```

## Rationale

The `[@id]` and related syntaxes were chosen because:

1. **Consistency with linking patterns**: Square brackets are used throughout
   CommonMark for linking constructs (links, reference links, footnotes), making
   this extension feel natural
2. **No parsing conflicts**: The `[@id]`, `[@id!]`, and `[@id!display]` patterns
   do not conflict with any existing CommonMark syntax
3. **Clean separation of concerns**: Invisible form (`[@id]`) creates invisible
   linkable points, visible forms (`[@id!]` and `[@id!display]`) add visible
   text when needed
4. **Unambiguous parsing**: The exclamation mark clearly indicates when display
   text should appear, eliminating parsing ambiguity
5. **Leverages existing linking**: References use standard CommonMark fragment
   links, requiring no new syntax
6. **Minimal HTML**: Produces simple, clean HTML without mandating presentation
   decisions
7. **Unified namespace**: Works seamlessly with any other ID-generating
   mechanisms since they all share the HTML ID namespace

The choice to treat anchors as pure location markers reflects the reality of
versification systems where markers often don't align with structural document
elements and may appear mid-sentence or mid-thought.
