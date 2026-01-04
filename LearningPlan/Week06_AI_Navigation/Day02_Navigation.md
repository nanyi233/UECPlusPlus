# Day 02: Navigation System

## Learning Objectives
- [ ] Set up NavMesh
- [ ] Use pathfinding
- [ ] Implement NavMesh modifiers
- [ ] Handle dynamic obstacles

## Core Concepts (20% that matters)

### 1. NavMesh Setup (Editor)
```
1. Place NavMeshBoundsVolume in level
2. Scale to cover playable area
3. Build navigation (P key to visualize)
4. Configure Project Settings > Navigation System
```

### 2. C++ Pathfinding
```cpp
// Simple pathfinding
UNavigationSystemV1* NavSys = FNavigationSystem::GetCurrent<UNavigationSystemV1>(GetWorld());

if (NavSys)
{
    FPathFindingQuery Query;
    Query.StartLocation = GetPawn()->GetActorLocation();
    Query.EndLocation = TargetLocation;
    
    FPathFindingResult Result = NavSys->FindPathSync(Query);
    
    if (Result.IsSuccessful())
    {
        // Path found
        TArray<FNavPathPoint>& PathPoints = Result.Path->GetPathPoints();
    }
}

// Or simpler with AI Controller
MoveToLocation(TargetLocation);
MoveToActor(TargetActor);
```

### 3. Path Following Callbacks
```cpp
// In AI Controller
void AAIEnemyController::OnPossess(APawn* InPawn)
{
    Super::OnPossess(InPawn);
    
    // Get path following component
    UPathFollowingComponent* PathFollowing = GetPathFollowingComponent();
    if (PathFollowing)
    {
        PathFollowing->OnRequestFinished.AddUObject(
            this, &AAIEnemyController::OnMoveCompleted);
    }
}

void AAIEnemyController::OnMoveCompleted(FAIRequestID RequestID, 
    const FPathFollowingResult& Result)
{
    if (Result.IsSuccess())
    {
        UE_LOG(LogTemp, Log, TEXT("Reached destination"));
    }
    else if (Result.IsInterrupted())
    {
        UE_LOG(LogTemp, Log, TEXT("Movement interrupted"));
    }
    else
    {
        UE_LOG(LogTemp, Warning, TEXT("Movement failed"));
    }
}
```

### 4. NavMesh Query
```cpp
// Find random point in NavMesh
FNavLocation RandomLocation;
UNavigationSystemV1* NavSys = FNavigationSystem::GetCurrent<UNavigationSystemV1>(GetWorld());

if (NavSys && NavSys->GetRandomPointInNavigableRadius(
    GetPawn()->GetActorLocation(), 1000.f, RandomLocation))
{
    MoveToLocation(RandomLocation.Location);
}

// Project point to NavMesh
FNavLocation ProjectedLocation;
if (NavSys->ProjectPointToNavigation(WorldLocation, ProjectedLocation))
{
    // Point is valid on NavMesh
}
```

### 5. Nav Areas and Costs
```cpp
// Custom Nav Area
UCLASS()
class UNavArea_Water : public UNavArea
{
    GENERATED_BODY()
    
public:
    UNavArea_Water()
    {
        DefaultCost = 2.0f;  // More expensive to traverse
        FixedAreaEnteringCost = 10.f;
        DrawColor = FColor::Blue;
    }
};
```

## Daily Task
1. Set up NavMesh in test level
2. Implement pathfinding to player
3. Add path completion callbacks
4. Test with obstacles

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
