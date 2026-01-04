# Day 03: Gameplay Ability System Introduction

## Learning Objectives
- [ ] Understand GAS architecture
- [ ] Set up Ability System Component
- [ ] Create basic Gameplay Ability
- [ ] Implement ability activation

## Core Concepts (20% that matters)

### 1. GAS Components
```
UAbilitySystemComponent (ASC)  - Manages abilities and attributes
UGameplayAbility               - Ability logic
UAttributeSet                  - Character stats
FGameplayTag                   - Hierarchical labels
FGameplayEffect                - Modify attributes
```

### 2. Module Setup
```csharp
// Build.cs
PublicDependencyModuleNames.AddRange(new string[] { 
    "GameplayAbilities", 
    "GameplayTags", 
    "GameplayTasks" 
});
```

### 3. Ability System Component Setup
```cpp
// AMyCharacter.h
UCLASS()
class AMyCharacter : public ACharacter, public IAbilitySystemInterface
{
    GENERATED_BODY()
    
public:
    // IAbilitySystemInterface
    virtual UAbilitySystemComponent* GetAbilitySystemComponent() const override;
    
protected:
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Abilities")
    UAbilitySystemComponent* AbilitySystemComponent;
    
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Abilities")
    UMyAttributeSet* AttributeSet;
    
    // Abilities to grant on spawn
    UPROPERTY(EditDefaultsOnly, Category = "Abilities")
    TArray<TSubclassOf<UGameplayAbility>> DefaultAbilities;
    
    virtual void BeginPlay() override;
    void InitializeAbilities();
};

// AMyCharacter.cpp
void AMyCharacter::BeginPlay()
{
    Super::BeginPlay();
    InitializeAbilities();
}

void AMyCharacter::InitializeAbilities()
{
    if (AbilitySystemComponent)
    {
        for (TSubclassOf<UGameplayAbility>& Ability : DefaultAbilities)
        {
            AbilitySystemComponent->GiveAbility(
                FGameplayAbilitySpec(Ability, 1, INDEX_NONE, this));
        }
    }
}
```

### 4. Simple Gameplay Ability
```cpp
// UGA_Jump.h
UCLASS()
class UGA_Jump : public UGameplayAbility
{
    GENERATED_BODY()
    
public:
    UGA_Jump();
    
    virtual void ActivateAbility(const FGameplayAbilitySpecHandle Handle,
        const FGameplayAbilityActorInfo* ActorInfo,
        const FGameplayAbilityActivationInfo ActivationInfo,
        const FGameplayEventData* TriggerEventData) override;
    
    virtual bool CanActivateAbility(const FGameplayAbilitySpecHandle Handle,
        const FGameplayAbilityActorInfo* ActorInfo,
        const FGameplayTagContainer* SourceTags,
        const FGameplayTagContainer* TargetTags,
        FGameplayTagContainer* OptionalRelevantTags) const override;
};

// UGA_Jump.cpp
void UGA_Jump::ActivateAbility(...)
{
    if (ACharacter* Character = Cast<ACharacter>(GetAvatarActorFromActorInfo()))
    {
        Character->Jump();
    }
    
    EndAbility(Handle, ActorInfo, ActivationInfo, true, false);
}
```

## Daily Task
1. Add GAS modules to project
2. Create ASC on Character
3. Create simple ability (Jump/Dash)
4. Test ability activation

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
