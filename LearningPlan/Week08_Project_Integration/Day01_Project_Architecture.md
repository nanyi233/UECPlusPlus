# Day 01: Project Architecture

## Learning Objectives
- [ ] Design game architecture
- [ ] Plan class hierarchy
- [ ] Create project structure
- [ ] Define coding standards

## Core Concepts (20% that matters)

### 1. Project Structure
```
Source/MyGame/
├── Core/                    # Base classes, interfaces
│   ├── MyGameMode.h/cpp
│   ├── MyGameState.h/cpp
│   ├── MyGameInstance.h/cpp
│   └── Interfaces/
│
├── Characters/              # Player and NPC characters
│   ├── Player/
│   │   ├── MyCharacter.h/cpp
│   │   └── MyPlayerController.h/cpp
│   └── AI/
│       ├── AIEnemy.h/cpp
│       └── AIController.h/cpp
│
├── Components/              # Reusable components
│   ├── HealthComponent.h/cpp
│   ├── InventoryComponent.h/cpp
│   └── InteractionComponent.h/cpp
│
├── Abilities/               # GAS related
│   ├── Abilities/
│   ├── Effects/
│   └── Attributes/
│
├── UI/                      # UMG widgets
│   ├── HUD/
│   ├── Menus/
│   └── Common/
│
├── Items/                   # Items and pickups
│   ├── Weapons/
│   └── Pickups/
│
└── Data/                    # Data assets
    └── DataAssets/
```

### 2. Class Hierarchy Design
```
Game Framework:
AGameModeBase → AMyGameMode
AGameStateBase → AMyGameState
UGameInstance → AMyGameInstance

Character System:
ACharacter → AMyCharacterBase
    → AMyPlayerCharacter
    → AMyAICharacter

Controller:
APlayerController → AMyPlayerController
AAIController → AMyAIController

Components (Composition over Inheritance):
UActorComponent
    → UHealthComponent
    → UInventoryComponent
    → UCombatComponent
```

### 3. Module Dependencies
```csharp
// MyGame.Build.cs
PublicDependencyModuleNames.AddRange(new string[] { 
    "Core", 
    "CoreUObject", 
    "Engine", 
    "InputCore",
    "EnhancedInput",
    "UMG",
    "Slate",
    "SlateCore",
    "GameplayAbilities",
    "GameplayTags",
    "GameplayTasks",
    "AIModule",
    "NavigationSystem"
});
```

### 4. Coding Standards
```cpp
// Naming Conventions
class AMyActor;           // A prefix for Actors
class UMyComponent;       // U prefix for UObjects
class FMyStruct;          // F prefix for structs
class IMyInterface;       // I prefix for interfaces
class EMyEnum;            // E prefix for enums

// File Organization
// Header
#pragma once
#include "CoreMinimal.h"
#include "MyActor.generated.h"

// Forward declarations over includes when possible
class UHealthComponent;

UCLASS()
class MYGAME_API AMyActor : public AActor
{
    GENERATED_BODY()
    
public:
    // Constructors
    AMyActor();
    
    // Public interface
    
protected:
    // Overrides
    virtual void BeginPlay() override;
    
    // Components (protected for BP access)
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Components")
    UHealthComponent* HealthComponent;
    
private:
    // Private implementation
};
```

### 5. Project Configuration
```ini
// DefaultEngine.ini
[/Script/Engine.CollisionProfile]
+DefaultChannelResponses=(Channel=ECC_GameTraceChannel1,DefaultResponse=ECR_Ignore,bTraceType=True,bStaticObject=False,Name="Weapon")
+DefaultChannelResponses=(Channel=ECC_GameTraceChannel2,DefaultResponse=ECR_Overlap,bTraceType=False,bStaticObject=False,Name="Interaction")

[/Script/Engine.PhysicsSettings]
DefaultGravityZ=-980.0

// DefaultGame.ini
[/Script/EngineSettings.GameMapsSettings]
GameDefaultMap=/Game/Maps/MainMenu
```

## Daily Task
1. Plan your project architecture
2. Create folder structure
3. Define base classes
4. Write coding standards doc

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
