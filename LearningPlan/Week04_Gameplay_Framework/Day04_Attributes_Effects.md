# Day 04: Attribute Set & Gameplay Effects

## Learning Objectives
- [ ] Create custom AttributeSet
- [ ] Implement attribute change callbacks
- [ ] Design Gameplay Effects
- [ ] Apply effects to modify attributes

## Core Concepts (20% that matters)

### 1. Custom Attribute Set
```cpp
// UMyAttributeSet.h
UCLASS()
class UMyAttributeSet : public UAttributeSet
{
    GENERATED_BODY()
    
public:
    UMyAttributeSet();
    
    // Health
    UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
    FGameplayAttributeData Health;
    ATTRIBUTE_ACCESSORS(UMyAttributeSet, Health)
    
    UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_MaxHealth)
    FGameplayAttributeData MaxHealth;
    ATTRIBUTE_ACCESSORS(UMyAttributeSet, MaxHealth)
    
    // Mana
    UPROPERTY(BlueprintReadOnly, Category = "Mana", ReplicatedUsing = OnRep_Mana)
    FGameplayAttributeData Mana;
    ATTRIBUTE_ACCESSORS(UMyAttributeSet, Mana)
    
    // Damage (meta attribute - not replicated)
    UPROPERTY(BlueprintReadOnly, Category = "Damage")
    FGameplayAttributeData Damage;
    ATTRIBUTE_ACCESSORS(UMyAttributeSet, Damage)
    
    // Callbacks
    virtual void PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue) override;
    virtual void PostGameplayEffectExecute(const FGameplayEffectModCallbackData& Data) override;
    
protected:
    UFUNCTION()
    void OnRep_Health(const FGameplayAttributeData& OldHealth);
    
    UFUNCTION()
    void OnRep_MaxHealth(const FGameplayAttributeData& OldMaxHealth);
    
    UFUNCTION()
    void OnRep_Mana(const FGameplayAttributeData& OldMana);
    
    virtual void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutProps) const override;
};

// UMyAttributeSet.cpp
void UMyAttributeSet::PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)
{
    Super::PreAttributeChange(Attribute, NewValue);
    
    // Clamp health to max
    if (Attribute == GetHealthAttribute())
    {
        NewValue = FMath::Clamp(NewValue, 0.f, GetMaxHealth());
    }
}

void UMyAttributeSet::PostGameplayEffectExecute(const FGameplayEffectModCallbackData& Data)
{
    Super::PostGameplayEffectExecute(Data);
    
    // Handle damage meta attribute
    if (Data.EvaluatedData.Attribute == GetDamageAttribute())
    {
        float DamageDone = GetDamage();
        SetDamage(0.f);  // Clear meta attribute
        
        if (DamageDone > 0.f)
        {
            float NewHealth = GetHealth() - DamageDone;
            SetHealth(FMath::Clamp(NewHealth, 0.f, GetMaxHealth()));
            
            // Check for death
            if (GetHealth() <= 0.f)
            {
                // Broadcast death event
            }
        }
    }
}
```

### 2. Gameplay Effect (Blueprint Data Asset)
Create in Editor: `GE_DamageEffect`
```
- Duration Policy: Instant
- Modifiers:
  - Attribute: MyAttributeSet.Damage
  - Modifier Op: Add
  - Magnitude: Scalable Float (50)
```

### 3. Applying Effects
```cpp
// Apply effect to target
void ApplyDamageEffect(UAbilitySystemComponent* TargetASC, float Damage)
{
    FGameplayEffectContextHandle Context = TargetASC->MakeEffectContext();
    Context.AddSourceObject(this);
    
    FGameplayEffectSpecHandle SpecHandle = TargetASC->MakeOutgoingSpec(
        DamageEffectClass, 1, Context);
    
    if (SpecHandle.IsValid())
    {
        // Set magnitude dynamically
        SpecHandle.Data->SetSetByCallerMagnitude(
            FGameplayTag::RequestGameplayTag("Data.Damage"), Damage);
        
        TargetASC->ApplyGameplayEffectSpecToSelf(*SpecHandle.Data);
    }
}
```

## Daily Task
1. Create attribute set with Health, Mana, Stamina
2. Create damage effect
3. Create heal effect
4. Test applying effects

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
