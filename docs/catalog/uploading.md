---
title: Uploading items
---

<p class="kicker">Catalog</p>

# Uploading items

<p class="lead">Every portfolio starts with <strong>items</strong> — the individual works you catalogue in CatalogueCanvas. This guide covers adding files, the formats we support, and getting metadata right the first time.</p>

!!! info "Key Concept"

    A key concept in this software is that a piece of work being stored is together with their metadata. Those being the necesary files to reproduce that piece of work, a json files with settings, a pdf file, pictures...etc

## Upload from the dashboard

The dashboard is the fastest way to add a handful of items. Drag files straight onto the
dropzone, or browse from disk — CatalogueCanvas renders a preview plate for each one as it lands.

<ol class="steps" markdown>
<li markdown>Open the **Catalog** and click **Upload** in the top nav — or press <kbd>⌘</kbd> <kbd>U</kbd> from anywhere.</li>
<li markdown>Drag files onto the dropzone, or click **Browse** to pick them from disk.</li>
<li markdown>Wait for thumbnails to render. Large images and PDFs may take a moment to generate a cover plate.</li>
<li markdown>Click **Save to catalog**. New items land in your default collection, ready to tag.</li>
</ol>

<div class="plate-ph" data-label="screenshot · dropzone (drag state)"></div>

## Supported file types

Most creative formats are catalogued natively. Anything we can't preview is still stored
safely and shown with a generic plate.

| Kind | Extensions | Max size | Preview |
|---|---|---|---|
| **Image** | `jpg · png · webp · tiff` | 50 MB | Thumbnail auto-generated |
| **Vector** | `svg · ai · eps` | 25 MB | Rasterised at import |
| **Document** | `pdf · docx` | 25 MB | First page as cover plate |
| **Other** | `zip · any` | 200 MB | Stored as-is, generic plate |

## Add metadata

Good metadata is what makes a catalogue searchable later. Each item carries a **title**, an
auto-assigned `ID`, free-form **tags**, and an optional **note**. You can edit any field inline
from the item view, or in bulk from the catalogue grid.

!!! tip "Tip"

    Keep tag casing consistent. Search is case-insensitive, but tag chips render exactly as
    typed — `Mono` and `mono` will look like two different tags in a portfolio.

## Import metadata in bulk

Cataloguing a large archive? Upload the files first, then attach metadata from a CSV. Match rows
to items by `id`; any column you omit is left untouched.

```csv title="catalog-import.csv"
id,title,tags,note
CC-0184,"Aperture Study","print,mono","Test plate, archived"
CC-0185,"Grid No. 7","poster","Final, client-approved"
CC-0186,"Cyan Field","experiment,color",""
```

Then open **Bulk actions → Import metadata** and drop the file. A preview shows exactly which
items will change before you commit.

<div class="keys" markdown>
<kbd>⌘</kbd> <kbd>U</kbd> Upload &nbsp;·&nbsp; <kbd>/</kbd> Search catalogue &nbsp;·&nbsp; <kbd>⌘</kbd> <kbd>Enter</kbd> Save changes &nbsp;·&nbsp; <kbd>Esc</kbd> Close panel
</div>

## Troubleshooting

!!! warning "Upload stalls or fails"

    If a file hangs at "processing", it usually exceeds the size limit for its type or uses an
    unsupported codec. Re-export at a smaller size, or store it as a `zip` to keep it in the
    catalogue without a preview.

    Still stuck? Check your connection and retry — partial uploads resume automatically for up
    to 24 hours.
