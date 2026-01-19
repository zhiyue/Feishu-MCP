## ADDED Requirements

### Requirement: File Download Service Method

The system SHALL provide a `downloadFile` method in `FeishuApiService` that downloads files from Feishu Drive using the `/drive/v1/files/:file_token/download` API.

#### Scenario: Successful file download

- **WHEN** a valid file_token is provided
- **AND** the user has read permission for the file
- **THEN** the system returns the file content as a Buffer

#### Scenario: Invalid file token

- **WHEN** an invalid or non-existent file_token is provided
- **THEN** the system throws an error with a descriptive message

---

### Requirement: File Download MCP Tool

The system SHALL provide a `download_feishu_file` MCP tool that allows AI assistants to download uploaded files from Feishu Drive.

#### Scenario: Download file and return Base64 content

- **WHEN** the tool is called with a valid file_token
- **THEN** the system returns the file content as Base64 encoded string

#### Scenario: Tool description clarity

- **WHEN** an AI assistant reads the tool description
- **THEN** it understands that file_token can be obtained from `get_feishu_folder_files` tool

---

### Requirement: Export Task Creation

The system SHALL provide a `createExportTask` method that creates an export task for Feishu native documents using `POST /drive/v1/export_tasks`.

#### Scenario: Create export task for docx

- **WHEN** a valid document token and export format (docx/pdf) are provided
- **AND** the document type is docx or doc
- **THEN** the system returns a ticket for tracking the export task

#### Scenario: Create export task for sheet

- **WHEN** a valid document token and export format (xlsx/csv) are provided
- **AND** the document type is sheet or bitable
- **THEN** the system returns a ticket for tracking the export task

---

### Requirement: Export Task Status Query

The system SHALL provide a `getExportTaskResult` method that queries export task status using `GET /drive/v1/export_tasks/:ticket`.

#### Scenario: Query pending task

- **WHEN** the export task is still processing
- **THEN** the system returns job_status indicating processing state

#### Scenario: Query completed task

- **WHEN** the export task has completed successfully
- **THEN** the system returns job_status=0 and the file_token for download

---

### Requirement: Export File Download

The system SHALL provide a `downloadExportFile` method that downloads the exported file using `GET /drive/v1/export_tasks/file/:file_token/download`.

#### Scenario: Download exported file

- **WHEN** a valid export file_token is provided
- **AND** the file has not expired (within 10 minutes of export completion)
- **THEN** the system returns the file content as a Buffer

---

### Requirement: High-Level Export Method

The system SHALL provide an `exportDocument` method that combines task creation, polling, and download into a single operation.

#### Scenario: Export document with auto-polling

- **WHEN** a document token, type, and format are provided
- **THEN** the system creates the task, polls until completion, and returns the exported file content

#### Scenario: Export timeout

- **WHEN** the export task does not complete within the timeout period
- **THEN** the system throws an error indicating timeout

---

### Requirement: Document Export MCP Tool

The system SHALL provide an `export_feishu_document` MCP tool that exports Feishu native documents to common formats.

#### Scenario: Export docx to PDF

- **WHEN** the tool is called with a docx document ID and format "pdf"
- **THEN** the system exports the document and returns Base64 encoded PDF content

#### Scenario: Export sheet to Excel

- **WHEN** the tool is called with a sheet document ID and format "xlsx"
- **THEN** the system exports the spreadsheet and returns Base64 encoded Excel content

#### Scenario: Tool provides format guidance

- **WHEN** an AI assistant reads the tool description
- **THEN** it understands which formats are supported for each document type
