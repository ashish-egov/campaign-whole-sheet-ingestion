Start((Start))

%% Project creation
subgraph ProjectCreation [Project Creation - Levels in Parallel]
    direction LR
    L1["Level 1 → 20 parallel batches"]
    L2["Level 2 → 20 parallel batches"]
    L3["Level 3 → 20 parallel batches"]
end

%% Facility creation
FacilityCreation["Facility Creation → 20 parallel batches"]

%% User creation simplified
UserCreation["User Bulk Creation → 5 batches with 50 users each"]

%% Merge point
Merge((Merge All))

%% Mappings
M1["Mappings → 50 parallel mapping batches"]

%% Connections
Start --> ProjectCreation --> Merge
Start --> FacilityCreation --> Merge
Start --> UserCreation --> Merge
Merge --> M1
