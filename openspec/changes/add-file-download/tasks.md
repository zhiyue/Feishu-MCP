## 1. Schema Definitions

- [x] 1.1 Add `FileTokenSchema` to `src/types/feishuSchema.ts`
- [x] 1.2 Add `ExportFileExtensionSchema` for export format options (docx, pdf, xlsx, csv, png)
- [x] 1.3 Add `ExportDocTypeSchema` for source document types (docx, doc, sheet, bitable, mindnote)
- [x] 1.4 Add `ExportFileTokenSchema` and `ExportTicketSchema` for export task management

## 2. File Download Implementation

- [x] 2.1 Add `downloadFile` method to `src/services/feishuApiService.ts` - calls `/drive/v1/files/:file_token/download`
- [x] 2.2 Add `download_feishu_file` MCP tool to `src/mcp/tools/feishuFolderTools.ts`

## 3. Document Export Implementation

- [x] 3.1 Add `createExportTask` method - `POST /drive/v1/export_tasks`
- [x] 3.2 Add `getExportTaskResult` method - `GET /drive/v1/export_tasks/:ticket`
- [x] 3.3 Add `downloadExportFile` method - `GET /drive/v1/export_tasks/file/:file_token/download`
- [x] 3.4 Add `exportDocument` high-level method - combines create + poll + download
- [x] 3.5 Add `export_feishu_document` MCP tool

## 4. Testing

- [ ] 4.1 Manual test: Download an uploaded file
- [ ] 4.2 Manual test: Export a docx document to PDF
- [ ] 4.3 Manual test: Export a sheet to Excel
- [ ] 4.4 Verify error handling for invalid tokens and permissions
