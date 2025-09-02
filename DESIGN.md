# Unified Microplan Workflow Design

## 1. Excel Template Generation
**Type:** `microplan-template-generate`  
**API:** `POST /v1/data/_generate`

```mermaid
sequenceDiagram
    participant Client
    participant ExcelIngestionService
    participant boundary-service
    participant localization-service
    participant mdms-service
    participant filestore-service
    
    Client->>ExcelIngestionService: POST /v1/data/_generate<br/>(type: microplan-template-generate)
    
    ExcelIngestionService->>boundary-service: Fetch Boundary Hierarchy
    boundary-service-->>ExcelIngestionService: Boundary Data
    
    ExcelIngestionService->>localization-service: Fetch Localized Labels
    localization-service-->>ExcelIngestionService: Localized Messages
    
    ExcelIngestionService->>mdms-service: Fetch Master Data
    mdms-service-->>ExcelIngestionService: MDMS Configuration
    
    Note over ExcelIngestionService: Generate Excel Template
    Note over ExcelIngestionService: Sheet 1: ReadMe Instructions
    Note over ExcelIngestionService: Sheet 2: Facility Data
    Note over ExcelIngestionService: Sheet 3: User Data  
    Note over ExcelIngestionService: Sheet 4: Target Data
    
    ExcelIngestionService->>filestore-service: Upload Excel Template
    filestore-service-->>ExcelIngestionService: FileStore ID
    
    ExcelIngestionService-->>Client: GenerateResourceResponse<br/>(with fileStoreId)
```

## 2. Validation Process
**Type:** `microplan-ingestion-validate`  
**API:** `POST /v1/data/_process`

```mermaid
sequenceDiagram
    participant Client
    participant ExcelIngestionService
    participant filestore-service
    participant mdms-service
    
    Client->>ExcelIngestionService: POST /v1/data/_process<br/>(type: microplan-ingestion-validate)
    
    ExcelIngestionService->>filestore-service: Download Excel File
    filestore-service-->>ExcelIngestionService: Excel File Data
    
    Note over ExcelIngestionService: Parse Excel Sheets
    Note over ExcelIngestionService: MDMS Schema Validation
    Note over ExcelIngestionService: Manual Validation
    Note over ExcelIngestionService: - User Existence Check
    Note over ExcelIngestionService: - Boundary Code Existence
    Note over ExcelIngestionService: - Boundary Code in Campaign Selected
    
    ExcelIngestionService->>filestore-service: Upload Processed File
    filestore-service-->>ExcelIngestionService: Processed FileStore ID
    
    ExcelIngestionService-->>Client: ProcessResponse<br/>(with processedFileStoreId)
```

## 3. Data Storage & Campaign Creation
**Type:** `microplan-ingestion`  
**API:** `POST /v1/data/_process`

```mermaid
sequenceDiagram
    participant Client
    participant ExcelIngestionService
    participant filestore-service
    participant CampaignDataTable
    participant CampaignProcessTable
    participant ProjectFactoryService
    
    Client->>ExcelIngestionService: POST /v1/data/_process<br/>(type: microplan-ingestion)
    
    ExcelIngestionService->>filestore-service: Download Processed File
    filestore-service-->>ExcelIngestionService: Processed File Data
    
    Note over ExcelIngestionService: Parse All Sheet Data
    
    ExcelIngestionService->>CampaignDataTable: Insert All Data (Temporary Storage)
    Note over CampaignDataTable: - Facility Data Rows
    Note over CampaignDataTable: - User Data Rows  
    Note over CampaignDataTable: - Target Data Rows
    Note over CampaignDataTable: Status: PENDING
    
    ExcelIngestionService->>CampaignProcessTable: Create 7 Processes
    Note over CampaignProcessTable: 1. Facility Create - PENDING
    Note over CampaignProcessTable: 2. User Create - PENDING
    Note over CampaignProcessTable: 3. Project Create - PENDING
    Note over CampaignProcessTable: 4. Facility Mapping - PENDING
    Note over CampaignProcessTable: 5. User Mapping - PENDING
    Note over CampaignProcessTable: 6. Resource Mapping - PENDING
    Note over CampaignProcessTable: 7. Credential Generation - PENDING
    
    Note over ExcelIngestionService: Data Storage Complete
    Note over ExcelIngestionService: All processes in PENDING state
    
    ExcelIngestionService->>ProjectFactoryService: POST /campaign/_create<br/>(Campaign Data + Process Config)
    
    Note over ProjectFactoryService: Project Factory handles all creation
    Note over ProjectFactoryService: - Facility Creation
    Note over ProjectFactoryService: - User Creation  
    Note over ProjectFactoryService: - Project Creation
    Note over ProjectFactoryService: - All Mappings
    Note over ProjectFactoryService: - Credential Generation
    Note over ProjectFactoryService: Updates tables as processes complete
    
    ProjectFactoryService-->>ExcelIngestionService: Campaign Creation Response
    
    ExcelIngestionService-->>Client: Campaign Created Successfully<br/>(Project Factory Processing Started)
```
