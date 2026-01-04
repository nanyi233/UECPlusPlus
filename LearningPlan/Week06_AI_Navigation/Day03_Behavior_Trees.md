# Day 03: Behavior Trees

## Learning Objectives
- [ ] Understand Behavior Tree structure
- [ ] Create custom BT Tasks
- [ ] Implement BT Decorators
- [ ] Use Blackboard for AI memory

## Core Concepts (20% that matters)

### 1. Behavior Tree Components
```
Behavior Tree
├── Root
│   └── Selector (pick first successful)
│       ├── Sequence (attack if has target)
│       │   ├── Decorator: HasTarget
│       │   └── Task: Attack
│       └── Sequence (patrol otherwise)
│           └── Task: Patrol
            
Blackboard
├── TargetActor (Object)
├── PatrolLocation (Vector)
└── IsAlert (Bool)
```

### 2. Running Behavior Tree
```cpp
// In AI Controller
void AAIEnemyController::OnPossess(APawn* InPawn)
{
    Super::OnPossess(InPawn);
    
    if (BehaviorTree)
    {
        RunBehaviorTree(BehaviorTree);
        
        // Initialize blackboard
        UBlackboardComponent* BB = GetBlackboardComponent();
        if (BB)
        {
            BB->SetValueAsVector(FName("HomeLocation"), InPawn->GetActorLocation());
        }
    }
}

// Update blackboard from perception
void AAIEnemyController::OnTargetPerceptionUpdated(AActor* Actor, FAIStimulus Stimulus)
{
    UBlackboardComponent* BB = GetBlackboardComponent();
    if (BB)
    {
        if (Stimulus.WasSuccessfullySensed())
        {
            BB->SetValueAsObject(FName("TargetActor"), Actor);
        }
        else
        {
            BB->ClearValue(FName("TargetActor"));
        }
    }
}
```

### 3. Custom BT Task
```cpp
// UBTTask_FindPatrolLocation.h
UCLASS()
class UBTTask_FindPatrolLocation : public UBTTaskNode
{
    GENERATED_BODY()
    
public:
    UBTTask_FindPatrolLocation();
    
    virtual EBTNodeResult::Type ExecuteTask(UBehaviorTreeComponent& OwnerComp, 
        uint8* NodeMemory) override;
    
protected:
    UPROPERTY(EditAnywhere, Category = "Blackboard")
    FBlackboardKeySelector PatrolLocationKey;
    
    UPROPERTY(EditAnywhere, Category = "Patrol")
    float SearchRadius = 1000.f;
};

// Implementation
EBTNodeResult::Type UBTTask_FindPatrolLocation::ExecuteTask(
    UBehaviorTreeComponent& OwnerComp, uint8* NodeMemory)
{
    AAIController* Controller = OwnerComp.GetAIOwner();
    if (!Controller) return EBTNodeResult::Failed;
    
    APawn* Pawn = Controller->GetPawn();
    if (!Pawn) return EBTNodeResult::Failed;
    
    UNavigationSystemV1* NavSys = FNavigationSystem::GetCurrent<UNavigationSystemV1>(GetWorld());
    if (!NavSys) return EBTNodeResult::Failed;
    
    FNavLocation RandomLocation;
    if (NavSys->GetRandomPointInNavigableRadius(
        Pawn->GetActorLocation(), SearchRadius, RandomLocation))
    {
        OwnerComp.GetBlackboardComponent()->SetValueAsVector(
            PatrolLocationKey.SelectedKeyName, RandomLocation.Location);
        return EBTNodeResult::Succeeded;
    }
    
    return EBTNodeResult::Failed;
}
```

### 4. Custom BT Decorator
```cpp
// UBTDecorator_HasTarget.h
UCLASS()
class UBTDecorator_HasTarget : public UBTDecorator
{
    GENERATED_BODY()
    
public:
    UBTDecorator_HasTarget();
    
protected:
    virtual bool CalculateRawConditionValue(UBehaviorTreeComponent& OwnerComp, 
        uint8* NodeMemory) const override;
    
    UPROPERTY(EditAnywhere, Category = "Blackboard")
    FBlackboardKeySelector TargetKey;
};

// Implementation
bool UBTDecorator_HasTarget::CalculateRawConditionValue(
    UBehaviorTreeComponent& OwnerComp, uint8* NodeMemory) const
{
    UBlackboardComponent* BB = OwnerComp.GetBlackboardComponent();
    if (!BB) return false;
    
    UObject* Target = BB->GetValueAsObject(TargetKey.SelectedKeyName);
    return Target != nullptr;
}
```

### 5. Custom BT Service
```cpp
// UBTService_UpdateDistance.h - Runs periodically
UCLASS()
class UBTService_UpdateDistance : public UBTService
{
    GENERATED_BODY()
    
protected:
    virtual void TickNode(UBehaviorTreeComponent& OwnerComp, uint8* NodeMemory, 
        float DeltaSeconds) override;
    
    UPROPERTY(EditAnywhere)
    FBlackboardKeySelector TargetKey;
    
    UPROPERTY(EditAnywhere)
    FBlackboardKeySelector DistanceKey;
};
```

## Daily Task
1. Create behavior tree in editor
2. Implement FindPatrolLocation task
3. Create HasTarget decorator
4. Test patrol + chase behavior

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
