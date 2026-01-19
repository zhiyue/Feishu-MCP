## Context

The Feishu MCP server needs to support two distinct download scenarios:

### Scenario 1: Download Uploaded Files
Files uploaded to Feishu Drive (images, PDFs, archives, etc.) can be downloaded directly using:
- API: `GET /drive/v1/files/:file_token/download`
- Synchronous operation

### Scenario 2: Export Native Documents
Feishu native documents (online docs, sheets, etc.) must be exported first:
- Create export task → Poll for completion → Download exported file
- Asynchronous operation (typically completes in seconds)

### API Comparison

| Feature | File Download | Media Download | Document Export |
|---------|--------------|----------------|-----------------|
| API Endpoint | `/drive/v1/files/:file_token/download` | `/drive/v1/medias/:media_id/download` | `/drive/v1/export_tasks/*` |
| Use Case | Uploaded files | Document images | Native docs export |
| Token Source | File list's `token` | Image block's `image.token` | Document ID + format |
| Operation | Sync | Sync | Async (create→poll→download) |

## Goals / Non-Goals

**Goals:**
- Download files from Feishu Drive using file_token
- Export Feishu native documents to common formats (docx, pdf, xlsx, csv, png)
- Provide a simple high-level API that handles the async export workflow
- Return file data in Base64 format for MCP transport

**Non-Goals:**
- Streaming large files (MCP doesn't support streaming well)
- Batch export of multiple documents in one call
- Real-time progress reporting during export

## Decisions

**Decision 1: Provide both low-level and high-level export APIs**
- Low-level: `createExportTask`, `getExportTaskResult`, `downloadExportFile` for fine control
- High-level: `exportDocument` combines all steps with polling
- Rationale: High-level API is simpler for most use cases; low-level APIs allow custom polling strategies

**Decision 2: Default polling with timeout**
- Poll interval: 500ms
- Max polling time: 60 seconds
- Rationale: Most exports complete in 2-5 seconds; timeout prevents infinite loops

**Decision 3: Return Base64 encoded data**
- Matches existing pattern used by `get_feishu_image_resource`
- MCP tool responses are JSON-based, requiring base64 for binary data

**Decision 4: Auto-detect export format based on document type**
- If format not specified, use sensible defaults:
  - docx/doc → docx
  - sheet → xlsx
  - bitable → xlsx
  - mindnote → png

## Export Task Flow

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│ Create Export   │───▶│ Poll Status     │───▶│ Get file_token  │───▶│ Download File   │
│ Task            │    │ (until done)    │    │ from result     │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘    └─────────────────┘
POST /export_tasks     GET /export_tasks/:ticket                     GET /export_tasks/file/:token/download
Returns: ticket        Returns: job_status, file_token               Returns: binary file
```

## Supported Export Formats

| doc_type | file_extension options | Default |
|----------|----------------------|---------|
| docx | docx, pdf | docx |
| doc | docx, pdf | docx |
| sheet | xlsx, csv | xlsx |
| bitable | xlsx, csv | xlsx |
| mindnote | png, pdf | png |

## Risks / Trade-offs

**Risk: Export task timeout**
- Mitigation: 60-second timeout with clear error message
- Large documents may take longer; user can retry

**Risk: Exported file expires after 10 minutes**
- Mitigation: Download immediately after export completes
- Document this limitation in tool description

**Risk: Rate limiting on export API**
- Mitigation: Document that frequent exports may be rate-limited

## Open Questions

1. Should we expose the polling interval as a configurable parameter?
2. Should we add a "save to folder" option instead of returning Base64?
