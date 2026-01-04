# Day 05: AI States & Combat

## Learning Objectives
- [ ] Implement AI state machine
- [ ] Create attack behaviors
- [ ] Handle AI damage response
- [ ] Implement death and respawn

## Core Concepts (20% that matters)

### 1. AI States Enum
```cpp
UENUM(BlueprintType)
enum class EAIState : uint8
{
    Idle,
    Patrol,
    Chase,
    Attack,
    Flee,
    Dead
};

// In AI Controller
UPROPERTY(VisibleAnywhere, BlueprintReadOnly)
EAIState CurrentState = EAIState::Idle;

void AAIEnemyController::SetState(EAIState NewState)
{
    if (CurrentState == NewState) return;
    
    ExitState(CurrentState);
    CurrentState = NewState;
    EnterState(NewState);
    
    // Update blackboard
    if (UBlackboardComponent* BB = GetBlackboardComponent())
    {
        BB->SetValueAsEnum(FName("AIState"), static_cast<uint8>(NewState));
    }
}
```

### 2. State Handlers
```cpp
void AAIEnemyController::EnterState(EAIState State)
{
    switch (State)
    {
    case EAIState::Idle:
        StopMovement();
        break;
        
    case EAIState::Patrol:
        // Start patrol behavior
        break;
        
    case EAIState::Chase:
        // Start chasing target
        if (CurrentTarget)
        {
            MoveToActor(CurrentTarget, AttackRange);
        }
        break;
        
    case EAIState::Attack:
        // Start attack sequence
        StopMovement();
        StartAttacking();
        break;
        
    case EAIState::Dead:
        StopMovement();
        if (BrainComponent)
        {
            BrainComponent->StopLogic(TEXT("Dead"));
        }
        break;
    }
}
```

### 3. Attack Implementation
```cpp
// Attack BT Task
EBTNodeResult::Type UBTTask_Attack::ExecuteTask(
    UBehaviorTreeComponent& OwnerComp, uint8* NodeMemory)
{
    AAIController* Controller = OwnerComp.GetAIOwner();
    AAIEnemy* Enemy = Cast<AAIEnemy>(Controller->GetPawn());
    if (!Enemy) return EBTNodeResult::Failed;
    
    // Check cooldown
    if (!Enemy->CanAttack())
    {
        return EBTNodeResult::Failed;
    }
    
    // Get target
    UBlackboardComponent* BB = OwnerComp.GetBlackboardComponent();
    AActor* Target = Cast<AActor>(BB->GetValueAsObject(TargetKey.SelectedKeyName));
    if (!Target) return EBTNodeResult::Failed;
    
    // Check range
    float Distance = FVector::Dist(Enemy->GetActorLocation(), Target->GetActorLocation());
    if (Distance > AttackRange)
    {
        return EBTNodeResult::Failed;
    }
    
    // Execute attack
    Enemy->PerformAttack(Target);
    
    return EBTNodeResult::Succeeded;
}
```

### 4. Damage Response
```cpp
// In Enemy class
void AAIEnemy::HandleDamage(float Damage, AActor* DamageDealer)
{
    Health -= Damage;
    
    // Update AI
    if (AAIEnemyController* AIController = Cast<AAIEnemyController>(GetController()))
    {
        // Set attacker as target
        AIController->SetTarget(DamageDealer);
        
        if (Health <= 0)
        {
            AIController->SetState(EAIState::Dead);
            HandleDeath();
        }
        else if (Health < MaxHealth * 0.2f)
        {
            // Low health - flee
            AIController->SetState(EAIState::Flee);
        }
    }
    
    // Play hit reaction
    PlayAnimMontage(HitReactionMontage);
}
```

### 5. Death Handling
```cpp
void AAIEnemy::HandleDeath()
{
    // Disable collision
    GetCapsuleComponent()->SetCollisionEnabled(ECollisionEnabled::NoCollision);
    
    // Play death animation
    if (DeathMontage)
    {
        PlayAnimMontage(DeathMontage);
    }
    
    // Ragdoll
    GetMesh()->SetSimulatePhysics(true);
    
    // Cleanup timer
    GetWorldTimerManager().SetTimer(DestroyTimerHandle, [this]()
    {
        Destroy();
    }, 5.f, false);
    
    // Notify game mode
    AMyGameMode* GM = Cast<AMyGameMode>(GetWorld()->GetAuthGameMode());
    if (GM)
    {
        GM->OnEnemyKilled(this, LastDamageDealer);
    }
}
```

## Daily Task
1. Implement state machine for AI
2. Create attack behavior
3. Add damage response
4. Implement death handling

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
