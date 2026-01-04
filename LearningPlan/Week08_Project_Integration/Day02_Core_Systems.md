# Day 02: Core Systems Implementation

## Learning Objectives
- [ ] Implement GameInstance
- [ ] Create save/load system
- [ ] Build event system
- [ ] Set up audio manager

## Core Concepts (20% that matters)

### 1. Game Instance
```cpp
// UMyGameInstance.h
UCLASS()
class UMyGameInstance : public UGameInstance
{
    GENERATED_BODY()
    
public:
    virtual void Init() override;
    
    // Global access
    UFUNCTION(BlueprintPure, meta = (WorldContext = "WorldContextObject"))
    static UMyGameInstance* Get(UObject* WorldContextObject);
    
    // Save System
    UFUNCTION(BlueprintCallable)
    void SaveGame();
    
    UFUNCTION(BlueprintCallable)
    void LoadGame();
    
    UFUNCTION(BlueprintPure)
    UMySaveGame* GetSaveData() const { return CurrentSaveData; }
    
    // Settings
    UPROPERTY(BlueprintReadOnly)
    UGameSettings* GameSettings;
    
    // Audio
    UPROPERTY(BlueprintReadOnly)
    UAudioManager* AudioManager;
    
private:
    UPROPERTY()
    UMySaveGame* CurrentSaveData;
};
```

### 2. Save Game System
```cpp
// UMySaveGame.h
UCLASS()
class UMySaveGame : public USaveGame
{
    GENERATED_BODY()
    
public:
    // Player progress
    UPROPERTY()
    int32 PlayerLevel = 1;
    
    UPROPERTY()
    int32 PlayerExperience = 0;
    
    UPROPERTY()
    TArray<FInventoryItem> Inventory;
    
    // World state
    UPROPERTY()
    TMap<FName, bool> CompletedQuests;
    
    UPROPERTY()
    TMap<FName, FTransform> CheckpointLocations;
    
    // Settings
    UPROPERTY()
    float MasterVolume = 1.0f;
    
    UPROPERTY()
    float SFXVolume = 1.0f;
    
    UPROPERTY()
    float MusicVolume = 1.0f;
};

// Implementation
void UMyGameInstance::SaveGame()
{
    if (!CurrentSaveData)
    {
        CurrentSaveData = Cast<UMySaveGame>(
            UGameplayStatics::CreateSaveGameObject(UMySaveGame::StaticClass()));
    }
    
    // Gather data from current game state
    // ...
    
    UGameplayStatics::AsyncSaveGameToSlot(CurrentSaveData, TEXT("SaveSlot1"), 0,
        FAsyncSaveGameToSlotDelegate::CreateLambda([](const FString& SlotName, int32 UserIndex, bool bSuccess)
        {
            if (bSuccess)
            {
                UE_LOG(LogTemp, Log, TEXT("Game saved successfully"));
            }
        }));
}

void UMyGameInstance::LoadGame()
{
    UGameplayStatics::AsyncLoadGameFromSlot(TEXT("SaveSlot1"), 0,
        FAsyncLoadGameFromSlotDelegate::CreateLambda([this](const FString& SlotName, int32 UserIndex, USaveGame* LoadedGame)
        {
            if (LoadedGame)
            {
                CurrentSaveData = Cast<UMySaveGame>(LoadedGame);
                OnGameLoaded.Broadcast();
            }
        }));
}
```

### 3. Event System
```cpp
// UEventManager.h
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnPlayerDied, ACharacter*, DeadPlayer);
DECLARE_DYNAMIC_MULTICAST_DELEGATE_TwoParams(FOnItemCollected, AActor*, Collector, FName, ItemID);
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnQuestCompleted, FName, QuestID);

UCLASS()
class UEventManager : public UGameInstanceSubsystem
{
    GENERATED_BODY()
    
public:
    // Events
    UPROPERTY(BlueprintAssignable)
    FOnPlayerDied OnPlayerDied;
    
    UPROPERTY(BlueprintAssignable)
    FOnItemCollected OnItemCollected;
    
    UPROPERTY(BlueprintAssignable)
    FOnQuestCompleted OnQuestCompleted;
    
    // Broadcast functions
    UFUNCTION(BlueprintCallable)
    void BroadcastPlayerDied(ACharacter* DeadPlayer);
    
    UFUNCTION(BlueprintCallable)
    void BroadcastItemCollected(AActor* Collector, FName ItemID);
};

// Usage
UEventManager* Events = GetGameInstance()->GetSubsystem<UEventManager>();
Events->OnPlayerDied.AddDynamic(this, &AMyActor::HandlePlayerDeath);
```

### 4. Audio Manager
```cpp
// UAudioManager.h
UCLASS()
class UAudioManager : public UObject
{
    GENERATED_BODY()
    
public:
    void Initialize();
    
    UFUNCTION(BlueprintCallable)
    void PlaySFX(USoundBase* Sound, FVector Location = FVector::ZeroVector);
    
    UFUNCTION(BlueprintCallable)
    void PlayMusic(USoundBase* Music, float FadeInDuration = 1.0f);
    
    UFUNCTION(BlueprintCallable)
    void StopMusic(float FadeOutDuration = 1.0f);
    
    UFUNCTION(BlueprintCallable)
    void SetMasterVolume(float Volume);
    
private:
    UPROPERTY()
    UAudioComponent* MusicComponent;
    
    UPROPERTY()
    USoundMix* MasterSoundMix;
    
    UPROPERTY()
    USoundClass* SFXSoundClass;
    
    UPROPERTY()
    USoundClass* MusicSoundClass;
};
```

## Daily Task
1. Implement GameInstance
2. Create save/load system
3. Build event manager
4. Test persistence

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
