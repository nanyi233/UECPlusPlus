# Day 04: Environment Query System (EQS)

## Learning Objectives
- [ ] Understand EQS architecture
- [ ] Create custom EQS queries
- [ ] Implement context providers
- [ ] Use EQS for tactical positioning

## Core Concepts (20% that matters)

### 1. EQS Components
```
Environment Query
├── Generator (creates test points)
│   └── Points: Grid, Circle, PathGrid
│   └── Actors: All Actors of Class
└── Tests (scores points)
    └── Distance, Trace, Dot Product, etc.
```

### 2. Running EQS Query
```cpp
// In AI Controller or BT Task
void AAIEnemyController::FindCoverPosition()
{
    UEnvQueryManager* EQSManager = UEnvQueryManager::GetCurrent(GetWorld());
    if (EQSManager && CoverQuery)
    {
        FEnvQueryRequest QueryRequest(CoverQuery, GetPawn());
        QueryRequest.Execute(EEnvQueryRunMode::SingleResult,
            FQueryFinishedSignature::CreateUObject(this, &AAIEnemyController::OnCoverQueryFinished));
    }
}

void AAIEnemyController::OnCoverQueryFinished(TSharedPtr<FEnvQueryResult> Result)
{
    if (Result->IsSuccessful())
    {
        FVector BestLocation = Result->GetItemAsLocation(0);
        MoveToLocation(BestLocation);
    }
}
```

### 3. Custom EQS Generator
```cpp
// UEnvQueryGenerator_CoverPoints.h
UCLASS()
class UEnvQueryGenerator_CoverPoints : public UEnvQueryGenerator
{
    GENERATED_BODY()
    
public:
    UEnvQueryGenerator_CoverPoints();
    
    virtual void GenerateItems(FEnvQueryInstance& QueryInstance) const override;
    
protected:
    UPROPERTY(EditDefaultsOnly, Category = "Generator")
    float SearchRadius = 1000.f;
    
    UPROPERTY(EditDefaultsOnly, Category = "Generator")
    float PointSpacing = 100.f;
};

// Implementation
void UEnvQueryGenerator_CoverPoints::GenerateItems(FEnvQueryInstance& QueryInstance) const
{
    UObject* QueryOwner = QueryInstance.Owner.Get();
    AActor* OwnerActor = Cast<AActor>(QueryOwner);
    if (!OwnerActor) return;
    
    FVector Origin = OwnerActor->GetActorLocation();
    
    // Generate grid of points
    TArray<FNavLocation> CoverPoints;
    UNavigationSystemV1* NavSys = FNavigationSystem::GetCurrent<UNavigationSystemV1>(
        OwnerActor->GetWorld());
    
    for (float X = -SearchRadius; X <= SearchRadius; X += PointSpacing)
    {
        for (float Y = -SearchRadius; Y <= SearchRadius; Y += PointSpacing)
        {
            FVector TestPoint = Origin + FVector(X, Y, 0.f);
            FNavLocation NavLoc;
            if (NavSys->ProjectPointToNavigation(TestPoint, NavLoc))
            {
                QueryInstance.AddItemData<UEnvQueryItemType_Point>(NavLoc.Location);
            }
        }
    }
}
```

### 4. Custom EQS Test
```cpp
// UEnvQueryTest_InCover.h
UCLASS()
class UEnvQueryTest_InCover : public UEnvQueryTest
{
    GENERATED_BODY()
    
public:
    UEnvQueryTest_InCover();
    
    virtual void RunTest(FEnvQueryInstance& QueryInstance) const override;
    
protected:
    UPROPERTY(EditDefaultsOnly, Category = "Test")
    FEnvTraceData TraceData;
};

// Implementation
void UEnvQueryTest_InCover::RunTest(FEnvQueryInstance& QueryInstance) const
{
    UObject* QueryOwner = QueryInstance.Owner.Get();
    AActor* OwnerActor = Cast<AActor>(QueryOwner);
    if (!OwnerActor) return;
    
    // Get threat location from blackboard
    FVector ThreatLocation = /* get from context */;
    
    for (FEnvQueryInstance::ItemIterator It(this, QueryInstance); It; ++It)
    {
        FVector ItemLocation = GetItemLocation(QueryInstance, It.GetIndex());
        
        // Check if point has cover from threat
        FHitResult Hit;
        bool bHasCover = OwnerActor->GetWorld()->LineTraceSingleByChannel(
            Hit, ItemLocation + FVector(0, 0, 50), ThreatLocation,
            ECC_Visibility);
        
        It.SetScore(TestPurpose, FilterType, bHasCover, true);
    }
}
```

## Daily Task
1. Create cover-finding EQS query
2. Implement custom generator
3. Create distance-based test
4. Integrate with behavior tree

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
