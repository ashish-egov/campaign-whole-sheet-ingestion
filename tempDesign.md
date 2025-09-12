```mermaid
flowchart TD

    Start((Start))

    %% Project creation
    subgraph ProjectCreation [Project Creation - Levels in Parallel]
        direction LR
        L1["Level 1 → 20 parallel batches"] Promise all
        L2["Level 2 → 20 parallel batches"] Promise all
        L3["Level 3 → 20 parallel batches"] Promise all
    end

    %% Facility creation
    FacilityCreation["Facility Creation → 20 parallel batches"] Promise all

    %% User creation simplified
    UserCreation["User Bulk Creation → 5 batches with 50 users each"] Promise all

    %% Merge point
    Merge((Promise All))

    %% Mappings
    M1["Mappings → 50 parallel mapping batches"] Promise all

    %% Connections
    Start --> ProjectCreation --> Merge
    Start --> FacilityCreation --> Merge
    Start --> UserCreation --> Merge
    Merge --> M1
```
