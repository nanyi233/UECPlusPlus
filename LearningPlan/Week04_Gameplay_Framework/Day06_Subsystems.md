# Day 06: Subsystems

## Learning Objectives
- [ ] Understand UE Subsystem types
- [ ] Create Game Instance Subsystem
- [ ] Create World Subsystem
- [ ] Implement global managers

## Core Concepts (20% that matters)

### 1. Subsystem Types
```
USubsystem
├── UGameInstanceSubsystem    - Per game instance, persists across levels
├── UWorldSubsystem           - Per world, recreated per level
├── ULocalPlayerSubsystem     - Per local player
└── UEngineSubsystem          - Engine lifetime
```

### 2. Game Instance Subsystem
```cpp
// UInventorySubsystem.h
UCLASS()
class UInventorySubsystem : public UGameInstanceSubsystem
{
    GENERATED_BODY()
    
public:
    // Lifecycle
    virtual void Initialize(FSubsystemCollectionBase& Collection) override;
    virtual void Deinitialize() override;
    virtual bool ShouldCreateSubsystem(UObject* Outer) const override { return true; }
    
    // Inventory functions
    UFUNCTION(BlueprintCallable)
    void AddItem(FName ItemID, int32 Count);
    
    UFUNCTION(BlueprintCallable)
    bool RemoveItem(FName ItemID, int32 Count);
    
    UFUNCTION(BlueprintPure)
    int32 GetItemCount(FName ItemID) const;
    
    UFUNCTION(BlueprintPure)
    TArray<FInventoryItem> GetAllItems() const;
    
private:
    UPROPERTY()
    TMap<FName, int32> Inventory;
};

// UInventorySubsystem.cpp
void UInventorySubsystem::Initialize(FSubsystemCollectionBase& Collection)
{
    Super::Initialize(Collection);
    UE_LOG(LogTemp, Log, TEXT("Inventory Subsystem Initialized"));
}
```

### 3. World Subsystem
```cpp
// UEnemyManagerSubsystem.h
UCLASS()
class UEnemyManagerSubsystem : public UWorldSubsystem
{
    GENERATED_BODY()
    
public:
    virtual void Initialize(FSubsystemCollectionBase& Collection) override;
    virtual void Deinitialize() override;
    
    // Tick support
    virtual bool DoesSupportWorldType(EWorldType::Type WorldType) const override;
    virtual void Tick(float DeltaTime) override;
    virtual TStatId GetStatId() const override;
    
    // Enemy management
    UFUNCTION(BlueprintCallable)
    void RegisterEnemy(AActor* Enemy);
    
    UFUNCTION(BlueprintCallable)
    void UnregisterEnemy(AActor* Enemy);
    
    UFUNCTION(BlueprintPure)
    int32 GetEnemyCount() const;
    
    UFUNCTION(BlueprintPure)
    AActor* GetClosestEnemy(FVector Location) const;
    
private:
    UPROPERTY()
    TArray<AActor*> RegisteredEnemies;
};

bool UEnemyManagerSubsystem::DoesSupportWorldType(EWorldType::Type WorldType) const
{
    return WorldType == EWorldType::Game || WorldType == EWorldType::PIE;
}
```

### 4. Accessing Subsystems
```cpp
// Game Instance Subsystem
UInventorySubsystem* Inventory = GetGameInstance()->GetSubsystem<UInventorySubsystem>();

// World Subsystem
UEnemyManagerSubsystem* EnemyManager = GetWorld()->GetSubsystem<UEnemyManagerSubsystem>();

// Local Player Subsystem
UMyLocalPlayerSubsystem* MySubsystem = ULocalPlayer::GetSubsystem<UMyLocalPlayerSubsystem>(PC->GetLocalPlayer());
```

## Daily Task
1. Create UInventorySubsystem (persists across levels)
2. Create UWaveManagerSubsystem (world lifetime)
3. Test level transitions with subsystems
4. Document subsystem lifecycles

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
