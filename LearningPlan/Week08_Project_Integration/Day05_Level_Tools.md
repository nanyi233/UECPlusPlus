# Day 05: Level Design Tools

## Learning Objectives
- [ ] Create level loading system
- [ ] Build checkpoint system
- [ ] Implement spawn points
- [ ] Create trigger volumes

## Core Concepts (20% that matters)

### 1. Level Streaming
```cpp
// ULevelManager.h
UCLASS()
class ULevelManager : public UGameInstanceSubsystem
{
    GENERATED_BODY()
    
public:
    UFUNCTION(BlueprintCallable)
    void LoadLevel(FName LevelName, bool bShowLoadingScreen = true);
    
    UFUNCTION(BlueprintCallable)
    void StreamInSubLevel(FName SubLevelName);
    
    UFUNCTION(BlueprintCallable)
    void StreamOutSubLevel(FName SubLevelName);
    
    UPROPERTY(BlueprintAssignable)
    FOnLevelLoaded OnLevelLoaded;
    
private:
    void OnLevelLoadComplete();
    
    FName PendingLevelName;
};

// Implementation
void ULevelManager::LoadLevel(FName LevelName, bool bShowLoadingScreen)
{
    PendingLevelName = LevelName;
    
    if (bShowLoadingScreen)
    {
        UMyGameInstance::Get(this)->ShowLoadingScreen();
    }
    
    FLatentActionInfo LatentInfo;
    LatentInfo.CallbackTarget = this;
    LatentInfo.ExecutionFunction = FName("OnLevelLoadComplete");
    LatentInfo.Linkage = 0;
    LatentInfo.UUID = FMath::Rand();
    
    UGameplayStatics::OpenLevel(this, LevelName);
}

void ULevelManager::StreamInSubLevel(FName SubLevelName)
{
    FLatentActionInfo LatentInfo;
    UGameplayStatics::LoadStreamLevel(this, SubLevelName, true, false, LatentInfo);
}
```

### 2. Checkpoint System
```cpp
// ACheckpoint.h
UCLASS()
class ACheckpoint : public AActor
{
    GENERATED_BODY()
    
public:
    ACheckpoint();
    
    UPROPERTY(EditInstanceOnly, BlueprintReadOnly)
    FName CheckpointID;
    
    UPROPERTY(EditAnywhere, BlueprintReadOnly)
    bool bIsInitialSpawn = false;
    
protected:
    UPROPERTY(VisibleAnywhere)
    UBoxComponent* TriggerVolume;
    
    UPROPERTY(VisibleAnywhere)
    UArrowComponent* SpawnDirection;
    
    UFUNCTION()
    void OnTriggerOverlap(UPrimitiveComponent* OverlappedComp, AActor* OtherActor,
        UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep,
        const FHitResult& SweepResult);
};

// Implementation
void ACheckpoint::OnTriggerOverlap(...)
{
    AMyPlayerCharacter* Player = Cast<AMyPlayerCharacter>(OtherActor);
    if (!Player || !Player->IsLocallyControlled()) return;
    
    // Save checkpoint
    UMyGameInstance* GI = UMyGameInstance::Get(this);
    if (GI && GI->GetSaveData())
    {
        GI->GetSaveData()->CheckpointLocations.Add(
            GetWorld()->GetCurrentLevel()->GetPathName(),
            GetActorTransform());
        
        GI->SaveGame();
        
        // Notify player
        Player->ShowCheckpointSaved();
    }
}
```

### 3. Spawn System
```cpp
// USpawnManager.h
UCLASS()
class USpawnManager : public UWorldSubsystem
{
    GENERATED_BODY()
    
public:
    void RegisterSpawnPoint(APlayerStart* SpawnPoint);
    void UnregisterSpawnPoint(APlayerStart* SpawnPoint);
    
    UFUNCTION(BlueprintCallable)
    FTransform GetSpawnTransform(AController* ForPlayer);
    
    UFUNCTION(BlueprintCallable)
    void RespawnPlayer(AController* Player, float Delay = 0.f);
    
private:
    UPROPERTY()
    TArray<APlayerStart*> SpawnPoints;
    
    FTransform FindBestSpawnPoint(AController* ForPlayer);
};

// Implementation
FTransform USpawnManager::GetSpawnTransform(AController* ForPlayer)
{
    // Check for saved checkpoint first
    UMyGameInstance* GI = UMyGameInstance::Get(this);
    if (GI && GI->GetSaveData())
    {
        FTransform* SavedTransform = GI->GetSaveData()->CheckpointLocations.Find(
            GetWorld()->GetCurrentLevel()->GetPathName());
        
        if (SavedTransform)
        {
            return *SavedTransform;
        }
    }
    
    // Fall back to spawn points
    return FindBestSpawnPoint(ForPlayer);
}

void USpawnManager::RespawnPlayer(AController* Player, float Delay)
{
    FTimerHandle TimerHandle;
    GetWorld()->GetTimerManager().SetTimer(TimerHandle, [this, Player]()
    {
        AMyGameMode* GM = GetWorld()->GetAuthGameMode<AMyGameMode>();
        if (GM)
        {
            GM->RestartPlayer(Player);
        }
    }, Delay, false);
}
```

### 4. Trigger Volumes
```cpp
// ATriggerZone.h
UCLASS()
class ATriggerZone : public AActor
{
    GENERATED_BODY()
    
public:
    UPROPERTY(BlueprintAssignable)
    FOnTriggerActivated OnTriggered;
    
    UPROPERTY(EditAnywhere, Category = "Trigger")
    bool bTriggerOnce = true;
    
    UPROPERTY(EditAnywhere, Category = "Trigger")
    TArray<TSubclassOf<AActor>> TriggerActorClasses;
    
    UPROPERTY(EditAnywhere, Category = "Trigger|Events")
    bool bSpawnEnemies = false;
    
    UPROPERTY(EditAnywhere, Category = "Trigger|Events", meta = (EditCondition = "bSpawnEnemies"))
    TArray<FEnemySpawnInfo> EnemiesToSpawn;
    
protected:
    UPROPERTY(VisibleAnywhere)
    UBoxComponent* TriggerBox;
    
    bool bHasTriggered = false;
    
    UFUNCTION()
    void OnTriggerOverlap(UPrimitiveComponent* OverlappedComp, AActor* OtherActor,
        UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep,
        const FHitResult& SweepResult);
    
    void ExecuteTrigger(AActor* Triggerer);
};
```

## Daily Task
1. Implement level manager
2. Create checkpoint system
3. Build spawn manager
4. Add trigger volumes

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
