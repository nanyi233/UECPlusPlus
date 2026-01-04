# Day 06: Crowd & Group AI

## Learning Objectives
- [ ] Implement crowd manager
- [ ] Create group behaviors
- [ ] Use AI avoidance
- [ ] Implement formations

## Core Concepts (20% that matters)

### 1. Crowd Manager Setup
```cpp
// In Project Settings or via code
// Enable RVO Avoidance in CharacterMovementComponent
GetCharacterMovement()->bUseRVOAvoidance = true;
GetCharacterMovement()->AvoidanceConsiderationRadius = 500.f;
GetCharacterMovement()->AvoidanceWeight = 0.5f;

// Avoidance groups
GetCharacterMovement()->SetAvoidanceGroup(1);
GetCharacterMovement()->SetGroupsToAvoid(1);
GetCharacterMovement()->SetGroupsToIgnore(0);
```

### 2. Group AI Manager
```cpp
// UAIGroupManager.h
UCLASS()
class UAIGroupManager : public UWorldSubsystem
{
    GENERATED_BODY()
    
public:
    void RegisterAI(AAIController* Controller);
    void UnregisterAI(AAIController* Controller);
    
    // Group commands
    void CommandGroupMove(int32 GroupID, FVector Location);
    void CommandGroupAttack(int32 GroupID, AActor* Target);
    void SetFormation(int32 GroupID, EFormationType Formation);
    
    // Queries
    TArray<AAIController*> GetGroupMembers(int32 GroupID);
    AAIController* GetGroupLeader(int32 GroupID);
    
private:
    TMap<int32, TArray<AAIController*>> AIGroups;
    TMap<int32, AAIController*> GroupLeaders;
};
```

### 3. Formation System
```cpp
UENUM(BlueprintType)
enum class EFormationType : uint8
{
    Line,
    Column,
    Wedge,
    Circle
};

TArray<FVector> UAIGroupManager::GetFormationPositions(
    FVector LeaderLocation, FRotator LeaderRotation, 
    int32 MemberCount, EFormationType Formation)
{
    TArray<FVector> Positions;
    float Spacing = 150.f;
    
    switch (Formation)
    {
    case EFormationType::Line:
        for (int32 i = 0; i < MemberCount; i++)
        {
            FVector Offset = LeaderRotation.RotateVector(FVector(0, (i - MemberCount/2) * Spacing, 0));
            Positions.Add(LeaderLocation + Offset);
        }
        break;
        
    case EFormationType::Column:
        for (int32 i = 0; i < MemberCount; i++)
        {
            FVector Offset = LeaderRotation.RotateVector(FVector(-i * Spacing, 0, 0));
            Positions.Add(LeaderLocation + Offset);
        }
        break;
        
    case EFormationType::Wedge:
        for (int32 i = 0; i < MemberCount; i++)
        {
            int32 Row = (i + 1) / 2;
            int32 Side = (i % 2 == 0) ? -1 : 1;
            FVector Offset = LeaderRotation.RotateVector(
                FVector(-Row * Spacing, Side * Row * Spacing * 0.7f, 0));
            Positions.Add(LeaderLocation + Offset);
        }
        break;
        
    case EFormationType::Circle:
        for (int32 i = 0; i < MemberCount; i++)
        {
            float Angle = (2 * PI * i) / MemberCount;
            FVector Offset(FMath::Cos(Angle) * Spacing * 2, FMath::Sin(Angle) * Spacing * 2, 0);
            Positions.Add(LeaderLocation + Offset);
        }
        break;
    }
    
    return Positions;
}
```

### 4. Flanking Behavior
```cpp
// BT Task for flanking
EBTNodeResult::Type UBTTask_Flank::ExecuteTask(
    UBehaviorTreeComponent& OwnerComp, uint8* NodeMemory)
{
    AAIController* Controller = OwnerComp.GetAIOwner();
    UBlackboardComponent* BB = OwnerComp.GetBlackboardComponent();
    
    AActor* Target = Cast<AActor>(BB->GetValueAsObject(TargetKey.SelectedKeyName));
    if (!Target) return EBTNodeResult::Failed;
    
    APawn* Pawn = Controller->GetPawn();
    if (!Pawn) return EBTNodeResult::Failed;
    
    // Calculate flanking position
    FVector ToTarget = Target->GetActorLocation() - Pawn->GetActorLocation();
    FVector FlankDir = FVector::CrossProduct(ToTarget, FVector::UpVector).GetSafeNormal();
    
    // Randomly pick left or right flank
    if (FMath::RandBool())
    {
        FlankDir *= -1;
    }
    
    FVector FlankPosition = Target->GetActorLocation() + FlankDir * FlankDistance;
    
    Controller->MoveToLocation(FlankPosition);
    return EBTNodeResult::InProgress;
}
```

## Daily Task
1. Enable RVO avoidance
2. Create group manager
3. Implement formation system
4. Test group movement

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
