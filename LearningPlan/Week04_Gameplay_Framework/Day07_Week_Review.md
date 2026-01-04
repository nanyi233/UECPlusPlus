# Day 07: Week 4 Review & Mini Project

## Weekly Review Checklist
- [ ] GameMode/GameState understood
- [ ] PlayerController/PlayerState working
- [ ] GAS basics functional
- [ ] Attributes and Effects implemented
- [ ] Gameplay Tags configured
- [ ] Subsystems created

## Mini Project: Wave Survival Game Mode

### Requirements
Create a wave-based survival game mode with:
1. Custom GameMode managing waves
2. GameState tracking score and wave number
3. PlayerState with kills/deaths
4. EnemyManager Subsystem
5. Basic GAS for player abilities

### Architecture
```
AMyGameMode
├── Wave management logic
├── Enemy spawning
└── Score calculation

AMyGameState
├── CurrentWave (replicated)
├── TotalScore (replicated)
├── EnemiesRemaining
└── WaveTimer

AMyPlayerState
├── PlayerScore
├── Kills
└── Deaths

UEnemyManagerSubsystem
├── Spawn enemies
├── Track living enemies
└── Broadcast wave complete
```

### Core Implementation

```cpp
// Wave management
void AMyGameMode::StartNextWave()
{
    AMyGameState* GS = GetGameState<AMyGameState>();
    GS->CurrentWave++;
    
    int32 EnemyCount = CalculateEnemyCount(GS->CurrentWave);
    
    UEnemyManagerSubsystem* EnemyManager = 
        GetWorld()->GetSubsystem<UEnemyManagerSubsystem>();
    
    for (int32 i = 0; i < EnemyCount; i++)
    {
        FVector SpawnLocation = GetRandomSpawnPoint();
        AActor* Enemy = SpawnEnemy(SpawnLocation);
        EnemyManager->RegisterEnemy(Enemy);
    }
    
    GS->OnWaveStarted.Broadcast(GS->CurrentWave);
}

void AMyGameMode::OnEnemyKilled(AActor* Enemy, AController* Killer)
{
    if (AMyPlayerState* PS = Killer->GetPlayerState<AMyPlayerState>())
    {
        PS->AddScore(100);
        PS->RecordKill();
    }
    
    UEnemyManagerSubsystem* EnemyManager = 
        GetWorld()->GetSubsystem<UEnemyManagerSubsystem>();
    EnemyManager->UnregisterEnemy(Enemy);
    
    if (EnemyManager->GetEnemyCount() == 0)
    {
        StartNextWave();
    }
}
```

## Daily Task
1. Implement complete wave survival mode
2. Create all framework classes
3. Test wave progression
4. Write week summary

## Week 4 Summary
Write your key takeaways here:
1. 
2. 
3. 

## Completion Status
- [ ] Completed all objectives
- [ ] Mini project working
- [ ] Week 4 complete
