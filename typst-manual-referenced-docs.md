### Tracking references using Typst

Rather than using the built-in bibliography, it looks as though Typst is flexible enough to generate referencs in any format. The following example uses a table format.

First, a file to keep the list of documents, alternatively the documents could easily be loaded from JSON or CSV.

```typst
// ref-docs.typ

#show figure.where(kind: "req"): it => it.body // remove block

#let req-counter = counter("req")
#req-counter.update(1)

// doc definition
#let docs = (
    (id: "55", title: "A requirement on computational load", priority: 1),
    (id: "777", title: "A requirement on UI", priority: 2),
)

// figure definition for doc referencing
#for doc in docs {
  let req() = figure(
    kind: "req",
    supplement: [],
    numbering: n => [[#n]]
  )[]
  context [#req()#label(str("req:" + doc.id))]
}

// table definitions
#let ReferencedDocuments = docs.map(
    it => (
        context [*[#req-counter.display()#req-counter.step()]*],
        it.id,
        it.title
    )
).flatten()
```

Then we can import this to either genereate the reference table:
```typst
// ref-table.typ

#include "ref-docs.typ"
#import("ref-docs.typ"): ReferencedDocuments

#table(
  columns: 3,
  align: (center, left, center),
  stroke: none,
  table.hline(stroke: 0.75pt, position: bottom),
  table.header[*No*][*ID*][*Requirement*],
  ..ReferencedDocuments
)
```

Or references to the documents in the reference table:

```typst
// ref-refs.typ

#include "ref-docs.typ"

A document referencing something

@req:777

@req:55
```

Currently these scripts are optimal for producing individual markdown for each page or section, whilst automatically maintaining the numbering in the documents table and the references.

The [typlite](https://github.com/Myriad-Dreamin/tinymist/tree/main/crates/typlite) crate is a lightweight option for generating the resulting output:
```
$ typelite ref-table.typ
$ typelite ref-refs.typ
```

Solution based on this forum post:

https://forum.typst.app/t/how-do-i-include-custom-references-to-items-in-a-table/4409
