# Change: Add Feishu File Download and Export Support

## Why

Users need to:
1. **Download files** from Feishu Drive (uploaded files like images, PDFs, etc.) using `/drive/v1/files/:file_token/download`
2. **Export Feishu native documents** (online docs, sheets, bitable, mindnote) to common formats (Word, Excel, PDF, CSV, PNG) using the export task APIs

The current implementation only supports downloading media (images) from documents via `/drive/v1/medias/:media_id/download`, but does not support:
- Downloading general files from Feishu Drive
- Exporting Feishu native documents to downloadable formats

## What Changes

### Part 1: File Download (Simple Files)
- Add `downloadFile` method to `FeishuApiService` for downloading files using `/drive/v1/files/:file_token/download`
- Add `download_feishu_file` MCP tool for downloading uploaded files

### Part 2: Document Export (Native Documents)
- Add `createExportTask` method - Create export task using `POST /drive/v1/export_tasks`
- Add `getExportTaskResult` method - Query export status using `GET /drive/v1/export_tasks/:ticket`
- Add `downloadExportFile` method - Download exported file using `GET /drive/v1/export_tasks/file/:file_token/download`
- Add `exportDocument` high-level method - Combines create + poll + download into one operation
- Add `export_feishu_document` MCP tool for exporting native documents

### Supported Export Formats
| Document Type | Supported Formats |
|--------------|-------------------|
| docx (新版文档) | docx, pdf |
| doc (旧版文档) | docx, pdf |
| sheet (电子表格) | xlsx, csv |
| bitable (多维表格) | xlsx, csv |
| mindnote (思维笔记) | png, pdf |

## Impact

- Affected specs: New capability `file-download`
- Affected code:
  - `src/services/feishuApiService.ts` - Add download and export methods
  - `src/mcp/tools/feishuFolderTools.ts` - Add download and export tools
  - `src/types/feishuSchema.ts` - Add new schema definitions
