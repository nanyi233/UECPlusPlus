# Day 01: AI Controller Basics

## Learning Objectives
- [ ] Understand AI Controller role
- [ ] Create custom AI Controller
- [ ] Implement basic AI movement
- [ ] Use AI perception system

## Core Concepts (20% that matters)

### 1. AI Controller Setup
```cpp
// AAIEnemyController.h
UCLASS()
class AAIEnemyController : public AAIController
{
    GENERATED_BODY()
    
public:
    AAIEnemyController();
    
    virtual void OnPossess(APawn* InPawn) override;
    virtual void OnUnPossess() override;
    
    // AI Perception
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly)
    UAIPerceptionComponent* AIPerceptionComponent;
    
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly)
    UAISenseConfig_Sight* SightConfig;
    
protected:
    virtual void BeginPlay() override;
    
    UFUNCTION()
    void OnTargetPerceptionUpdated(AActor* Actor, FAIStimulus Stimulus);
    
    UPROPERTY()
    AActor* CurrentTarget;
};

// AAIEnemyController.cpp
AAIEnemyController::AAIEnemyController()
{
    // Setup perception
    AIPerceptionComponent = CreateDefaultSubobject<UAIPerceptionComponent>(TEXT("Perception"));
    
    SightConfig = CreateDefaultSubobject<UAISenseConfig_Sight>(TEXT("SightConfig"));
    SightConfig->SightRadius = 1500.f;
    SightConfig->LoseSightRadius = 2000.f;
    SightConfig->PeripheralVisionAngleDegrees = 60.f;
    SightConfig->DetectionByAffiliation.bDetectEnemies = true;
    SightConfig->DetectionByAffiliation.bDetectNeutrals = true;
    SightConfig->DetectionByAffiliation.bDetectFriendlies = false;
    
    AIPerceptionComponent->ConfigureSense(*SightConfig);
    AIPerceptionComponent->SetDominantSense(SightConfig->GetSenseImplementation());
}

void AAIEnemyController::BeginPlay()
{
    Super::BeginPlay();
    
    AIPerceptionComponent->OnTargetPerceptionUpdated.AddDynamic(
        this, &AAIEnemyController::OnTargetPerceptionUpdated);
}
```

### 2. AI Perception Events
```cpp
void AAIEnemyController::OnTargetPerceptionUpdated(AActor* Actor, FAIStimulus Stimulus)
{
    if (Stimulus.WasSuccessfullySensed())
    {
        // Target spotted
        CurrentTarget = Actor;
        UE_LOG(LogTemp, Log, TEXT("Spotted: %s"), *Actor->GetName());
        
        // Start chasing
        MoveToActor(CurrentTarget, 100.f);
    }
    else
    {
        // Target lost
        if (CurrentTarget == Actor)
        {
            CurrentTarget = nullptr;
            StopMovement();
        }
    }
}
```

### 3. Basic AI Movement
```cpp
// Move to location
FAIMoveRequest MoveRequest;
MoveRequest.SetGoalLocation(TargetLocation);
MoveRequest.SetAcceptanceRadius(50.f);
MoveTo(MoveRequest);

// Move to actor
MoveToActor(TargetActor, AcceptanceRadius);

// Stop movement
StopMovement();

// Get movement status
EPathFollowingStatus::Type Status = GetMoveStatus();
```

### 4. Setting AI Controller for Pawn
```cpp
// In Enemy Actor
AEnemy::AEnemy()
{
    // Auto-possess by AI
    AutoPossessAI = EAutoPossessAI::PlacedInWorldOrSpawned;
    AIControllerClass = AAIEnemyController::StaticClass();
}
```

## Daily Task
1. Create AI Controller with perception
2. Configure sight sense
3. Implement chase behavior
4. Test AI detection

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
