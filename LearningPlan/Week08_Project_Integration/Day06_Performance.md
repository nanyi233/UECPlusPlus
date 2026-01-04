# Day 06: Performance & Debugging

## Learning Objectives
- [ ] Profile game performance
- [ ] Optimize common bottlenecks
- [ ] Use debugging tools
- [ ] Implement logging strategy

## Core Concepts (20% that matters)

### 1. Stat Commands
```cpp
// Common stat commands
// stat fps        - Frame rate
// stat unit       - Frame time breakdown
// stat game       - Game thread time
// stat gpu        - GPU time
// stat memory     - Memory usage
// stat scenerendering - Rendering stats

// In-game stats display
void AMyHUD::DrawStats()
{
#if !UE_BUILD_SHIPPING
    float FPS = 1.0f / GetWorld()->GetDeltaSeconds();
    
    DrawText(FString::Printf(TEXT("FPS: %.1f"), FPS), 
        FColor::Green, 10, 10);
    
    // Memory
    FPlatformMemoryStats MemStats = FPlatformMemory::GetStats();
    DrawText(FString::Printf(TEXT("Used: %.2f MB"), 
        MemStats.UsedPhysical / (1024.f * 1024.f)), 
        FColor::Yellow, 10, 30);
#endif
}
```

### 2. Profiling Macros
```cpp
// Scope-based profiling
void AMyActor::ExpensiveFunction()
{
    SCOPE_CYCLE_COUNTER(STAT_MyExpensiveFunction);
    
    // ... expensive code
}

// Declare stat in header
DECLARE_CYCLE_STAT(TEXT("My Expensive Function"), STAT_MyExpensiveFunction, STATGROUP_Game);

// Quick timing
void AMyActor::TimedFunction()
{
    double StartTime = FPlatformTime::Seconds();
    
    // ... code to time
    
    double EndTime = FPlatformTime::Seconds();
    UE_LOG(LogTemp, Log, TEXT("Function took: %f ms"), (EndTime - StartTime) * 1000.0);
}
```

### 3. Common Optimizations
```cpp
// 1. Tick optimization
AMyActor::AMyActor()
{
    // Disable tick when not needed
    PrimaryActorTick.bCanEverTick = false;
    
    // Or reduce tick frequency
    PrimaryActorTick.TickInterval = 0.1f; // 10 times per second
}

// 2. Timer instead of Tick
void AMyActor::BeginPlay()
{
    Super::BeginPlay();
    
    // Use timer for periodic checks instead of Tick
    GetWorldTimerManager().SetTimer(CheckTimerHandle, this, 
        &AMyActor::PeriodicCheck, 0.5f, true);
}

// 3. Object pooling
TArray<AProjectile*> ProjectilePool;

AProjectile* AMyActor::GetPooledProjectile()
{
    for (AProjectile* Proj : ProjectilePool)
    {
        if (!Proj->IsActive())
        {
            Proj->Activate();
            return Proj;
        }
    }
    
    // Create new if pool empty
    AProjectile* NewProj = GetWorld()->SpawnActor<AProjectile>(ProjectileClass);
    ProjectilePool.Add(NewProj);
    return NewProj;
}

// 4. Async loading
void AMyActor::LoadAssetAsync()
{
    FStreamableManager& Streamable = UAssetManager::GetStreamableManager();
    
    Streamable.RequestAsyncLoad(AssetToLoad.ToSoftObjectPath(),
        FStreamableDelegate::CreateUObject(this, &AMyActor::OnAssetLoaded));
}
```

### 4. Custom Logging
```cpp
// Define log category
// In header
DECLARE_LOG_CATEGORY_EXTERN(LogMyGame, Log, All);
DECLARE_LOG_CATEGORY_EXTERN(LogCombat, Log, All);
DECLARE_LOG_CATEGORY_EXTERN(LogAI, Log, All);

// In cpp
DEFINE_LOG_CATEGORY(LogMyGame);
DEFINE_LOG_CATEGORY(LogCombat);
DEFINE_LOG_CATEGORY(LogAI);

// Usage
UE_LOG(LogCombat, Warning, TEXT("Player dealt %f damage to %s"), 
    Damage, *Target->GetName());

// Conditional logging
UE_CLOG(bDebugMode, LogMyGame, Log, TEXT("Debug info: %s"), *DebugString);

// Screen logging
#if !UE_BUILD_SHIPPING
GEngine->AddOnScreenDebugMessage(-1, 5.f, FColor::Yellow, 
    FString::Printf(TEXT("State: %s"), *StateString));
#endif
```

### 5. Visual Debugging
```cpp
void AMyActor::DebugDraw()
{
#if ENABLE_DRAW_DEBUG
    // Draw debug shapes
    DrawDebugSphere(GetWorld(), GetActorLocation(), 50.f, 12, FColor::Red, false, 0.f);
    
    DrawDebugLine(GetWorld(), Start, End, FColor::Green, false, 0.f, 0, 2.f);
    
    DrawDebugBox(GetWorld(), BoxCenter, BoxExtent, FColor::Blue, false, 0.f);
    
    // Draw debug string in world
    DrawDebugString(GetWorld(), GetActorLocation() + FVector(0, 0, 100),
        FString::Printf(TEXT("Health: %f"), Health), nullptr, FColor::White, 0.f);
#endif
}
```

## Daily Task
1. Profile your game
2. Identify bottlenecks
3. Implement optimizations
4. Set up logging strategy

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
