# Pagespool API — GitHub Action

Seven open-source document engines behind one API key: Docling, MarkItDown, WeasyPrint, veraPDF, Gotenberg, OCRmyPDF, EPUBCheck.

Pagespool hosts seven open-source document engines under their own names: Docling, MarkItDown, WeasyPrint, veraPDF, Gotenberg, OCRmyPDF and EPUBCheck. Each engine has its own REST API, MCP server and web tool, and one API key works for all seven. Docling, veraPDF and Gotenberg answer on their own routes, so their clients work with a new base URL. The servers are in Germany.

Calls the [Pagespool API](https://pagespool.com) from a workflow: every operation, one step each. Jobs are waited for and their result is downloaded.

## Get an API key

[Create a key](https://pagespool.com/go/gh-action?to=/app/api-keys) and store it as the repository secret `PAGESPOOL_API_KEY`. The key is optional: without `api-key` the action runs on the anonymous tier, with lower limits.

## Usage

```yaml
on: push
jobs:
  pagespool:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7.0.1
      - uses: pagespool-com/pagespool-action@v1.0.1
        with:
          operation: get_me
          api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `operation` | yes |  | The API operation to call: get_me, get_job, list_jobs, epubcheck_check, ocrmypdf_submit, ocrmypdf_get_result, gotenberg_chromium_convert_html, gotenberg_chromium_convert_url, gotenberg_chromium_convert_markdown, gotenberg_chromium_screenshot_html, gotenberg_chromium_screenshot_url, gotenberg_chromium_screenshot_markdown, gotenberg_libreoffice_convert, gotenberg_pdf_pipe, gotenberg_pdf_info, gotenberg_pdf_merge, gotenberg_pdf_split, gotenberg_pdf_select, gotenberg_pdf_delete, gotenberg_pdf_insert, gotenberg_pdf_rotate, gotenberg_pdf_compose, gotenberg_pdf_render, gotenberg_pdf_stamp_text, gotenberg_pdf_stamp_image, gotenberg_pdf_stamp_pdf, gotenberg_pdf_metadata, gotenberg_pdf_set_metadata, gotenberg_pdf_bookmarks, gotenberg_pdf_set_bookmarks, gotenberg_pdf_attach, gotenberg_pdf_attachments, gotenberg_pdf_detach, gotenberg_pdf_extract_attachments, gotenberg_pdf_form, gotenberg_pdf_set_form, gotenberg_pdf_flatten_form, gotenberg_pdf_extract_text, gotenberg_pdf_redact, gotenberg_pdf_optimize, gotenberg_pdf_convert_colors, gotenberg_pdf_flatten_transparency, gotenberg_pdf_remediate, gotenberg_pdf_validate, gotenberg_pdf_autotag, gotenberg_pdf_encrypt, gotenberg_pdf_decrypt, gotenberg_pdf_sign, gotenberg_pdf_timestamp, gotenberg_health, gotenberg_version, verapdf_validate_auto, verapdf_validate_1a, verapdf_validate_1b, verapdf_validate_2a, verapdf_validate_2b, verapdf_validate_2u, verapdf_validate_3a, verapdf_validate_3b, verapdf_validate_3u, verapdf_validate_4, verapdf_validate_4e, verapdf_validate_4f, verapdf_validate_ua1, verapdf_validate_ua2, verapdf_validate_wt1a, verapdf_validate_wt1r, verapdf_info, verapdf_list_profiles, verapdf_get_profile, weasyprint_render, markitdown_convert, docling_convert_file, docling_convert_file_async, docling_convert_source, docling_convert_source_async, docling_status_poll, docling_result. |
| `file` | no |  | Path of the file to upload, for operations that take one. An operation that takes several files takes one path per line. |
| `json` | no |  | The request body as a JSON object, for operations that take one. |
| `query` | no |  | Path, query and form parameters, one key=value per line; repeat a key for a list. An operation with free form fields takes any further key as one. |
| `api-key` | no |  | Your Pagespool API key, from a secret. Optional: without one the anonymous tier's lower limits apply; a key raises them. Create one at https://pagespool.com/go/gh-action?to=/app/api-keys |
| `output` | no |  | Where to write the answer (default: a file in the runner's temp directory, named after the operation). |
| `fail-if` | no |  | A jq expression on a JSON answer; the step fails when it is true (e.g. `.valid == false`). |
| `base-url` | no | `https://api.pagespool.com` | The API base URL. |

## Outputs

| Output | Description |
|---|---|
| `status` | The HTTP status of the last API call. |
| `output` | The path of the file holding the answer. |

## Operations

### `get_me`

Your plan, remaining requests, and remaining credits — `GET /v1/me`.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: get_me
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `get_job`

Get one queued job by ID — `GET /v1/jobs/{job_id}`.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: get_job
    query: |
      job_id=<job_id>
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `list_jobs`

List your queued jobs, newest first — `GET /v1/jobs`.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: list_jobs
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `epubcheck_check`

Check an EPUB file with EPUBCheck — `POST /v1/epubcheck/check`.
Costs 2 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: epubcheck_check
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `ocrmypdf_submit`

OCR a scanned PDF or image, 2 credits per page — `POST /v1/ocrmypdf/jobs`.
Submits a job, polls `get_job` until it is done and downloads `ocrmypdf_get_result`.
Costs 2 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: ocrmypdf_submit
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `ocrmypdf_get_result`

Download the searchable PDF of an OCR job — `GET /v1/ocrmypdf/jobs/{job_id}/result`.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: ocrmypdf_get_result
    query: |
      job_id=<job_id>
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_chromium_convert_html`

Convert an HTML file to PDF with Chromium — `POST /v1/gotenberg/forms/chromium/convert/html`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_chromium_convert_html
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_chromium_convert_url`

Convert a web page to PDF with Chromium — `POST /v1/gotenberg/forms/chromium/convert/url`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_chromium_convert_url
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_chromium_convert_markdown`

Convert Markdown files to PDF with Chromium — `POST /v1/gotenberg/forms/chromium/convert/markdown`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_chromium_convert_markdown
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_chromium_screenshot_html`

Screenshot an HTML file with Chromium — `POST /v1/gotenberg/forms/chromium/screenshot/html`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_chromium_screenshot_html
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_chromium_screenshot_url`

Screenshot a web page with Chromium — `POST /v1/gotenberg/forms/chromium/screenshot/url`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_chromium_screenshot_url
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_chromium_screenshot_markdown`

Screenshot Markdown files with Chromium — `POST /v1/gotenberg/forms/chromium/screenshot/markdown`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_chromium_screenshot_markdown
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_libreoffice_convert`

Convert office documents to PDF with LibreOffice — `POST /v1/gotenberg/forms/libreoffice/convert`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_libreoffice_convert
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_pipe`

Run a list of stages on PDFs — `POST /v1/gotenberg/forms/pdf/pipe`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_pipe
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_info`

Read the page count and properties of PDFs — `POST /v1/gotenberg/forms/pdf/info`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_info
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_merge`

Merge PDFs — `POST /v1/gotenberg/forms/pdf/merge`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_merge
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_split`

Split PDFs — `POST /v1/gotenberg/forms/pdf/split`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_split
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_select`

Keep selected pages of PDFs — `POST /v1/gotenberg/forms/pdf/select`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_select
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_delete`

Delete pages of PDFs — `POST /v1/gotenberg/forms/pdf/delete`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_delete
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_insert`

Insert pages into PDFs — `POST /v1/gotenberg/forms/pdf/insert`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_insert
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_rotate`

Rotate the pages of PDFs — `POST /v1/gotenberg/forms/pdf/rotate`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_rotate
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_compose`

Compose pages of PDFs on new pages — `POST /v1/gotenberg/forms/pdf/compose`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_compose
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_render`

Render the pages of PDFs as images — `POST /v1/gotenberg/forms/pdf/render`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_render
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_stamp_text`

Stamp text on PDFs — `POST /v1/gotenberg/forms/pdf/stamp-text`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_stamp_text
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_stamp_image`

Stamp an image on PDFs — `POST /v1/gotenberg/forms/pdf/stamp-image`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_stamp_image
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_stamp_pdf`

Stamp a PDF page on PDFs — `POST /v1/gotenberg/forms/pdf/stamp-pdf`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_stamp_pdf
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_metadata`

Read the metadata of PDFs — `POST /v1/gotenberg/forms/pdf/metadata`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_metadata
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_set_metadata`

Write the metadata of PDFs — `POST /v1/gotenberg/forms/pdf/set-metadata`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_set_metadata
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_bookmarks`

Read the bookmarks of PDFs — `POST /v1/gotenberg/forms/pdf/bookmarks`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_bookmarks
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_set_bookmarks`

Write the bookmarks of PDFs — `POST /v1/gotenberg/forms/pdf/set-bookmarks`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_set_bookmarks
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_attach`

Attach files to PDFs — `POST /v1/gotenberg/forms/pdf/attach`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_attach
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_attachments`

List the attachments of PDFs — `POST /v1/gotenberg/forms/pdf/attachments`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_attachments
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_detach`

Remove an attachment from PDFs — `POST /v1/gotenberg/forms/pdf/detach`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_detach
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_extract_attachments`

Extract the attachments of PDFs — `POST /v1/gotenberg/forms/pdf/extract-attachments`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_extract_attachments
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_form`

List the form fields of PDFs — `POST /v1/gotenberg/forms/pdf/form`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_form
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_set_form`

Fill the form fields of PDFs — `POST /v1/gotenberg/forms/pdf/set-form`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_set_form
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_flatten_form`

Flatten the form fields of PDFs — `POST /v1/gotenberg/forms/pdf/flatten-form`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_flatten_form
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_extract_text`

Extract the text of PDFs — `POST /v1/gotenberg/forms/pdf/extract-text`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_extract_text
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_redact`

Redact content from PDFs — `POST /v1/gotenberg/forms/pdf/redact`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_redact
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_optimize`

Optimize PDFs — `POST /v1/gotenberg/forms/pdf/optimize`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_optimize
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_convert_colors`

Convert the colors of PDFs — `POST /v1/gotenberg/forms/pdf/convert-colors`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_convert_colors
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_flatten_transparency`

Flatten the transparency of PDFs — `POST /v1/gotenberg/forms/pdf/flatten-transparency`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_flatten_transparency
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_remediate`

Make PDFs conform to PDF/A or PDF/UA — `POST /v1/gotenberg/forms/pdf/remediate`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_remediate
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_validate`

Validate PDFs against PDF/A or PDF/UA — `POST /v1/gotenberg/forms/pdf/validate`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_validate
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_autotag`

Add structure tags to PDFs — `POST /v1/gotenberg/forms/pdf/autotag`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_autotag
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_encrypt`

Encrypt PDFs — `POST /v1/gotenberg/forms/pdf/encrypt`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_encrypt
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_decrypt`

Decrypt PDFs — `POST /v1/gotenberg/forms/pdf/decrypt`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_decrypt
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_sign`

Sign PDFs with a certificate — `POST /v1/gotenberg/forms/pdf/sign`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_sign
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_pdf_timestamp`

Timestamp PDFs — `POST /v1/gotenberg/forms/pdf/timestamp`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_pdf_timestamp
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_health`

Check that Gotenberg is up — `GET /v1/gotenberg/health`.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_health
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `gotenberg_version`

Show Gotenberg's version — `GET /v1/gotenberg/version`.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: gotenberg_version
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_auto`

Check a PDF against the PDF/A part it declares — `POST /v1/verapdf/api/validate/auto`.
Costs 2 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_auto
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_1a`

Check a PDF against PDF/A-1a — `POST /v1/verapdf/api/validate/1a`.
Costs 2 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_1a
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_1b`

Check a PDF against PDF/A-1b — `POST /v1/verapdf/api/validate/1b`.
Costs 2 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_1b
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_2a`

Check a PDF against PDF/A-2a — `POST /v1/verapdf/api/validate/2a`.
Costs 2 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_2a
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_2b`

Check a PDF against PDF/A-2b — `POST /v1/verapdf/api/validate/2b`.
Costs 2 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_2b
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_2u`

Check a PDF against PDF/A-2u — `POST /v1/verapdf/api/validate/2u`.
Costs 2 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_2u
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_3a`

Check a PDF against PDF/A-3a — `POST /v1/verapdf/api/validate/3a`.
Costs 2 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_3a
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_3b`

Check a PDF against PDF/A-3b — `POST /v1/verapdf/api/validate/3b`.
Costs 2 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_3b
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_3u`

Check a PDF against PDF/A-3u — `POST /v1/verapdf/api/validate/3u`.
Costs 2 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_3u
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_4`

Check a PDF against PDF/A-4 — `POST /v1/verapdf/api/validate/4`.
Costs 2 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_4
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_4e`

Check a PDF against PDF/A-4e — `POST /v1/verapdf/api/validate/4e`.
Costs 2 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_4e
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_4f`

Check a PDF against PDF/A-4f — `POST /v1/verapdf/api/validate/4f`.
Costs 2 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_4f
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_ua1`

Check a PDF against PDF/UA-1 — `POST /v1/verapdf/api/validate/ua1`.
Costs 10 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_ua1
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_ua2`

Check a PDF against PDF/UA-2 — `POST /v1/verapdf/api/validate/ua2`.
Costs 10 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_ua2
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_wt1a`

Check a PDF against WTPDF 1.0 Accessibility — `POST /v1/verapdf/api/validate/wt1a`.
Costs 10 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_wt1a
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_validate_wt1r`

Check a PDF against WTPDF 1.0 Reuse — `POST /v1/verapdf/api/validate/wt1r`.
Costs 10 credits.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_validate_wt1r
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_info`

Get the veraPDF server's environment — `GET /v1/verapdf/api/info`.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_info
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_list_profiles`

List veraPDF's validation profiles — `GET /v1/verapdf/api/profiles`.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_list_profiles
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `verapdf_get_profile`

Get one veraPDF validation profile — `GET /v1/verapdf/api/profiles/{profile_id}`.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: verapdf_get_profile
    query: |
      profile_id=<profile_id>
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `weasyprint_render`

Render an HTML document to a PDF — `POST /v1/weasyprint/render`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: weasyprint_render
    json: |
      {
        "filename": "invoice.pdf",
        "html": "<!doctype html>\n<style>\n@page { size: A4; margin: 22mm }\nbody { font: 11pt/1.5 sans-serif; color: #1c2024 }\ntable { width: 100%; border-collapse: collapse }\nth { text-align: left; border-bottom: 2px solid #1c2024 }\ntd { padding: 3mm 0; border-bottom: 1px solid #e3e6ea }\n</style>\n<h1>Invoice 2026-041</h1>\n<p>Northwind Studio · Due Sep 30, 2026</p>\n<table>\n  <thead><tr><th>Description</th><th>Qty</th><th>Amount</th></tr></thead>\n  <tbody>\n    <tr><td>Design retainer, September</td><td>1</td><td>1,800.00</td></tr>\n    <tr><td>Illustration, cover set</td><td>6</td><td>720.00</td></tr>\n  </tbody>\n</table>\n"
      }
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `markitdown_convert`

Convert a document to Markdown with MarkItDown — `POST /v1/markitdown/convert`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: markitdown_convert
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `docling_convert_file`

Convert an uploaded document, waiting up to 50 s (1 credit per PDF page, 10 per other file) — `POST /v1/docling/v1/convert/file`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: docling_convert_file
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `docling_convert_file_async`

Queue an uploaded document for conversion (1 credit per PDF page, 10 per other file) — `POST /v1/docling/v1/convert/file/async`.
Submits a job, polls `docling_status_poll` until it is done and downloads `docling_result`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: docling_convert_file_async
    file: path/to/file
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `docling_convert_source`

Convert a document from a URL or base64, waiting up to 50 s (1 credit per PDF page, 10 per other file) — `POST /v1/docling/v1/convert/source`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: docling_convert_source
    json: |
      {
        "sources": []
      }
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `docling_convert_source_async`

Queue a document from a URL or base64 for conversion (1 credit per PDF page, 10 per other file) — `POST /v1/docling/v1/convert/source/async`.
Submits a job, polls `docling_status_poll` until it is done and downloads `docling_result`.
Costs 1 credit.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: docling_convert_source_async
    json: |
      {
        "sources": []
      }
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `docling_status_poll`

Check a conversion — `GET /v1/docling/v1/status/poll/{task_id}`.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: docling_status_poll
    query: |
      task_id=<task_id>
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

### `docling_result`

Download a conversion's result — `GET /v1/docling/v1/result/{task_id}`.

```yaml
- uses: pagespool-com/pagespool-action@v1.0.1
  with:
    operation: docling_result
    query: |
      task_id=<task_id>
    api-key: ${{ secrets.PAGESPOOL_API_KEY }}
```

API reference: https://pagespool.com/docs · Base URL: `https://api.pagespool.com`

## Support

https://pagespool.com/support

This repository is generated from the live API; changes to its files are overwritten.
