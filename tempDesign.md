    subgraph P[Project Creation (Level-wise)]
        direction TB
        L1["Level 1 → 20 parallel batches"]
        L2["Level 2 → 20 parallel batches"]
        L3["Level N → 20 parallel batches"]
        L1 --> L2 --> L3
    end

    subgraph F[Facility Creation]
        F1["20 parallel batches"]
    end

    subgraph U[User Bulk Creation (Promise.all 5 batches)]
        direction LR
        U1["Batch 1 (50 users)"]
        U2["Batch 2 (50 users)"]
        U3["Batch 3 (50 users)"]
        U4["Batch 4 (50 users)"]
        U5["Batch 5 (50 users)"]
    end

    subgraph M[Mappings]
        M1["50 parallel mapping batches"]
    end

    %% Flow connections
    P --> F --> U --> M
