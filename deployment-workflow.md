# PwC Deployment & Release Workflow

```mermaid
flowchart TD
    %% Styling customized for PwC theme (Orange, Dark Grey, White)
    classDef default fill:#ffffff,stroke:#333333,stroke-width:1px,color:#333333;
    classDef highlight fill:#fdf1e9,stroke:#d04a02,stroke-width:2px,color:#000000;
    classDef decision fill:#fff8f5,stroke:#d04a02,stroke-width:2px,color:#000000,shape:diamond;
    classDef startEnd fill:#d04a02,stroke:#d04a02,stroke-width:2px,color:#ffffff,font-weight:bold;
    classDef db fill:#f9f9f9,stroke:#666666,stroke-width:2px,color:#333333,stroke-dasharray: 4 4;

    %% --- PHASE 1 ---
    subgraph Phase1 [PHASE 1: REQUIREMENTS & DEVELOPMENT]
        direction TB
        A1([Identify & document new business requirement]) ::: startEnd --> A2(Translate into formal Business Requirement Specification)
        A2 --> A3(Develop & configure ETL pipelines in DEV environment)
        A3 --> A4[(Load data from Excel/SharePoint to DMP database)] ::: db
        A4 --> A5(Develop & validate Qlik dashboard using DMP DB)
    end

    %% --- PHASE 2 ---
    subgraph Phase2 [PHASE 2: UAT & BUSINESS APPROVAL]
        direction TB
        B1(Promote ONLY Qlik dashboard to UAT environment<br/><i>*ETL processes must remain in DEV*</i>) ::: highlight
        B2(Present dashboard to Business Unit for review & testing)
        B3{Formal Business<br/>Approval?} ::: decision
        
        B1 --> B2 --> B3
    end

    %% --- PHASE 3 ---
    subgraph Phase3 [PHASE 3: PRODUCTION DEPLOYMENT]
        direction TB
        C1{Nature of<br/>Changes} ::: decision
        
        C1 -- "Scenario A:<br/>New Deployment" --> C2a(Deploy approved solution to Production)
        C1 -- "Scenario B:<br/>Modifying Existing Functionality" --> C2b(Deploy approved solution to Production)
        
        C2a --> C3a(Perform comprehensive data validation<br/>using BU provided data)
        C2b --> C3b(Perform comprehensive data validation<br/>using BU provided data)
        
        C3a --> C4a([Monitor dashboard & confirm execution]) ::: startEnd
        C3b --> C4b([Monitor dashboard & confirm execution]) ::: startEnd
    end

    %% --- BACKUP & VERSION CONTROL ---
    subgraph VC [BACKUP & VERSION CONTROL]
        direction TB
        V1(Commit all changes, enhancements & bug fixes) --> V2[(Centralized Git Repository)] ::: db
        V2 -.-> V3(Primary backup mechanism<br/>enables rollback & recovery)
    end

    %% --- CONNECTIONS ---
    A5 ===> B1
    B3 -- "Yes" ===> C1
    B3 -- "No / Revisions Needed" -.-> A3
    
    %% Connect Dev to Version Control
    Phase1 -. "Ongoing Source Code Commits" .-> V1
```

## Workflow Summary

- **Phase 1**: Requirements gathering and development in DEV environment
- **Phase 2**: UAT with dashboard promotion and business approval
- **Phase 3**: Production deployment with validation
- **Version Control**: Git repository as primary backup mechanism

Key constraint: ETL processes remain in DEV; only Qlik dashboard is promoted to UAT.
