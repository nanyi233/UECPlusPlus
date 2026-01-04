# Day 03: Player Character Integration

## Learning Objectives
- [ ] Build complete player character
- [ ] Integrate all systems
- [ ] Implement state machine
- [ ] Add polish features

## Core Concepts (20% that matters)

### 1. Integrated Player Character
```cpp
// AMyPlayerCharacter.h
UCLASS()
class AMyPlayerCharacter : public ACharacter, public IAbilitySystemInterface
{
    GENERATED_BODY()
    
public:
    AMyPlayerCharacter();
    
    // IAbilitySystemInterface
    virtual UAbilitySystemComponent* GetAbilitySystemComponent() const override;
    
protected:
    virtual void BeginPlay() override;
    virtual void SetupPlayerInputComponent(UInputComponent* Input) override;
    
    // Components
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Components")
    UCameraComponent* CameraComponent;
    
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Components")
    USpringArmComponent* SpringArm;
    
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Components")
    UAbilitySystemComponent* AbilitySystemComponent;
    
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Components")
    UMyAttributeSet* AttributeSet;
    
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Components")
    UInventoryComponent* InventoryComponent;
    
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Components")
    UInteractionComponent* InteractionComponent;
    
    // State
    UPROPERTY(BlueprintReadOnly, ReplicatedUsing = OnRep_CharacterState)
    ECharacterState CurrentState = ECharacterState::Idle;
    
    // Input
    UPROPERTY(EditDefaultsOnly, Category = "Input")
    UInputMappingContext* DefaultMappingContext;
    
    UPROPERTY(EditDefaultsOnly, Category = "Input")
    UInputAction* MoveAction;
    
    UPROPERTY(EditDefaultsOnly, Category = "Input")
    UInputAction* LookAction;
    
    UPROPERTY(EditDefaultsOnly, Category = "Input")
    UInputAction* JumpAction;
    
    UPROPERTY(EditDefaultsOnly, Category = "Input")
    UInputAction* InteractAction;
    
    UPROPERTY(EditDefaultsOnly, Category = "Input")
    UInputAction* AttackAction;
    
private:
    void Move(const FInputActionValue& Value);
    void Look(const FInputActionValue& Value);
    void TryInteract();
    void TryAttack();
    
    UFUNCTION()
    void OnRep_CharacterState();
    
    void SetState(ECharacterState NewState);
};
```

### 2. State Management
```cpp
UENUM(BlueprintType)
enum class ECharacterState : uint8
{
    Idle,
    Moving,
    Attacking,
    Interacting,
    Stunned,
    Dead
};

void AMyPlayerCharacter::SetState(ECharacterState NewState)
{
    if (CurrentState == NewState) return;
    
    // Exit current state
    switch (CurrentState)
    {
    case ECharacterState::Attacking:
        GetCharacterMovement()->SetMovementMode(MOVE_Walking);
        break;
    case ECharacterState::Stunned:
        // Re-enable input
        break;
    }
    
    CurrentState = NewState;
    
    // Enter new state
    switch (CurrentState)
    {
    case ECharacterState::Attacking:
        GetCharacterMovement()->StopMovementImmediately();
        break;
    case ECharacterState::Stunned:
        GetCharacterMovement()->StopMovementImmediately();
        // Disable input for duration
        break;
    case ECharacterState::Dead:
        DisableInput(Cast<APlayerController>(GetController()));
        GetCapsuleComponent()->SetCollisionEnabled(ECollisionEnabled::NoCollision);
        GetMesh()->SetSimulatePhysics(true);
        break;
    }
    
    OnStateChanged.Broadcast(CurrentState);
}
```

### 3. Damage Handling
```cpp
void AMyPlayerCharacter::HandleDamage(float DamageAmount, AActor* DamageSource)
{
    if (CurrentState == ECharacterState::Dead) return;
    
    // Apply through GAS
    FGameplayEffectContextHandle Context = AbilitySystemComponent->MakeEffectContext();
    Context.AddSourceObject(DamageSource);
    
    FGameplayEffectSpecHandle DamageSpec = AbilitySystemComponent->MakeOutgoingSpec(
        DamageEffectClass, 1, Context);
    
    DamageSpec.Data->SetSetByCallerMagnitude(
        FGameplayTag::RequestGameplayTag("Data.Damage"), DamageAmount);
    
    AbilitySystemComponent->ApplyGameplayEffectSpecToSelf(*DamageSpec.Data);
    
    // Visual feedback
    PlayHitReaction();
    Client_ShowDamageIndicator(DamageSource->GetActorLocation());
}

void AMyPlayerCharacter::OnHealthDepleted()
{
    SetState(ECharacterState::Dead);
    
    // Notify game mode
    AMyGameMode* GM = GetWorld()->GetAuthGameMode<AMyGameMode>();
    if (GM)
    {
        GM->OnPlayerDied(this);
    }
    
    // Broadcast event
    UEventManager* Events = GetGameInstance()->GetSubsystem<UEventManager>();
    Events->BroadcastPlayerDied(this);
}
```

### 4. Polish Features
```cpp
// Camera shake on damage
void AMyPlayerCharacter::PlayHitReaction()
{
    // Camera shake
    APlayerController* PC = Cast<APlayerController>(GetController());
    if (PC)
    {
        PC->PlayerCameraManager->StartCameraShake(HitCameraShakeClass);
    }
    
    // Controller vibration
    PC->PlayDynamicForceFeedback(0.5f, 0.2f, true, true, true, true);
    
    // Hit flash on character
    if (UMaterialInstanceDynamic* DynMaterial = GetMesh()->CreateDynamicMaterialInstance(0))
    {
        DynMaterial->SetScalarParameterValue(FName("HitFlash"), 1.0f);
        
        FTimerHandle ResetHandle;
        GetWorldTimerManager().SetTimer(ResetHandle, [DynMaterial]()
        {
            DynMaterial->SetScalarParameterValue(FName("HitFlash"), 0.0f);
        }, 0.1f, false);
    }
}
```

## Daily Task
1. Implement integrated character
2. Add state management
3. Connect all systems
4. Add polish features

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
