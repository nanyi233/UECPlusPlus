# Day 02: Player Controller & Player State

## Learning Objectives
- [ ] Understand Controller responsibilities
- [ ] Create custom PlayerController
- [ ] Implement PlayerState for player data
- [ ] Handle input and UI

## Core Concepts (20% that matters)

### 1. Controller Hierarchy
```
AController
├── APlayerController    - Human player control
│   └── AYourPC          - Your custom PC
└── AAIController        - AI control
```

### 2. Custom Player Controller
```cpp
// AMyPlayerController.h
UCLASS()
class AMyPlayerController : public APlayerController
{
    GENERATED_BODY()
    
public:
    virtual void BeginPlay() override;
    virtual void SetupInputComponent() override;
    virtual void OnPossess(APawn* InPawn) override;
    virtual void OnUnPossess() override;
    
    // UI Management
    UFUNCTION(BlueprintCallable)
    void ShowPauseMenu();
    
    UFUNCTION(BlueprintCallable)
    void ShowGameOverScreen(bool bWon);
    
    // Input modes
    UFUNCTION(BlueprintCallable)
    void SetUIInputMode();
    
    UFUNCTION(BlueprintCallable)
    void SetGameInputMode();
    
protected:
    UPROPERTY(EditDefaultsOnly, Category = "UI")
    TSubclassOf<UUserWidget> PauseMenuClass;
    
    UPROPERTY(EditDefaultsOnly, Category = "UI")
    TSubclassOf<UUserWidget> HUDClass;
    
private:
    UPROPERTY()
    UUserWidget* CurrentHUD;
    
    UPROPERTY()
    UUserWidget* PauseMenu;
};

// AMyPlayerController.cpp
void AMyPlayerController::SetUIInputMode()
{
    FInputModeUIOnly InputMode;
    InputMode.SetWidgetToFocus(PauseMenu->TakeWidget());
    InputMode.SetLockMouseToViewportBehavior(EMouseLockMode::DoNotLock);
    SetInputMode(InputMode);
    SetShowMouseCursor(true);
}

void AMyPlayerController::SetGameInputMode()
{
    FInputModeGameOnly InputMode;
    SetInputMode(InputMode);
    SetShowMouseCursor(false);
}
```

### 3. Custom Player State
```cpp
// AMyPlayerState.h
UCLASS()
class AMyPlayerState : public APlayerState
{
    GENERATED_BODY()
    
public:
    // Replicated player data
    UPROPERTY(ReplicatedUsing = OnRep_Score, BlueprintReadOnly)
    int32 PlayerScore;
    
    UPROPERTY(Replicated, BlueprintReadOnly)
    int32 Kills;
    
    UPROPERTY(Replicated, BlueprintReadOnly)
    int32 Deaths;
    
    UPROPERTY(BlueprintAssignable)
    FOnPlayerScoreChanged OnScoreChanged;
    
    UFUNCTION(BlueprintCallable)
    void AddScore(int32 Amount);
    
    UFUNCTION(BlueprintCallable)
    void RecordKill();
    
    UFUNCTION(BlueprintCallable)
    void RecordDeath();
    
protected:
    UFUNCTION()
    void OnRep_Score();
    
    virtual void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutProps) const override;
};
```

### 4. Accessing Player Data
```cpp
// Get PlayerController
AMyPlayerController* PC = Cast<AMyPlayerController>(GetController());

// Get PlayerState from Controller
AMyPlayerState* PS = PC->GetPlayerState<AMyPlayerState>();

// Get PlayerState from Pawn
AMyPlayerState* PS = GetPlayerState<AMyPlayerState>();

// Get all PlayerStates
for (APlayerState* PS : GetWorld()->GetGameState()->PlayerArray)
{
    AMyPlayerState* MyPS = Cast<AMyPlayerState>(PS);
}
```

## Daily Task
1. Create custom PlayerController
2. Create custom PlayerState
3. Implement score/stats tracking
4. Add HUD creation on possess

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
