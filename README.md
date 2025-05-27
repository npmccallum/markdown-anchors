# Markdown Anchor Extension

## Document Information

- Date: May 27, 2025
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

Unlike heading-based references, these anchor systems often ignore structural
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
ensuring compatibility with any existing markdown processor. Likewise, a similar
strategy can be taken with postprocessing.

## Syntax

There are two forms of Markdown anchor:

- `<#foo>` - renders: `<span id="foo">foo</span>`
- `[bar]<#foo>` - renders: `<span id="foo">bar</span>`

The second form of the anchor syntax contains two fields (from left to right):

- Text
- ID

### Text Field

The text field has exactly the same syntax and semantics as the text field of
the link element as defined in [CommonMark][commonmark-links].

The text field may be empty to create invisible anchors. For example, `[]<#foo>`
renders as `<span id="foo"></span>`.

### ID Field

The ID field has exactly the same syntax and semantics of the `id` attribute as
defined in the [HTML Living Standard][html-id]. Note especially that IDs must
have at least one character and be unique.

Additionally, since angle brackets (`<` and `>`) are used as syntax delimiters,
the ID field requires that they must either be balanced or escaped. This mirrors
the same syntax and semantics as the square brackets (`[` and `]`) in the text
field of the link element as defined in [CommonMark][commonmark-links].

For example:

- Balanced: `<#my<id>>`
  - Renders: `<span id="my<id>">my&lt;id&gt;</span>`
  - Note: Propagated angle brackets must be escaped in HTML content!
- Escaped: `[text]<#foo\>bar>`
  - Renders: `<span id="foo>bar">text</span>`

## Referencing Anchors

Anchors can be referenced using standard CommonMark fragment links. No change to
the parser is necessary.

```markdown
As mentioned in [the opening passage](#v1.1), the novel begins with contrasting
themes. As mentioned in [chapter 1.1](#chapter-1-opening), Dickens establishes
the central paradox.
```

## Interaction with Markdown Elements

### Existing Elements

Since anchors behave substantially like links, parsers should ensure that they
can be used in all the same places that links are used, according to the
specification followed by the parser. Broadly speaking (after a quick review of
some of the specifications), this implies that anchors can be used in headings,
blockquotes, lists, and tables, but not code blocks. However, this summary does
not imply an implementation constraint beyond the underlying Markdown
specification you are implementing.

### Nested Anchors

While nested anchors are syntactically valid (e.g., `[<#inner>]<#outer>`), they
are not recommended as they create complex ID relationships and may confuse
readers about which anchor is being referenced.

## Examples

### Classical Text Citations

```markdown
# Aristotle, Nicomachean Ethics Book I

[1094a1]<#ne-1094a1> Every art and every inquiry, and similarly every action and
pursuit, is thought to aim at some good; and for this reason the good has
rightly been declared to be that at which all things aim. [1094a3]<#ne-1094a3>
But a certain difference is found among ends; some are activities, others are
products apart from the activities that produce them.

As established in [the opening argument](#ne-1094a1), Aristotle claims that all
human activities aim at some perceived good.
```

## Rationale

Some parsers already support the syntax of `# Heading {#id}`. This clearly
defines a need to attach ID to structural elements. However, no such parallel
exists for non-structural anchors. The syntax defined in this document can be
used in headings. This implies that this syntax can either supplement or replace
this existing extension.

The `<#id>` and `[display]<#id>` syntaxes were chosen because:

1. **Consistency with fragment identifiers**: The `#` symbol is already
   associated with anchors and fragments in URLs, making this extension feel
   natural.
2. **No parsing conflicts**: The `<#id>` and `[display]<#id>` patterns do not
   conflict with any existing CommonMark syntax and tested clean across major
   parsers.
3. **Clean separation of concerns**: The simple form (`<#id>`) uses the ID as
   display text, while the custom form (`[display]<#id>`) allows any display
   text including empty for invisible anchors. This gives full flexibility.
4. **Familiar bracket usage**: The `[display]` portion follows the same rules as
   CommonMark link text, leveraging existing knowledge.
5. **Leverages existing linking**: References use standard CommonMark fragment
   links, requiring no new syntax.
6. **Minimal HTML**: Produces simple, clean HTML without mandating presentation
   decisions.
7. **Unified namespace**: Works seamlessly with any other ID-generating
   mechanisms since they all share the HTML ID namespace.

The choice to treat anchors as pure location markers reflects the reality of
versification systems where markers often don't align with structural document
elements and may appear mid-sentence or mid-thought.

## Security Considerations

When implementing this extension, consider the following security implications:

1. **HTML Injection**: Since anchor IDs are inserted directly into HTML `id`
   attributes, implementers must ensure that IDs are properly sanitized
   according to HTML standards. Malicious IDs could potentially break HTML
   structure or introduce unexpected behavior.

2. **XSS Prevention**: While `id` attributes have limited XSS potential compared
   to other HTML attributes, implementers should validate that IDs conform to
   HTML specifications and do not contain characters that could be
   misinterpreted by browsers.

3. **ID Collision Attacks**: In environments where multiple users can contribute
   content, implementers should consider how duplicate IDs are handled to
   prevent one user from overriding another's anchor references.

4. **Content Injection**: The text portion of anchors follows CommonMark link
   text rules, which may allow HTML entities or other markup. Implementers
   should apply the same security measures used for link text processing.

These considerations are not unique to this extension but apply to any system
that processes user-provided IDs or generates HTML from markdown content.

[commonmark-links]: https://spec.commonmark.org/0.31.2/#links
[html-id]: https://html.spec.whatwg.org/multipage/dom.html#the-id-attribute
