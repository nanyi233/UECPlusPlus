# Day 05: Game State Synchronization

## Learning Objectives
- [ ] Sync game state across clients
- [ ] Implement match flow replication
- [ ] Handle player connections/disconnections
- [ ] Create scoreboard system

## Core Concepts (20% that matters)

### 1. GameState Replication
```cpp
// AMyGameState.h
UCLASS()
class AMyGameState : public AGameStateBase
{
    GENERATED_BODY()
    
public:
    // Replicated match data
    UPROPERTY(ReplicatedUsing = OnRep_MatchState)
    EMatchState MatchState = EMatchState::WaitingToStart;
    
    UPROPERTY(Replicated)
    float MatchTimeRemaining;
    
    UPROPERTY(ReplicatedUsing = OnRep_TeamScores)
    TArray<int32> TeamScores;
    
    // Server functions
    void SetMatchState(EMatchState NewState);
    void AddTeamScore(int32 TeamIndex, int32 Score);
    
protected:
    UFUNCTION()
    void OnRep_MatchState();
    
    UFUNCTION()
    void OnRep_TeamScores();
    
    virtual void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutProps) const override;
};

// Implementation
void AMyGameState::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutProps) const
{
    Super::GetLifetimeReplicatedProps(OutProps);
    
    DOREPLIFETIME(AMyGameState, MatchState);
    DOREPLIFETIME(AMyGameState, MatchTimeRemaining);
    DOREPLIFETIME(AMyGameState, TeamScores);
}

void AMyGameState::OnRep_MatchState()
{
    // Update UI on clients
    OnMatchStateChanged.Broadcast(MatchState);
}
```

### 2. PlayerState Replication
```cpp
// AMyPlayerState.h
UCLASS()
class AMyPlayerState : public APlayerState
{
    GENERATED_BODY()
    
public:
    UPROPERTY(Replicated, BlueprintReadOnly)
    int32 Kills = 0;
    
    UPROPERTY(Replicated, BlueprintReadOnly)
    int32 Deaths = 0;
    
    UPROPERTY(Replicated, BlueprintReadOnly)
    int32 TeamIndex = 0;
    
    UPROPERTY(ReplicatedUsing = OnRep_IsReady)
    bool bIsReady = false;
    
    // Server RPCs for client actions
    UFUNCTION(Server, Reliable)
    void Server_SetReady(bool bReady);
    
protected:
    UFUNCTION()
    void OnRep_IsReady();
    
    virtual void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutProps) const override;
};
```

### 3. Match Flow
```cpp
// In GameMode (server only)
void AMyGameMode::HandleMatchIsWaitingToStart()
{
    Super::HandleMatchIsWaitingToStart();
    
    // Check if all players ready
    bool bAllReady = true;
    for (APlayerState* PS : GameState->PlayerArray)
    {
        AMyPlayerState* MyPS = Cast<AMyPlayerState>(PS);
        if (MyPS && !MyPS->bIsReady)
        {
            bAllReady = false;
            break;
        }
    }
    
    if (bAllReady && GameState->PlayerArray.Num() >= MinPlayers)
    {
        StartMatch();
    }
}

void AMyGameMode::StartMatch()
{
    AMyGameState* GS = GetGameState<AMyGameState>();
    GS->SetMatchState(EMatchState::InProgress);
    
    // Start match timer
    GetWorldTimerManager().SetTimer(MatchTimerHandle, this, 
        &AMyGameMode::UpdateMatchTime, 1.f, true);
}
```

### 4. Connection Handling
```cpp
void AMyGameMode::PostLogin(APlayerController* NewPlayer)
{
    Super::PostLogin(NewPlayer);
    
    // Assign team
    AMyPlayerState* PS = NewPlayer->GetPlayerState<AMyPlayerState>();
    PS->TeamIndex = GetSmallestTeam();
    
    // Notify all players
    for (FConstPlayerControllerIterator It = GetWorld()->GetPlayerControllerIterator(); It; ++It)
    {
        AMyPlayerController* PC = Cast<AMyPlayerController>(It->Get());
        if (PC)
        {
            PC->Client_OnPlayerJoined(PS->GetPlayerName());
        }
    }
}

void AMyGameMode::Logout(AController* Exiting)
{
    AMyPlayerState* PS = Exiting->GetPlayerState<AMyPlayerState>();
    FString PlayerName = PS ? PS->GetPlayerName() : TEXT("Unknown");
    
    // Notify remaining players
    for (FConstPlayerControllerIterator It = GetWorld()->GetPlayerControllerIterator(); It; ++It)
    {
        AMyPlayerController* PC = Cast<AMyPlayerController>(It->Get());
        if (PC && PC != Exiting)
        {
            PC->Client_OnPlayerLeft(PlayerName);
        }
    }
    
    Super::Logout(Exiting);
}
```

## Daily Task
1. Implement GameState replication
2. Add PlayerState with stats
3. Create match flow system
4. Handle player join/leave

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
