# Bug review: document

Repo: EssentialBlocks/document (public). Fork: suhaeel/document
Branch: `review/document-bugs` (no changes to code, main untouched)
What it is: "Document" embed block by EmbedPress (version 1.1.0). It embeds a PDF with PDFObject, and other Office files through Microsoft's online viewer. `php -l` is clean and `build/` is committed.

## Findings
| # | Severity | Where | What | Evidence |
|---|---|---|---|---|
| 1 | Medium | src/save.js:4 | `'//view.officeapps.live.com/op/embed.aspx?src=' + href` does not encode `href`. A file URL with `?` or `&` (for example a signed or CDN URL) is cut at the first `&`, and the viewer opens the wrong or no file. Should use `encodeURIComponent(href)`. Changing it changes the saved markup, so it needs a block deprecation. | From reading the code, not run |
| 2 | Medium | src/edit.js:106-108 | In `onSelectFile` the new `id` is saved with `setAttributes`, then `PDFObject.embed(media.url, "." + this.props.attributes.id)` runs straight away. `this.props.attributes.id` is still the old value, which is `undefined` for a new block, so the selector is `.undefined` and the PDF preview does not appear until the block is re-rendered. | From reading the code, not run, worth a quick check in the editor |
| 3 | Low | src/save.js:21-23 | The `<iframe>` has no `title` (accessibility), no `loading="lazy"` and no `sandbox`. | From reading the code, not run |
| 4 | Low | src/index.js:41 | `powered_by` defaults to `true`, so every embed carries "Powered By EmbedPress" until it is switched off. Product choice, not a bug. | From reading the code, not run |
| 5 | Info | document.php | The document's URL is sent to Microsoft's viewer, and the file must be publicly reachable. Worth a line in the readme. | From reading the code, not run |
| 6 | Low | document.php:62 | `register_block_type( 'block/document' )` uses the generic namespace `block/`, which can collide with other plugins. Renaming would break saved posts. | From reading the code, not run |

## Not covered
- Read the code only, unless a finding says "Verified". No automated tests exist in this repo.
- Chrome-only and WordPress 7.1.3 on Local where something was run.
- Last commit is 2022; I did not check whether this plugin is still shipped.
